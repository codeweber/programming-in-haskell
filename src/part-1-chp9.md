---
jupyter:
  jupytext:
    formats: ipynb,md,hs:light
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.13.6
  kernelspec:
    display_name: Haskell
    language: haskell
    name: haskell
---

# Chapter 9 - The Countdown Problem 


## Aritmetic Ops


Create a data type for representing arithmetic operations. 

```haskell
data Op = Add | Sub | Mul | Div
```

```haskell
instance Show Op where
    show Add = "+"
    show Sub = "-"
    show Mul = "*"
    show Div = "/"
```

Create functions for applying an operator to values and determine if the application is valid.

```haskell
-- Below is the initial definition
--valid :: Op -> Int -> Int -> Bool
--valid Add _ _ = True
--valid Sub x y = x > y
--valid Mul _ _ = True
--valid Div x y = x `mod` y == 0

--Following added to improve performance
valid :: Op -> Int -> Int -> Bool
valid Add x y = x <= y
valid Sub x y = x > y
valid Mul x y = x /= 1 && y /= 1 && x <= y 
valid Div x y = y /= 1 && x `mod` y == 0
```

```haskell
apply :: Op -> Int -> Int -> Int
apply Add x y = x + y
apply Sub x y = x - y
apply Mul x y = x * y
apply Div x y = x `div` y
```

## Numeric expressions 


Create a data type for storing espressions.

```haskell
data Expr = Val Int | App Op Expr Expr
```

Note that this structure is isomorphic to a binary tree, which stores values in it's leaves and operators at the nodes.

```haskell
instance Show Expr where
    show (Val n) = show n
    show (App o l r) = brak l ++ show o ++ brak r
                              where brak (Val n) = show n
                                    brak e = "(" ++ show e ++ ")"
```

Define a function for evaluating an expression

```haskell
eval :: Expr -> [Int]
eval (Val n) = [n | n > 0]
eval (App o l r) = [apply o x y | x <- eval l, y <- eval r, valid o x y]
```

Check examples

```haskell
eval (App Add (Val 2) (Val 3))
```

```haskell
eval (App Sub (Val 2) (Val 3))
```

Note that eval returns either a singleton or empty list.


## Combinatorics 


Given a set of N numbers, we want a function to provides all possible subsets.

```haskell
subsets :: [a] -> [[a]]
subsets [] = [[]]
subsets (x:xs) = yss ++ [x:ys | ys <- yss]
                 where yss = subsets xs

```

```haskell
subsets [1..3]
```

```haskell
subsets [1..4]
```

We now want a function to provide all possible permutations of a list.

```haskell
interleave :: a -> [a] -> [[a]]
interleave x [] = [[x]]
interleave x (y:ys) = (x : y : ys) : map (y:) (interleave x ys) 
```

```haskell
interleave 1 [2..3]
```

```haskell
perms :: [a] -> [[a]]
perms [] = [[]]
perms (x:xs) = concatMap (interleave x) (perms xs)
```

We can now build a function to generate all possible choices of numbers

```haskell
choices :: [a] -> [[a]]
choices = concatMap perms . subsets
```

```haskell
choices [1..3]
```

```haskell
sum $ [ product [1..3] / product [1..(3-n)] | n <- [0..3] ]
```

```haskell
length $ choices [1..3]
```

## Expressions 


Given a sequence of values, we want to consider all possible 'expression trees'.

Each expression tree should maintain the order of the original sequence.

```haskell
partition :: [a] -> [([a],[a])]
partition [] = []
partition [x] = []
partition (x:xs) = ([x],xs) : [ (x:l, r) | (l,r) <- partition xs ]
```

```haskell
partition [1..4]
```

```haskell
ops :: [Op]
ops = [Add, Sub, Mul, Div]
```

```haskell
expressions :: [Int] -> [Expr]
expressions [] = []
expressions [n] = [Val n]
expressions ns = [ App op eL eR | (l, r) <- partition ns, eL <- expressions l, eR <- expressions r, op <- ops]
```

## Basic checks 


Calculate the number of possible expressions given 6 numbers (as in Countdown). Should be 33665406.

```haskell
sum $ map (length . expressions) (choices [1,3,7,10,25,50])
```

How many of these can actually be calculated?

```haskell
sum $ map (length . filter (not . null . eval) . expressions) (choices [1,3,7,10,25,50])
```

Note that this is different from the number in Graham's book (since a different input list is given), but gives the same outcome; only ~15% of expressions can actually be evaluated.

```haskell
5341067.0 / 33665406.0
```

## Performance Improvements 


Introduce a new type to store expressions and the result of their evalutation.

Replace expressions with results, which calculates the results of an expression as they are built.

```haskell
type Result = (Expr, Int)
```

```haskell
results :: [Int] -> [Result]
results [] = []
results [n] = [(Val n, n) | n > 0]
results ns = [ (App op eL eR, apply op vL vR) | (l, r) <- partition ns, 
                                                (eL,vL) <- results l, 
                                                (eR, vR) <- results r, 
                                                op <- ops,
                                                valid op vL vR
                                                ]
```

Note that this now evaluates the following set of results

```haskell
sum $ map (length . results) (choices [1,3,7,10,25,50])
```

Define a function to find solutions

```haskell
solutions :: [Int] -> Int -> [Expr]
solutions ns n = [e | ns' <- choices ns, (e, r) <- results ns', r == n]
```

```haskell
length $ solutions [1,3,7,10,25,50] 765
```

```haskell
solutions [1,3,7,10,25,50] 765
```
