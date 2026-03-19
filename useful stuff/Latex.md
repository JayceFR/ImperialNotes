Pronounced laytech 

# Basic syntax
### Comments
`%` denotes a comment 
```latex
% this is a comment %
```
### Commands
`\emph` denotes the command emph (makes stuff italics). 
Commands start with the 
so 
```latex
\emph{"Hello World, I am the goat"}
```
would produce the string inside the {} italiciced (yh its a word). 

`\\` stands for new line 
We can use `{}` to split up tokens 

`\begin{env} .... \end{env}` Basically creates an environment. And anything between them belongs to one environment. 

#### Maths
Any maths equation must be between `$` 
so 
```latex
$x^2 + y$
```
FUN maths commands 
`\mathcal{O}` -> renders in calligraphy style. 
`\sigma` -> for sigma sign 
`\log | \frac{n}{m} -> n/m | \sin`
`\, | \: | \;` -> Used to increase spacing in equations. (smallest to largest)

#### Changing Text style 

`\emph{text}` -> makes it italics
`\textbf{text}` -> makes it bold 
`\texttt{text}` -> makes it look teletype
`\underline{text}` -> can be used to underline text 
`\textsc{text}` -> small caps

# Document structure

Preamble -> basically just copy paste before every document 
```latex
\documentclass[a4paper, 11pt]{article}

\usepackage[margin=1in]{geometry}
\usepackage[tt=false]{libertine}
\usepackage[libertine]{newtxmath}
\usepackage[scaled=0.8, lining]{FiraMono}

\title{My Fancy Document}
\author{Jayce Jefferson Felix Robin}

```

The actual document 
```latex
\begin{document}
\maketitle
Hello this is me \emph{GOAT} document
\end{document}
```

There are even sections
```latex
\section{big section}
\subsection{one sub section}
\subsubsection{one sub sub section}
\subsubsection{one more sub sub section}
```

*Note:* Each section would be given its own number 

There are even appendixes 
```latex
\appendix 
\section{First appendix}
\subsection{...}
```
Appendices would have their name prefixed with A. {number}. 

## Internal Links
The numbers while referencing to a section can be figured out by the latex compiler itself. wooo.
we just need to use `\label{name}` and `\ref{name}` command 
```latex
\section{A section}\label{sec:a-section}
Contents .. referenced in Section~\ref{sec:a-section}.

\section{Another section}\label{sec:another-section}
Referenced to Section~\ref{sec:a-section}.
```
*Note:* the `~` this basically says the number of the section should not fly off to the next line. It should stay in the same line... 

*Always use `~` when working with numbers, it ensures it is styled properly.*

The numbers are clickable links. 

To make the link more appealing follow the following slide 
![[Pasted image 20250604232725.png]]

For better referencing use the pacakage *cleveref* 
![[Pasted image 20250604232940.png]]
