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

# Programming in Haskell - Part 1 - Exercises


## Chapter 2 


#### Q2. 

```haskell
2^3*4 == (2^3)*4
```

```haskell
2*3+4*5 == (2*3)+(4*5)
```

```haskell
2+3*4^5 == 2 + (3 * (4^5))
```

#### Q3.

```haskell
n = a `div` length xs
    where
        a = 10
        xs = [1,2,3,4,5]
```

```haskell
n
```

Errors:
- N cannot be used for a variable; only concrete types can begin with a capital letter
- ''' cannot be used, but rather '`'
- xs and a must be aligned in the same column


#### Q4. 

```haskell
last [1,2,3,4,5]
```

Here a possible alternative definition

```haskell
last' :: [a] -> a
last' xs = xs !! (length xs - 1)
```

```haskell
last' [1..5]
```

Here another

```haskell
last'' :: [a] -> a
last''  = head . reverse
```

```haskell
last'' [1..10]
```

#### Q5.

```haskell
init [1..5]
```

```haskell
init' :: [a] -> [a]
init' xs = take (length xs - 1) xs
```

```haskell
init' [1..10]
```

```haskell
init'' :: [a] -> [a]
init'' = reverse . drop 1 . reverse
```

```haskell
init'' [1..10]
```

## Chapter 3 


#### Q1

- `['a', 'b', 'c'] :: [Char]`
- `('a', 'b', 'c') :: (Char, Char, Char)`
- `[(False, '0'), (True, '1')] :: [(Bool, Char)]`
- `([False, True], ['0', '1']) :: ([Bool], [Char])`
- `[tail, init, reverse] :: [[a] -> [a]]`

```haskell
:t ['a', 'b']
```

```haskell
:t ('a', 'b', 'c')
```

```haskell
:t [(False, '0')]
```

```haskell
:t ([False], ['0'])
```

```haskell
:t [tail, init, reverse]
```

#### Q2

```haskell
bools :: [Bool]
bools = map (>4) [1..10]
```

```haskell
bools
```

```haskell
nums :: [[Int]]
nums = map (\n -> [0..n]) [0..5]
```

```haskell
nums
```

```haskell
add :: Int -> Int -> Int -> Int
add x y z = x + y + z
```

```haskell
add 4 5 6
```

```haskell
copy :: a -> (a,a)
copy x = (x,x)
```

```haskell
copy 10
```

```haskell
copy 'a'
```

```haskell
copy [0..5]
```

```haskell
apply :: (a -> b) -> a -> b
apply f = f
```

```haskell
apply show 5
```

#### Q3 

```haskell
second :: [a] -> a
second = head . tail
```

```haskell
second [1..5]
```

```haskell
swap :: (a, b) -> (b, a)
swap (x,y) = (y,x)
```

```haskell
swap (0,True)
```

```haskell
pair :: a -> b -> (a,b)
pair x y = (x,y)
```

```haskell
pair True [0..3]
```

```haskell
double :: Num a => a -> a
double x = x * 2
```

```haskell
double 3
```

```haskell
double 9.0
```

```haskell
palindrome :: Eq a => [a] -> Bool
palindrome xs = reverse xs == xs
```

```haskell
palindrome ['a'..'z']
```

```haskell
palindrome (['a'..'z'] ++ reverse ['a'..'z'])
```

```haskell
twice :: (a -> a) -> a -> a
twice f = f . f
```

```haskell
twice (+1) 3
```

## Chapter 4


#### Q1

```haskell
:t splitAt
```

```haskell
halve :: [a] -> ([a], [a])
halve xs = splitAt (length xs `div` 2) xs
```

```haskell
halve [1..10]
```

```haskell
halve [1..9]
```

#### Q2

```haskell
third :: [a] -> a
third = head . tail . tail
```

```haskell
third [1..5]
```

```haskell
third' :: [a] -> a
third' xs = xs !! 2
```

```haskell
third' [1..5]
```

```haskell
third'' :: [a] -> a
third'' (_:_:x:_) = x
```

```haskell
third'' [1..5]
```

```haskell
third'' [1..3]
```

```haskell
third'' [1..2]
```

#### Q4

```haskell
safetail :: [a] -> [a]
safetail xs = if null xs then [] else tail xs
```

```haskell
safetail [1..5]
```

```haskell
safetail []
```

```haskell
safetail' :: [a] -> [a]
safetail' xs | null xs = []
             | otherwise = tail xs
```

```haskell
safetail' [1..5]
```

```haskell
safetail' []
```

```haskell
safetail'' :: [a] -> [a]
safetail'' [] = []
safetail'' (_:xs) = xs
```

```haskell
safetail'' [1..5]
```

```haskell
safetail'' []
```

#### Q4

```haskell
testIn = [(True, True), (True, False), (False, True), (False, False)]
testExpected = [True, True, True, False]
```

```haskell
(||) :: Bool -> Bool -> Bool
True || True = True
True || False = True
False || True = True
False || False = False
```

```haskell
testExpected == map (uncurry (||)) testIn
```

```haskell
(|||) :: Bool -> Bool -> Bool
True ||| _ = True
False ||| b = b
```

```haskell
testExpected == map (uncurry (|||)) testIn
```

```haskell
(||||) :: Bool -> Bool -> Bool
False |||| False = False
_ |||| _ = True
```

```haskell
testExpected == map (uncurry (||||)) testIn
```

```haskell
(|||||) :: Bool -> Bool -> Bool
False ||||| False = False
True  ||||| _ = True
_ ||||| True = True
```

```haskell
testExpected == map (uncurry (|||||)) testIn
```

## Chapter 5


#### The Caeser Cipher 

```haskell
import Data.Char
```

Import Data.Char to obtain access to the functions ord and chr. This are use to map characters to the their number representation in Unicode, as well as perfrom the inverse of this operation

```haskell
:t ord
```

```haskell
ord 'a'
```

```haskell
map ord ['a'..'e']
```

```haskell
:t chr
```

```haskell
chr 97
```

```haskell
map (chr . ord) ['a'..'e']
```

Define functions for shifting a character by `n`. Note this this should only operate on lower case characters. Use this using the isLower function provided by Data.Char.

```haskell
length ['a'..'z']
```

```haskell
charToInt :: Char -> Int
charToInt c = ord c - ord 'a'

intToChar :: Int -> Char
intToChar i = chr (i + ord 'a')


shift :: Int -> Char -> Char
shift n c | isLower c = intToChar ((charToInt c + n) `mod` 26)
          | otherwise = c
```

```haskell
shift 25 'a'
```

```haskell
shift 25 'b'
```

```haskell
shift 26 'a'
```

```haskell
shift 26 'b'
```

Based on this, create a function to encode strings

```haskell
encode :: Int -> String -> String
encode n cs = [ shift n c | c <- cs ]
```

Perform basic checks

```haskell
encode 3 "haskell is fun" == "kdvnhoo lv ixq"
```

```haskell
encode (-3) "kdvnhoo lv ixq" == "haskell is fun"
```

```haskell
encode 23 "kdvnhoo lv ixq" == "haskell is fun"
```

```haskell
encode 0 "hello world" == "hello world"
```

Consider cracking this encoding, for a given unknown shift, using known frequencies of letters in the English language.


To infer which shift is the 'most likely', we will find the shift that minimizes the following $\chi^2$ statistic:

$$
\chi^2 = \sum_{i=0}^{25}\frac{(o_i-e_i)^2}{e_i}
$$

where $o_i$ is the observed frequency of the $i^{\text th}$ character and $e_i$ is the expected frequency.

The expected frequencies are given by `expCharFreq`.


```haskell
expCharFreq :: [Float]
expCharFreq = [8.1, 1.5, 2.8, 4.2, 12.7, 2.2, 2.0, 6.1, 7.0, 0.2, 0.8, 4.0, 2.4, 6.7, 7.5, 1.9, 0.1, 6.0, 6.3, 9.0, 2.8, 1.0, 2.4, 0.2, 2.0, 0.1]
```

```haskell
length expCharFreq
```

```haskell
sum expCharFreq
```

We need a way of calculating the frequencies of each letter within a string. To do that, we need to count the occurence of each letter and divide by the total number of letters to get a frequency (in percent).

```haskell
numOccurrences :: Char -> String -> Int
numOccurrences c' cs = length [ 1 | c <- cs, c == c' ]
```

```haskell
occurrences :: String -> [Int]
occurrences xs = [ numOccurrences a xs | a <- ['a'..'z'] ]
```

```haskell
toPercent :: Int -> Int -> Float
toPercent n d = 100.0 * ( fromIntegral n / fromIntegral d )
```

```haskell
numLower :: String -> Int
numLower xs = length [ x | x <- xs, isLower x]
```

```haskell
frequencies :: String -> [Float]
frequencies s = map (\x -> toPercent x (numLower s)) (occurrences s)
```

```haskell
sum $ frequencies "the quick brown fox jumped over the lazy dog"
```

<!-- #region -->
Note the fromIntegral function. The `Num` class has a function `fromInteger`; note that this cannot be used directly, since the types used in `toPercent` are `Int`, not `Integer`.

`Real` types are both instances of `Num` and `Ord`. 

`Integral` types are instances of `Real` and `Enum`. The `Integral` class implements `div` and `mod`, as well as `toInteger`.

`fromInegral` is defined as:

```haskell
fromIntegral = fromInteger . toInteger
```

and allows for coersion of Integral types to other Num types, e.g. Float.
<!-- #endregion -->

```haskell
chiSquare :: [Float] -> [Float] -> Float
chiSquare os es = sum [ (o-e)^2/e | (o,e) <- zip os es  ]
```

```haskell
obsCharFreq :: Int -> String -> [Float]
obsCharFreq n xs = let encodedString = encode n xs
                   in frequencies encodedString
```

```haskell
chiSquareByShift :: String -> [Float]
chiSquareByShift s = [ chiSquare (obsCharFreq n s) charFreq | n <- [0..25]]
```

Given this, we now need a function to determine the index associated with the smallest value.

```haskell
minpos :: Ord a => [a] -> Int
minpos xs = head $ [ snd x | x <- zip xs [0..], fst x == minimum xs]
```

```haskell
crack :: String -> String
crack s = encode n s
            where n = minpos $ chiSquareByShift s
```

```haskell
crack "kdvnhoo lv ixq"
```

An alternative method involves the function rotate

```haskell
shiftLeft :: Int -> [a] -> [a]
shiftLeft n xs = drop n xs ++ take n xs
```

```haskell
crack' :: String -> String
crack' s = encode (-n) s
            where n = minpos chitab
                  chitab = [ chiSquare (shiftLeft n freq) charFreq | n <- [0..25]]
                  freq = frequencies s
```

```haskell
crack' "kdvnhoo lv ixq"
```

#### Q1

```haskell
sum [ x^2 | x <- [1..100] ]
```

#### Q2 

```haskell
grid :: Int -> Int -> [(Int, Int)]
grid m n = [ (x, y) | x <- [0..m], y <- [0..n] ]
```

```haskell
grid 1 2
```

#### Q3

```haskell
square :: Int -> [(Int, Int)]
square n = [ p | p <- grid n n, uncurry (/=) p ]
```

```haskell
square 2
```

#### Q4 

```haskell
replicate' :: Int -> a -> [a]
replicate' n x = [ x | i <- [1..n] ]
```

```haskell
replicate' 5 "foo"
```

#### Q5 

```haskell
pyths :: Int -> [(Int, Int, Int)]
pyths n = [ (x, y, z) | x <- [1..n], y <- [1..n], z <- [1..n], x^2 + y^2 == z^2 ]
```

```haskell
pyths 10
```
