elem  :  a -> t a -> Bool 

words : String -> [String]
```haskell
Data.Char (isSpace) :  Char -> Bool
	Basically just says if the curr character is a whiteSpace or one of these (' ', '\t', '\n')

Data.List ((`\\`)) : [a] -> [a] -> [a]
	Subtracts the second list from the first list 
	for example : [1,2,3]  `\\` [2,3,4]  = [1]

splitat :: Int -> [a] -> ([a], [a])
	Splits the list at that position 

zip :: [a] -> [b] -> [(a,b)]

zipWith :: (a -> b -> c)  -> [a] -> [b] -> [c]

Data.Maybe (mapMaybe) :: (a -> Maybe b) -> [a] -> [b]

the fold rule 

foldr f k . map g = foldr (f. g) k 

scanl 

foldl 

Data.List (foldl' )

concatMap 

recip 
	basically just for reciprocal 

Data.List (intercalate)
	Interesting function where you could combine a list of list with a "," or something 

partition :: (a -> Bool) -> [a] -> ([a], [a])

```
