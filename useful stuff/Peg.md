# PEG (Parser Expression Grammars)
Classic compilers courses usually teach subsets of Context-Free Grammars (CFG) and their
associated parsing algorithms: `LL(k)` and `LR(k)`, for instance.
These algorithms have to operator on token streams, and have their
own quirks and advantages.

|  | `LL(k)` | `LR(k)` |
|------------|---------|---|
| approach? | top-down | bottom-up |
| handles left-recursion? | no | prefers it |
| good errors? | can be good | reasonably weak |
| allows for ambiguity | up to `k` tokens | up to `k` tokens |
| complexity? | `O(n)` (but this assumes perfect `O(1)` choices) | `O(n)` |

In general, any CFGs can be parsed in `O(n^3)` and can be fully ambiguous with multiple parses using
CYK. `LL(k)` and `LR(k)` demand that any ambiguities can be resolved by looking ahead at the next
`k` tokens. `LL(1)` and `LR(1)` grammars are fully unambiguous. Generally, programming languages
should aspire to have unambiguous syntax to ensure fast runtime and clear syntax. Lookahead can
still degrade performance!

An alternative to CFGs is PEG, which is unambiguous by construction. This is a bit of a subtle point.
When you have ambiguity in the `LL` or `LR` sense, you say that by looking ahead you can take the
"correct" path. In other words, there shouldn't be two possible resulting parse trees. If you can't
lookahead for the answer and there are multiple possible parse trees, this is an ambiguous grammar.
PEG avoids this by giving a concrete semantics that will disambiguate a grammar so that it always
returns a single parse-tree, but there can still be local ambiguities in the grammar that may need
backtracking to resolve after `k` tokens of lookahead. As it happens, PEG is capable of parsing any
`LR(k)` grammar; it is an open question whether PEG can do all of CFG, and it is actually capable of
dealing with some context-sensitive languages. A PEG that doesn't need to backtrack (i.e. LL(1)) will
run in `O(n)` time, but it can run in `O(2^n)` instead if it has to backtrack for every decision. That
said, the *Packrat* algorithm can parse any PEG in `O(n)` with `O(n^3)` space complexity (in the
length of the input). Unlike `LL` and `LR`, PEG can parse in a tokenless style!

## PEG Definition
A PEG is described by a collection of productions of the form `v <- e`, with `v` an identifier and
`e` an expression which is an inductive definition of the following:

* a literal `"c"` or `'c'`
* a variable `v`
* epsilon (the empty parser, which always succeeds)
* sequencing `e1 e2` of two expressions
* left-biased ordered choice `e1 / e2` of two expressions
* negative lookahead `!e` which succeeds when `e` fails and vice-versa
* grouping `(e)`

In addition to this core set, there is a bunch of syntactic sugar:

* any character `.`
* character class `[a-z]`
* optional `e?`, which is equivalent to `e / epsilon`
* zero-or-more `e*` which is equivalent to `loop` where `loop <- e loop / epsilon`
* one-or-more `e+` which is equivalent to `e e*`
* positive lookahead `&e` which is equivalent to `!!e`

Of these, the most important is `/`, which characterises PEG's approach to ambiguity
resolution: when parsing `e1 / e2`, if `e1` succeeds, then `e2` is never attempted. If `e1` fails,
then we progress to `e2`. This resolves ambiguities by always picking the parse-tree resulting from
the left-most choices. However, it does have some implications we'll discuss later.

One of the joys of PEG is that it is remarkably easy to ascribe a semantics to. To demonstrate this,
here is a Haskell snippet that implements a simple PEG matcher (not a parser) that returns a `Bool`.

