---
jupyter:
  jupytext:
    formats: ipynb,hs:light,md
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

# Chapter 8: Declaring Types and Classes 


## Chapter Notes 


This chapter covers three different ways of declaring types:

- `type`
    + Used for type synonyms, e.g. `type String = [Char]`
    + Mainly used for purposes of annotation, i.e. making intention explicit, or simplification
    + Cannot be recursive
    + Can be parameterised, e.g. `type Pair a = (a,a)` or `type Assoc k v = [(k,v)]`
- `data`
    + Used for declaring new types, e.g. `data List a = Nil | Cons a (List a)`
    + Can have multiple type constructors (c.f. enums in Scala 3)
    + Constructor functions cannot be reduced, e.g. `Cons 1 (Cons 2 (Cons 3 Nil))` is fully evaluated
    + Can be recursive
- `newtype`
    + Used when there is one type constructor with a simple parameter, e.g. `newtype Nat = N Int`
    + From the compiler's perspective, types created with newtype are different from the underlying type, e.g. Nat and Int are different. As a result, Nat is not instance of the classes of which Int is a member (e.g. Eq, Ord, Show, etc.)
    + At compilation time, newtype constructors are removed and therefore have no associated cost


## 8.6 Tautology Checker 


A tautology checker will assess whether logical statements are a tautology, i.e. **always true**.

```haskell
data Prop = Const Bool
            | Var Char
            | Not Prop
            | And Prop Prop
            | Imply Prop Prop
```

Create the set of 4 propositions

```haskell
p1 :: Prop
p1 = And (Var 'A') (Not (Var 'A'))

p2 :: Prop
p2 = Imply (And (Var 'A') (Var 'B')) (Var 'A')

p3 :: Prop
p3 = Imply (Var 'A') (And (Var 'A') (Var 'B'))

p4 :: Prop
p4 = Imply (And (Var 'A') (Imply (Var 'A') (Var 'B'))) (Var 'B')
```

We wish to assess whether these propositions are tautologies. To do this, we need to:

- identify the variables involved
- consider the space of possible substituions for those variables
- determine if the all possible substituions, the proposition is true


Given a proposition, define a function to extract the set of variables.

```haskell
vars :: Prop -> [Char]
vars (Const _) = []
vars (Var x) = [x]
vars (Not p) = vars p
vars (And p q) = vars p ++ vars q
vars (Imply p q) = vars p ++ vars q
```

```haskell
vars p4
```

Define another function to remove dupes

```haskell
rmdupes :: Eq a => [a] -> [a]
rmdupes [] = []
rmdupes (x:xs) = x : (rmdupes . filter (/=x)) xs
```

```haskell
rmdupes $ vars p4
```

```haskell
rmdupes $ concatMap (\x -> [x | i <- [1..3]]) ['a'..'f']
```

Now create a function to determine the space of possible substituions.

```haskell
bools :: Int -> [[Bool]]
bools 0 = [[]]
bools n = [ False : x | x <- bs ] ++ [ True : x | x <- bs ]
          where bs = bools (n-1)
```

Define an association type, for mapping a variable to a boolean value.

```haskell
type Assoc k v = [(k,v)]
```

```haskell
find :: Eq k => k -> Assoc k v -> v
find k kvs = head [ v | (k', v) <- kvs, k' == k ]
```

```haskell
type Subst = Assoc Char Bool
```

```haskell
substs :: Prop -> [Subst]
substs p = map (zip vs) bss
           where vs = (rmdupes . vars) p
                 bss = bools (length vs)
```

Define a function for evaluating a proposition, given a set of substitutions.

```haskell
eval :: Subst -> Prop -> Bool
eval _ (Const b) = b
eval s (Var x) = find x s
eval s (Not p) = not (eval s p)
eval s (And p q) = eval s p && eval s q
eval s (Imply p q) = eval s p <= eval s q
```

Now define a function for checking for a tautology.

```haskell
isTaut :: Prop -> Bool
isTaut p = all e s
           where e = (`eval` p)
                 s = substs p
```

```haskell
isTaut p1
```

```haskell
isTaut p2
```

```haskell
isTaut p3
```

```haskell
isTaut p4
```

## Questions 


#### Prerequisites 


Define a search tree.

```haskell
data Tree a = Leaf a | Node (Tree a) a (Tree a)
```

Define a natural number

```haskell
data Nat = Zero | Succ Nat deriving (Show)
```

#### Q1 


Define the add function

```haskell
add :: Nat -> Nat -> Nat
add Zero n = n
add (Succ m) n = add m (Succ n)
```

```haskell
add (Succ Zero) (Succ (Succ Zero))
```

Define the multiplication function

```haskell
mult :: Nat -> Nat -> Nat
mult Zero n = Zero
mult n Zero = Zero
mult (Succ Zero) n = n
mult (Succ m) n = mult m (add n n)
```

```haskell
mult Zero (Succ Zero)
```

```haskell
mult (Succ (Succ Zero)) (Succ (Succ (Succ Zero)))
```

#### Q2 

```haskell
occurs :: Ord a => a -> Tree a -> Bool
occurs x (Leaf y) = x == y
occurs x (Node l y r) = case compare x y of
                            LT -> occurs x l
                            EQ -> True
                            GT -> occurs x r
```

This is more efficient that the version below, since only one comparison is made.

```haskell
occurs' :: Ord a => a -> Tree a -> Bool
occurs' x (Leaf y) = x == y
occurs' x (Node l y r) | x == y = True
                      | x < y  = occurs' x l
                      | otherwise = occurs' x r
```

#### Q3 

```haskell
data Tr a = Le a | Nd (Tr a) (Tr a) deriving (Show)
```

```haskell
numLeaves :: Tr a -> Int
numLeaves (Le a) = 1
numLeaves (Nd l r) = numLeaves l + numLeaves r
```

```haskell
balanced :: Tr a -> Bool
balanced (Le a) = True
balanced (Nd l r) = (abs (numLeaves l - numLeaves r) < 2) && balanced l && balanced r  
```

```haskell
balanced (Le 5)
```

```haskell
balanced (Nd (Le 5) (Le 6))
```

```haskell
balanced (Nd (Le 1) (Nd (Le 2) (Nd (Le 3) (Le 4))))
```

#### Q4 


Define a function to split a list into two halves.

```haskell
splitList :: [a] -> ([a], [a])
splitList xs = let n = length xs `div` 2
                in splitAt n xs
```

```haskell
splitList [1..11]
```

```haskell
splitList[1]
```

Define another function to generate a balanced tree from a list.

```haskell
balance :: [a] -> Tr a
balance [x] = Le x
balance xs = let (f,s) = splitList xs
             in Nd (balance f) (balance s)
```

```haskell
balance [1..6]
```

Check that a set of trees generated in this way are balanced.

```haskell
all balanced [ balance [1..n] | n <- [2..20]]
```