```hs
import Data.Set (Set)
import qualified Data.Set as Set
import Data.Map (Map)
import qualified Data.Map as Map
import Data.List
import Data.Maybe
import Control.Applicative ((<|>))

data Expr = Lit String | Var String | Eps
          | Seq Expr Expr | Alt Expr Expr | Neg Expr
          -- it's easiest to include these too, for usability
          | Rep0 Expr | Cls (Set Char)

rep1 :: Expr -> Expr
rep1 e = Seq e (Rep0 e)

opt :: Expr -> Expr
opt e = Alt e Eps

anyChr :: Expr
anyChr = Cls (Set.fromList ['\0' .. '\255']) -- let's not allocate a set of several MB

pos :: Expr -> Expr
pos e = Neg (Neg e)

eof :: Expr
eof = Neg anyChr

type PEG = Map String Expr

matches :: String -> PEG -> String -> Bool
matches start peg input = isJust (go (Var start) input) where
  go :: Expr -> String -> Maybe String
  go (Lit lit) input
    | isPrefixOf lit input = Just (drop (length lit) input)
    | otherwise            = Nothing
  go (Var v) input = go (peg Map.! v) input
  go Eps input = Just input
  -- if e1 matches input, then give the remainder to e2
  go (Seq e1 e2) input = do
    input' <- go e1 input
    go e2 input'
  {-
  This already gives us the left-biased behaviour we need, as it happens:

  Just x <|> _ = Just x
  Nothing <|> mx = mx
  -}
  go (Alt e1 e2) input = go e1 input <|> go e2 input
  go (Neg e1) input = case go e1 input of
    Just _ -> Nothing
    Nothing -> Just input
  go (Cls cs) (c:input) | Set.member c cs = Just input
  go (Cls _) _                            = Nothing
  -- do the grammar transformation (really, shouldn't be recursively
  -- defined for proper semantics, but oh well)
  go (Rep0 e) input = let loop = Alt (Seq e loop) Eps in go loop input

p :: PEG
p = Map.fromList [
    -- p <- expr !.
    ("p", Seq (Var "expr") eof),
    -- expr <- term ("+" term)*
    ("expr", Seq (Var "term") (Rep0 (Seq (Lit "+") (Var "term")))),
    -- term <- atom ("*" atom)*
    ("term", Seq (Var "atom") (Rep0 (Seq (Lit "*") (Var "atom")))),
    -- atom <- [0-9]* / "(" expr ")"
    ("atom", Alt (rep1 (Cls (Set.fromList ['0'..'9'])))
                 (Seq (Lit "(") (Seq (Var "expr") (Lit ")"))))
    ]
```

This will handle any PEG, in possibly exponential time. As it happens, a Haskell
program can serve as a denotation for the semantics of a language, so this is indeed
a big-step semantics of sorts for how PEG behaves (more precisely a denotational, because of the
self-recursive nature of `Rep0`). Indeed, `matches "p" p "1+4*8"` returns `True`. Have a play around
with this if you wish.

### Practical Implications of PEGs Semantics

* PEG exhibits "greediness". This means that it will always consume as much as can: it is most useful
  to therefore ensure that longer "ambiguous" branches are kept further to left in alternatives:

  ```peg
  stmt <- "if" expr "then" stmt "else" stmt
        / "if" expr "then" stmt
  ```

  is a non-ambiguous implementation of "else-less ifs". It will always favour gobbling an `else`,
  which leads to a right-association for nested ifs. If, however, you flipped the order of these
  sub-expressions, it would not be possible to ever parse an `else`, because the first branch would
  succeed and will never backtrack to "change its mind".

* Consider a rule like `expr <- expr "+" term / term`. If you follow the evaluation semantics for this
  you'll see a trace like:

  ```hs
  go (Var "expr") input
    =
  go (Seq (Var "expr") ..) input
    =
  go (Var "expr") input
  ```

  It should be clear that this is never going to resolve: `input` hasn't gotten any smaller, so this
  will infinite loop. Similarly, `e*` where `e` is *nullable* (i.e. can succeed without consuming
  input) will also infinite loop. To see why, let's evaluate `Rep0 (Var "e")`, where we assume that
  at somepoint `go (Var "e") input = Just input`:

  ```hs
  go (Rep0 (Var "e")) input =
    let loop = Alt (Seq (Var "e") loop) Eps
    in go loop input
    =
  go loop input
    =
  go (Alt (Seq (Var "e") loop) Eps) input
    =
  go (Seq (Var "e") loop) input <|> go Eps input
    =
  do input' <- go (Var "e") input
     go loop input'
  <|> go Eps input
    = -- by assumption above
  do input' <- Just input
     go loop input'
  <|> go Eps input
    = -- simplify
  go loop input <|> go Eps input
    = -- evaluate <|>'s left argument
  go loop input
  ```

  At this point, we can see that we are again, back to the top. In fact, this is what is known as an
  instance of *hidden* left-recursion, where recursing to `loop` is "hidden" behind the nullable `e`.
  If you're using `parsley`, the Scala 3 compiler can reliably find direct left-recursion for you as
  part of Scala's own semantics for (lazy) variable declarations, but for hidden left-recursion or
  non-productive iteration, you need different analysis tools.

* *Local* ambiguity in the grammar can be expensive (it is what can decay the complexity of the
  parser down to `O(2^n)`). This is best avoided in PEG-land by *left-factoring*. This is a
  transformation of the following form: `e e1 / e e2 ==> e (e1 / e2)`. This does not change
  the semantics of that expression, but does mean it does not need to backtrack, assuming `e1` and
  `e2` are totally disjoint. In the `stmt` grammar above, notice that `e = "if" expr "then" stmt`,
  `e1 = "else" stmt` and `e2 = epsilon` (why? `e = e epsilon` always). Using this, we can apply our
  transformation to the grammar:

  ```peg
  stmt <- "if" expr "then" stmt ("else" stmt / epsilon)
  ```

  or, equivalently, we can use the `?` sugar:

  ```peg
  stmt <- "if" expr "then" stmt ("else" stmt)?
  ```

  Lovely.


* You might want to consider whether the following rule `e1 e / e2 e ==> (e1 / e2) e` is also legal.
  As it happens, this does not preserve meaning! Try to come up with a counter-example to this (hint:
  you might wish to rely on the greediness of `*`...)

#### Resolving Left-Recursion
Since the semantics of PEG doesn't account for left-recursion, we do need to be careful to eliminate
it. One classic way is the following transformation:

```peg
E <- E OP T / T
==>
E <- T (OP T)*
```

or, more generally for postfix application of an operator:

```peg
E <- E OP / T
==>
E <- T OP*
```

(You'll notice that `OP` in the second definition is the same as `OP T` in the first). Let's unwind
this a bit and see what this transformation has really done:

```peg
E <- E OP T / T
==>
E <- T (OP T)*
===
E <- T loop
loop <- OP T loop / epsilon
=== { notice that we can replace `T loop` by `E` }
E <- T loop
loop <- OP E / epsilon
==> { inline `loop` }
E <- T (OP E / epsilon)
==> { apply left-factoring in reverse }
E <- T OP E / T
```

As it happens, this transformation has just right-associated the expressions! It is up to the
parser writer to then ensure that a generated AST from this is left-associated. In parser combinator
world, this transformation is known as a `chain.left1`, with the more general transformation being
`chain.postfix`. The implementations of these combinators will handle the re-association of the parse
often by recursion-with-accumulator, but this is a mere implementation detail.

### Context-Sensitivity
As I mentioned above, CFGs cannot (by definition) handle context-sensitivity. PEG can by using its
lookahead capacity (which is any arbitrary expression, making it incredibly powerful); however, it is
unknown what exactly the sub-class of context-sensitive grammars PEG can handle is.

One classic example of a context-sensitive grammar is the `n` `a`s, followed by `n` `b`s, followed by
`n` `c`s. Remove the `c`s and you can write a CFG for this:

```bnf
<as and bs> ::= 'a' <as and bs> 'b' | epsilon
```

This relies on the natural recursion of the rule to ensure the number of `a`s and `b`s match up.
Indeed,

```peg
AsAndBs <- 'a' AsAndBs 'b' / epsilon
```

Also works. But if you add the constraint on `c`s, you'll find that as you unwind the stack to
find `b`s, you're gently forgetting how many `a`s you had in total. Poor CFGs. With PEG though,
we can cheekily use lookahead to rollback our consumed `a`s and start again (for `n >= 1`):

```peg
// need to check that the matching as and bs are followed by a 'c' otherwise it would
// allow "aaabccc" to parse "" for the AsAndBs part!
AsAndBsAndCs <- &(AsAndBs 'c') AsAndCs !.
AsAndBs      <- 'a' AsAndBs 'b' / epsilon
AsAndCs      <- 'a' AsAndCs 'c' / 'b'*
```

Let's evaluate this on the smallest non-trivial example: `"abc"` using our evaluation semantics

```hs
g :: PEG
g = Map.fromList [
    ("as and bs and cs", Seq (pos (Seq (Var "as and bs") (Lit "c"))) (Seq (Var "as and cs") eof)),
    ("as and bs", opt (Seq (Lit "a") (Seq (Var "as and bs") (Lit "b")))),
    ("as and cs", Alt (Seq (Lit "a") (Seq (Var "as and cs") (Lit "c")))
                      (Rep0 (Lit "b")))
  ]

-- corollary:
-- go (pos e) input
--   =
-- go (Neg (Neg e)) input
--   =
-- case go (Neg e) input of
--   Just _ -> Nothing
--   Nothing -> Just input
--   =
-- case (case go e input of Just _ -> Nothing; Nothing -> Just input) of
--   Just _ -> Nothing
--   Nothing -> Just input
--   = {flatten}
-- case go e input of
--   Just _ -> Just input
--   Nothing -> Nothing

-- corollary
-- go (opt e) input
--   =
-- go (Alt e Eps) input
--   =
-- go e input <|> go eps input
--   =
-- go e input <|> Just input

go (Var "as and bs and cs") "abc"
  =
go (Seq (pos (Seq (Var "as and bs") (Lit "c"))) (Seq (Var "as and cs") eof)) "abc"
  =
do input' <- go (pos (Seq (Var "as and bs") (Lit "c"))) "abc"
   go (Seq (Var "as and cs") eof) input'
  = -- by above corollary and simplification (notice the same input for both `go`s!)
do go (Seq (Var "as and bs") (Lit "c")) "abc"
   go (Seq (Var "as and cs") eof) "abc"
  = -- evaluate go and flatten the `do`
do input' <- go (Var "as and bs") "abc"
   go (Lit "c") input'
   go (Seq (Var "as and cs") eof) "abc"
  =
do input' <- go (opt (Seq (Lit "a") (Seq (Var "as and bs") (Lit "b")))) "abc"
   go (Lit "c") input'
   go (Seq (Var "as and cs") eof) "abc"
  = -- above corollary
do input' <- go (Seq (Lit "a") (Seq (Var "as and bs") (Lit "b"))) "abc" <|> Just "abc"
   go (Lit "c") input'
   go (Seq (Var "as and cs") eof) "abc"
  = -- go of Seq + go of Lit "a" on "abc"
do input' <- go (Seq (Var "as and bs") (Lit "b")) "bc" <|> Just "abc"
   go (Lit "c") input'
   go (Seq (Var "as and cs") eof) "abc"
  =
do input' <- do input' <- go (Var "as and bs") "bc"
                go (Lit "b") input'
            <|> Just "abc"
   go (Lit "c") input'
   go (Seq (Var "as and cs") eof) "abc"
  =
do input' <- do input' <- go (opt (Seq (Lit "a") (Seq (Var "as and bs") (Lit "b")))) "bc"
                go (Lit "b") input'
            <|> Just "abc"
   go (Lit "c") input'
   go (Seq (Var "as and cs") eof) "abc"
  = -- by corrollary
do input' <- do input' <- go (Seq (Lit "a") (Seq (Var "as and bs") (Lit "b"))) "bc" <|> Just "bc"
                go (Lit "b") input'
            <|> Just "abc"
   go (Lit "c") input'
   go (Seq (Var "as and cs") eof) "abc"
  = -- by seq and Lit a fails
do input' <- do input' <- Nothing <|> Just "bc"
                go (Lit "b") input'
            <|> Just "abc"
   go (Lit "c") input'
   go (Seq (Var "as and cs") eof) "abc"
  =
do input' <- do input' <- Just "bc"
                go (Lit "b") input'
            <|> Just "abc"
   go (Lit "c") input'
   go (Seq (Var "as and cs") eof) "abc"
  =
do input' <- do go (Lit "b") "bc"
            <|> Just "abc"
   go (Lit "c") input'
   go (Seq (Var "as and cs") eof) "abc"
  =
do input' <- Just "c" <|> Just "abc"
   go (Lit "c") input'
   go (Seq (Var "as and cs") eof) "abc"
  =
do input' <- Just "c"
   go (Lit "c") input'
   go (Seq (Var "as and cs") eof) "abc"
  =
do go (Lit "c") "c"
   go (Seq (Var "as and cs") eof) "abc"
  =
do Just ""
   go (Seq (Var "as and cs") eof) "abc"
  = -- phew! notice that we are now just checking matching as and cs with some bs inbetween
    -- by this point we know the as and bs matched
    -- you can do the rest of the evaluation yourself :)
go (Seq (Var "as and cs") eof) "abc"
```

Hopefully following that gives you some intuition of how that PEG accompished its task.

How is lookahead practically useful to the PEG writer? Well, it can be used to ensure that identifiers
and keywords, say, don't overlap:

```peg
keyword <- ("if" / "while" / "else")
           !idLetter // keywords cannot be followed by legal identifier bits!
idLetter <- [a-zA-z0-9]
identifier <- !keyword // identifiers cannot be keywords
              [a-zA-Z] // they don't start with numbers
              idLetter*
```

This is an example of "character-driven" parsing, where `identifier` forms a PEG expression for
doing what a lexer would have done to define an `IDENTIFIER` token. We don't need tokens, we just
make small PEG rules to do what we want.

## Practical Implications of PEG
PEG is a nice grammar model because it allows us to unambiguously describe grammars with no
guess work and a concrete semantics we can use to reason about any PEG (no matter the underlying
algorithm that will be used, which only affects the complexity, really).

This doesn't mean it doesn't have its own share of counter-intuitive grammars (we don't talk
about palindromes...), but we can always reason about them in a principled way. In the real world,
Python's parser was switched to PEG, actually!

The vast majority of *parser combinator* libraries use a PEG-based semantics (which some minor
modifications). The Scala library `parsley`, and its Haskell sibling `gigaparsec` are no exception.
While these libraries also force you to care about AST construction instead of the pure language
definition of PEG, they mostly obey the above rules. In particular, you should aim to left-factor
to reduce local ambiguity that would result in backtracking; be mindful of the greedy behaviour;
avoid left-recursion; and go scannerless.

While there are probably parser generators that target PEG, the big advantage of the combinators
is that they can treat these PEG expressions as first-class values, allowing for nice composition
and abstraction of transformations. Above I mentioned `chain.left1`: this implements the refactoring
to remove left-recursion, and does the work to sort out the AST construction. The fact that you
could define this *yourself* is part of the power and joy that combinators bring.

## What else?
There is plenty you could go and look at:

* Regular expression and their connection to automata theory: NFAs and DFAs etc
* How to prove a language is not regular
* How `LR` parsing works
* How Packrat works