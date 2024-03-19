<!-- conventional commits: https://www.conventionalcommits.org/en/v1.0.0/ -->

# calc-programming

Hobby project using the macro programming features of stack-based GNU Emacs Calc, leading to esoteric-looking code, to solve numeric puzzles as those proposed by Project Euler or Rosetta code.

_All information and codes are included in the present README file._

## Table of contents

[Introduction](#introduction)  

[How to use below codes](#how-to-use-below-codes)  

[Standard Calc commands and useful macros](#standard-calc-commands-and-useful-macros)  
- [Calculations](#calculations)  
- [Primes](#primes)  
- [Comparison](#comparison)  
- [Vector/list manipulation](#vectorlist-manipulation)  
- [Matrix manipulation](#matrix-manipulation)  
- [Control flow commands](#control-flow-commands)  
- [Stack manipulation commands](#stack-manipulation-commands)  
- [Digits](#digits)  
- [Divisors](#divisors)  
- [Algebraic expressions](#algebraic-expressions)

Project Euler problems: [1](#project-euler-1-multiples-of-3-or-5), [2](#project-euler-2-even-fibonacci-numbers), [3](#project-euler-3-largest-prime-factor), [4](#project-euler-4-largest-palindrome-product), [5](#project-euler-5-smallest-multiple), [6](#project-euler-6-sum-square-difference), [7](#project-euler-7-10-001st-prime), [8](#project-euler-8-largest-product-in-a-series), [9](#project-euler-9-special-pythagorean-triplet), [10](#project-euler-10-summation-of-primes), [11](#project-euler-11-largest-product-in-a-grid), [12](#project-euler-12-highly-divisible-triangular-number), [13](#project-euler-13-large-sum), [97](#project-euler-97-large-non-mersenne-prime)  

[Annex](#annex-emacs-functions-to-quickly-test-calc-macros-in-calc)

## Introduction

Calc is a stack-based calculator included in GNU Emacs. It can be used as a standard calculator similar to HP28/48 calculators, or for advanced mathematics.

Its [manual](https://www.gnu.org/software/emacs/manual/html_mono/calc.html) is available on-line, starting with a [Getting Started](https://www.gnu.org/software/emacs/manual/html_mono/calc.html#Getting-Started) section encompassing a [demonstration](https://www.gnu.org/software/emacs/manual/html_mono/calc.html#Demonstration-of-Calc).

There are several ways to program Calc (see [programming section](https://www.gnu.org/software/emacs/manual/html_mono/calc.html#Programming) of the manual). Here we will exclusively use Calc's _keyboard macros_, which lead to esoteric-looking code. 

For instance, if the stack contains the following elements:
```
2: 123456789
1: 3
```
the following macro will manipulate the above stack and return the 3 first digits of 123456789, that is to say 123:
```
TAB RET H L F 1 + C-u 3 C-M-i - n f S F
``` 
More information on these commands below.

We will use this macro system to solve numeric puzzles as those proposed by Project Euler, or to contribute to Rosetta code. All codes are proposed below in the present file.

## How to use below codes

Copy code within GNU Emacs, select it, invoke `M-x read-kbd-macro`, go into Calc and press `X`.

## Standard Calc commands and useful macros
### Calculations

```
\      integer division
^      power
I^     n-th root
%      modulo
n      -x
&      1/x
Q      square root
f Q    integer square root
F      floor
L      ln
H L    log10
f S    scale by power of 10
E      exp
!      factorial
k g    GCD
k l    LCM
k c    combinations
H k c  permutations
f n    min
f x    max
```

**Modular exponentiation.** The following, using [right-to-left binary method](https://en.wikipedia.org/wiki/Modular_exponentiation#Right-to-left_binary_method), replaces stack containing `3: b 2: e 1: m` by `b^e mod m`

```
1 SPC C-u 4 C-M-i C-u 3 C-j % C-u 4 TAB Z{ C-u 3 C-j 0 a= Z/ C-u 3 C-j 2 SPC % 1 a= Z[ C-u 4 C-j * C-j % Z] C-u 3 C-M-i b r C-u 3 TAB C-u 4 C-M-i RET * C-u 3 C-j % C-u 4 TAB Z} TAB DEL TAB DEL TAB DEL
```

### Primes

```
k f             prime factorization as vector
k n             next prime
k p             is prime? (does not consume element, and print result in echo)
prime(k)        is prime? 0/1
'prime($1) RET  is prime? 0/1
```

**Largest prime factor.** The following macro returns the largest prime factor of the top element of the stack (consumed). It first computed the prime decomposition (`k f`), reverses it (`v v`) and extracts the first element. For instance: 600851475143 --> 6857.
```
k f v v v r 1 RET
```

**N-th prime.** The following macro returns the n-th prime where n is the top element of the stack (consumed). It takes advantage of `k n` command which computes next prime. For instance: 10001 --> 104743 (in a few seconds).
```
1 - 2 TAB Z< k n Z>
```

### Comparison

```
a=  compare the last elements of the stack, and return 1 or 0
a<  return 1 if element #2 < element #1, and 0 otherwise
```

### Vector/list manipulation

```
v l         ;; length
v p         ;; pack stack in a vector
v v         ;; reverse vector
v r 3 RET   ;; extract 3rd element of vector (for macro, use prefix argument ~)
v x 20 RET  ;; returns vector [1, ..., 20]   (for macro, use prefix argument ~)
v R ...     ;; reduce vector according to macro '...'
V M ...     ;; map macro '...' to list
v k         ;; add at the beginning of the list (cons)
|           ;; append
```

### Matrix manipulation

```
v c         ;; extract matrix column
```

Let's suppose that stack is:
```
5: [[ ... ]]  ;; matrix
4:
3: 6          ;; row number
2; 6          ;; column number
1: 5          ;; offset (stack position of matrix)
```
then the macro `1 - ~ C-j TAB ~ v c TAB ~ v c` returns the relevant element of the matrix (matrix is not consumed, but row number, column number and offset are consumed).

### Control flow commands

```
Z[ ... Z]         ;; if top element of the stack is non-zero, execute '...'
Z[ ... Z: ... Z]  ;; if top element of the stack is non-zero, execute the first '...', else the second '...' 
Z( ... 1 Z)       ;; 'for' loop (*)
20 Z< ... Z>      ;; repeat '...' 20 times
Z{ ... Z/ ... Z}  ;; repeat until
```
(*) Explanations for 'for' loop in exercise 6 of ['Programming tutorial'](https://www.gnu.org/software/emacs/manual/html_mono/calc.html#Programming-Tutorial) part of manual
Note: the loop shall not be empty: `3 4 Z( ... 1 n Z)` seems to fail.
Note: the loop shall not be from 1 to 1: `1 1 Z( ... 1 n Z)` seems to fail. 

### Stack manipulation commands

```
RET          (copy) duplicate top element; former stack content moves downward by 1 line
C-u 2 RET    (copy) copy first two elements of the stack
C-j          (copy) element #2 is copied on top of the stack; former stack content moves downward by 1 line
C-u 5 C-j    (copy) element #5 is copied on top of the stack; former stack content moves downward by 1 line
TAB          (move) swap two last elements
C-u 5 TAB    (move downward) element #1 is taken out, elements 2-5 move to the top
                             and become 1-4,
                             and former element #1 is inserted as element #5
C-u 5 C-M-i  (move upward) element #5 is taken out, elements 1-4 move to the bottom
                           and become 2-5, 
                           and former element #5 is inserted as element #1
DEL          (delete) remove top element from the stack
M-DEL        (delete) remove second element from the stack, former lines 3-... move towards the top
C-u 4 M-DEL  (delete) element #4 is deleted, former lines 5-... move towards the top
C-u 0 DEL    (delete) empty stack
~ DEL        (delete) delete the first n+1 elements of the stack,
                      where n is the top element of the stack
```

For basic stack manipulation commands, see [relevant entry of the manual](https://www.gnu.org/software/emacs/manual/html_mono/calc.html#Stack-Manipulation).
On the use of `~`, see the section of the manual on [prefix argument](https://www.gnu.org/software/emacs/manual/html_node/calc/Prefix-Arguments.html).

Caution: if n be the top element of stack, `~ C-j` copies n-th element of stack ,_where n is considered after consumption of the top element_, to the top

Derived more sophisticated macros:
```
C-u 3 C-M-i C-u 4 TAB                             ;; (swap) swap elements #3 and #4
C-u 4 C-M-i 1 + C-u 4 TAB                         ;; (add) add 1 to element #4
C-u 4 C-M-i + C-u 3 TAB                           ;; (add) consume element at top of the stack
                                                  ;;    and add it to initial element #4
C-u 4 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 4 TAB  ;; (compare and replace) compare element #1
                                                  ;;    (not consumed) to element #4 of the
                                                  ;;    stack; if element #1 > element #4, 
                                                  ;;    element #4 is replaced by element #1
```

### Digits 

**Number of digits.** The following macro returns the number of digits of top element of the stack (consumed), through the formula 1 + floor(log10(n)). For instance: 145 --> 3.
```
H L F 1 +
```

**Number formed with first m digits.** The following macro returns the number formed with first m (element #1 of stack, consumed) digits of number n (element #2 of stack, consumed). For instance: 12345678, 3 --> 123.
```
TAB RET H L F 1 + C-u 3 C-M-i - n f S F
```

**Number formed with last m digits.** The following macro returns the number formed with last m (element #1 of the stack, consumed) digits of number n (element #2 of the stack, consumed). For instance: 123456789, 3 --> 789.
```
10 TAB ^ %
```

**For all digits.** The following code iterates on all digits of the first element of the stack (consumed).
``` 
'acc TAB Z{ RET 10 % RET C-u 4 C-M-i
      ;; Stack;
      ;;   2: acc
      ;;   1: current digit
   ;; do something (which consumes top element of the stack)
C-u 3 TAB - RET 0 a= Z/ 10 \ Z} DEL
```

**Sum of digits.** By successive divisions by 10, the following macro returns the sum of digits of the top element of the stack (consumed). For instance: 145 --> 10
```
0 TAB Z{ RET 10 % RET C-u 4 C-M-i + C-u 3 TAB - RET 0 a= Z/ 10 \ Z} DEL
```

**Product of digits.** By successive divisions by 10, the following macro returns the product of digits of the top element of the stack (consumed). For instance: 245 --> 40
```
1 TAB Z{ RET 10 % RET C-u 4 C-M-i * C-u 3 TAB - RET 0 a= Z/ 10 \ Z} DEL
```

**List of digits within the stack.** By successive divisions by 10, the following macro returns the list of digits of the first element of the stack (consumed) within the stack, with the number of digits as new top element of the stack. For instance: 145 --> 5 4 1 3 (upward in the stack).
```
0 TAB Z{ RET 10 % RET C-u 4 TAB - TAB 1 + TAB RET 0 a= Z/ 10 \ Z} DEL
```

**List of digits as list.** By successive divisions by 10, the following macro returns the list of digits of the first element of the stack (consumed), as a list. For instance: 145 --> [1, 4, 5]
```
[] TAB Z{ RET 10 % RET C-u 4 C-M-i v k C-u 3 TAB - RET 0 a= Z/ 10 \ Z} DEL
```

In order to _compare the above two macros to return the list of digits of a given number_, let's operate them on all numbers from 1 to 5000. The macro returning the digits as a list is quicker.

```
;; list of digits within the stack:
;; --------------------------------
1 SPC 5000 Z( 
   0 TAB Z{ RET 10 % RET C-u 4 TAB - TAB 1 + TAB RET 0 a= Z/ 10 \ Z} DEL
   ~ DEL  ;; clean stack
1 Z)
;; 40 s

;; list of digits as a list:
;; -------------------------
1 SPC 5000 Z(
   [] TAB Z{ RET 10 % RET C-u 4 C-M-i v k C-u 3 TAB - RET 0 a= Z/ 10 \ Z} DEL
   DEL
1 Z)
;; 30 s
```

**Reverse number.** By successive divisions by 10, the following macro reverses the number which is the first element of the stack (consumed). For instance: 145 --> 541
```
0 TAB Z{ RET 0 a= Z/ RET 10 \ TAB 10 % C-u 3 C-M-i 10 * + TAB Z} DEL
```

**List of non-zero digits.** The following macro returns the list of non-zero digits of the first element of the stack (consumed), as a list. For instance: 1040050 --> [1, 4, 5]
```
[] TAB Z{ RET 10 % RET RET 0 a = Z[ DEL Z: C-u 4 C-M-i v k C-u 3 TAB Z] - RET 0 a= Z/ 10 \ Z} DEL
```

**Palindromic numbers.** The following macro consumes a number and returns 1 if it is palindromic (and 0 otherwise), by comparing the list of digits and its reverse list.
```
[] TAB Z{ RET 10 % RET C-u 4 C-M-i v k C-u 3 TAB - RET 0 a= Z/ 10 \ Z} DEL RET v v a=
```
(This method is quicker than comparing number to its reverse number.)

### Divisors

**Number of divisors (version 1, slow)** The following macro, in detailed then compact form, tests all integers between 1 and the integer square root of n (top element of the stack, consumed) to compute the number of divisors of n.

Specific case when n >= 4:
```
RET f Q   ;; 'f Q' = integer square root
RET RET * C-u 3 C-j a= n 0 SPC C-u 4 C-M-i 1 SPC C-u 5 C-M-i
;; Stack
;;    5: 0/-1     ;; -1 if n is perfect integer square, 0 otherwise
;;    4: 0        ;; current nb
;;    3: n
;;    2: 1
;;    1: isqrt(n)
Z(
;; Stack
;;   4: 0/-1        ;; -1 if n is perfect integer square, 0 otherwise
;;   3: current nb
;;   2: n
;;   1: i
C-j TAB % 0 a= Z[             ;; if new divisor...
   C-u 2 C-M-i 1 + C-u 2 TAB  ;; add 1 to current nb
Z]
1 Z)
DEL 2 * +
```

General case when n >= 1 (initial test cases to circumvent the fact that 'loop from 1 to 1' does not seem to work):
```
RET 1 a= Z[     ;; if n == 1, return 1
   DEL 1
Z:
   RET 4 a< Z[  ;; if n == 2 or 3, return 2
      DEL 2
   Z:           ;; if n >= 4...
      RET f Q   ;; 'f Q' = integer square root
      RET RET * C-u 3 C-j a= n 0 SPC C-u 4 C-M-i 1 SPC C-u 5 C-M-i
      ;; Stack
      ;;    5: 0/-1     ;; -1 if n is perfect integer square, 0 otherwise
      ;;    4: 0        ;; current nb
      ;;    3: n
      ;;    2: 1
      ;;    1: isqrt(n)
      Z(
      ;; Stack
      ;;   4: 0/-1        ;; -1 if n is perfect integer square, 0 otherwise
      ;;   3: current nb
      ;;   2: n
      ;;   1: i
      C-j TAB % 0 a= Z[             ;; if new divisor...
         C-u 2 C-M-i 1 + C-u 2 TAB  ;; add 1 to current nb
      Z]
      1 Z)
      DEL 2 * +
   Z] 
Z]
```

**Number of divisors (version 2, quicker)** The following macro uses the same algorithm with algebraic mode:

```
RET f Q 'sum(mod($2,k)=0,k,1,$1) RET 2 * C-u 3 TAB RET * a= -
```

**Number of divisors (version 3, quickest)** The following macro, in detailed then compact form, computes the number of divisors of n (>= 4) by using its decomposition into prime numbers (built-in function `k f`), and the following formula: if n = p1^a1...pr^ar then its number of divisors is (1+a1)...(1+ar).

```
k f                              ;; prime decomposition as vector
1 SPC TAB 0 SPC TAB 0 SPC TAB
RET v l 1 SPC TAB 
;; Stack:
;;   6: 0                        ;; current nb of divisors
;;   5: 0                        ;; current prime
;;   4: 0                        ;; nb of current prime
;;   3: [2, 2, 3, 5...]          ;; primes
;;   2: 1
;;   1: 14                       ;; nb of primes
Z(
   ;; Stack:
   ;;    5: 5                    ;; current nb of divisors
   ;;    4: 2                    ;; nb of current prime 
   ;;    3: 3                    ;; current prime
   ;;    2: [2, 2, 3, 5...]      ;; primes
   ;;    1: i
   C-j TAB ~ v r RET             ;; extract new prime
   ;; Stack:
   ;;    6: 5                    ;; current nb of divisors
   ;;    5: 2                    ;; nb of current prime 
   ;;    4: 3                    ;; current prime
   ;;    3: [2, 2, 3, 5...]      ;; primes
   ;;    2: 5                    ;; new prime
   ;;    1: 5                    ;; copy of new prime
   C-u 4 C-j a= Z[               ;; if same prime as before...
      ;; Stack:
      ;;    5: 5                 ;; current nb of divisors
      ;;    4: 2                 ;; nb of current prime 
      ;;    3: 3                 ;; current prime
      ;;    2: [2, 2, 3, 5...]   ;; primes
      ;;    1: 5                 ;; new prime
      DEL
      C-u 3 C-M-i 1 + C-u 3 TAB  ;; increase nb of current prime
   Z:                            ;; if new prime...
      ;; Stack:
      ;;    5: 5                 ;; current nb of divisors
      ;;    4: 2                 ;; nb of current prime 
      ;;    3: 3                 ;; current prime
      ;;    2: [2, 2, 3, 5...]   ;; primes
      ;;    1: 5                 ;; new prime
      C-u 4 C-M-i 1 + C-u 5 C-M-i * C-u 4 TAB  ;; update current nb of divisors
      1 SPC C-u 4 TAB                          ;; update nb of current prime
      C-u 3 M-DEL TAB                          ;; update current prime
   Z]
1 Z)
;; Stack:
;;    5: 5                       ;; current nb of divisors
;;    4: 2                       ;; nb of current prime 
;;    3: 3                       ;; current prime
;;    2: [2, 2, 3, 5...]         ;; primes
DEL DEL 1 + *
```

```
k f 1 SPC TAB 0 SPC TAB 0 SPC TAB RET v l 1 SPC TAB Z( C-j TAB ~ v r RET C-u 4 C-j a= Z[ DEL C-u 3 C-M-i 1 + C-u 3 TAB Z: C-u 4 C-M-i 1 + C-u 5 C-M-i * C-u 4 TAB 1 SPC C-u 4 TAB C-u 3 M-DEL TAB Z] 1 Z) DEL DEL 1 + *
```

### Algebraic expressions

The below macro, based on `a+` calculates the sum of k^2 for k=1...100:
```
'k*k RET 'k RET 1 SPC 100 a+ RET
```

It seems that there is no command to minimize a formula _on integer values_.

## Project Euler 1: Multiples of 3 or 5

_If we list all the natural numbers below 10 that are multiples of 3 or 5, we get 3, 5, 6 and 9. The sum of these multiples is 23. Find the sum of all the multiples of 3 or 5 below 1000._
[(source)](https://projecteuler.net/problem=1)

The following snippet pops the first element of the stack and replaces by 0 if it is a multiple of 3 or 5, and by non-zero otherwise.
```
RET 3 % Z[ 5 % Z: DEL 0 Z]
```

Proposed code is the following:
```
1000 SPC                    ;; puzzle parameter
1 - 0 TAB 1 TAB
                            ;; Stack is:
                            ;;    3: 0    ;; accumulator for sum
                            ;;    2: 1
                            ;;    1: 999
Z(
RET
RET 3 % Z[ 5 % Z: DEL 0 Z]  ;; non-multiple of 3 or 5?
Z[ DEL Z: + Z]              ;; if multiple, add to accumulator
1 Z)
```

The same as a one-liner:
```
1000 SPC 1 - 0 TAB 1 TAB Z( RET RET 3 % Z[ 5 % Z: DEL 0 Z] Z[ DEL Z: + Z] 1 Z)
```

It yields the correct result (voluntarily not shown here).

<!--- 1:  233168 --->
 
For 10000 as an input, it returns 23331668 in roughly 11 seconds on my (normal) laptop (as of January 2024).

## Project Euler 2: Even Fibonacci Numbers

_Each new term in the Fibonacci sequence is generated by adding the previous two terms. 
By starting with 1 and 2, the first 10 terms will be: 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, ...
By considering the terms in the Fibonacci sequence whose values do not exceed four million, find the sum of the even-valued terms._ [(source)](https://projecteuler.net/problem=2) 

```
0 SPC                       ;; running sum
0 SPC                       ;; first Fibonacci number
1                           ;; second Fibonacci number
Z{                          ;; repeat...
TAB C-j +                   ;; the two next Fibonacci numbers are on the stack
RET 4000000 TAB a< Z/       ;; if last Fibonacci number is > 4000000, break loop
RET RET 2 % Z[              ;; if last Fibonacci number is odd...
   DEL 
   Z:                       ;; last Fibonacci number is even...
   C-u 4 C-M-i + C-u 3 TAB  ;; add to running sum currently element #4 
   Z]                       
Z}                          ;; end of repeat
DEL DEL                     ;; delete Fibonacci numbers 
```

The same as a one-liner:
```
0 SPC 0 SPC 1 Z{ TAB C-j + RET 4000000 TAB a< Z/ RET RET 2 % Z[ DEL Z: C-u 4 C-M-i + C-u 3 TAB Z] Z} DEL DEL
```

It yields the correct result (voluntarily not shown here).

<!--- 4613732 --->

## Project Euler 3: Largest Prime Factor

_The prime factors of 13195 are 5, 7, 13 and 29. What is the largest prime factor of the number 600851475143?_ [(source)](https://projecteuler.net/problem=3)

We use the macro seen above to calculate the largest prime factor of a number.
```
600851475143 k f v v v r 1
```

It yields the correct result (voluntarily not shown here).

<!--- 6857 --->

## Project Euler 4: Largest Palindrome Product

_A palindromic number reads the same both ways. The largest palindrome made from the product of two 2-digit numbers is 9009 = 91 x 99.  
Find the largest palindrome made from the product of two 3-digit numbers._

The following macro builds two nested loops : n1 goes down from 999 and n2 goes down 
```
1 SPC                             ;; current max
999                               ;; initial value of n1
Z{
   C-u 2 RET RET * a> Z/          ;; if current max > n1*n1, break from outer loop
   RET 1 -
      ;; Stack:
      ;;    3: current max
      ;;    2: n1
      ;;    1: n2
   Z{
      C-u 3 RET * a> Z/          ;; if current max > n1*n2, then break from inner loop
      C-u 2 RET *
         ;; Stack:
         ;;    4: current max
         ;;    3: n1
         ;;    2: n2
         ;;    1: n1*n2
     RET [] TAB Z{ RET 10 % RET C-u 4 C-M-i v k C-u 3 TAB - RET 0 a= Z/ 10 \ Z} DEL RET v v a=  ;; is n1*n2 palindromic?
     Z[ C-u 4 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 4 TAB Z]                                     ;; if yes, update current max if relevant
     DEL                         ;; delete n1*n2
     1 - RET 100 a< Z/           ;; decrease n2; if n2 < 100, break from inner loop
  Z}
  DEL                            ;; delete n2
     ;; Stack:
     ;;    2: current max
     ;;    1: n1
  1 - RET 101 a< Z/              ;; decrease n1; if n1 < 101, break from outer loop
Z}
DEL                              ;; delete n1
```

As an one-liner:
```
1 SPC 999 Z{ C-u 2 RET RET * a> Z/ RET 1 - Z{ C-u 3 RET * a> Z/ C-u 2 RET * RET [] TAB Z{ RET 10 % RET C-u 4 C-M-i v k C-u 3 TAB - RET 0 a= Z/ 10 \ Z} DEL RET v v a= Z[ C-u 4 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 4 TAB Z] DEL 1 - RET 100 a< Z/ Z} DEL 1 - RET 101 a< Z/ Z} DEL
```

It yields the correct result (voluntarily not shown here) in a few minutes.

<!--- 906609 ---> 

In above macro, palindromic test is based on the construction of a reverse number by building a list of all digits of the said number. Below is a variant of with test based on the building of the reverse number by successive divisions by 10. It takes more time to execute.

```
1 SPC 999 Z{ C-u 2 RET RET * a> Z/ RET 1 - Z{ C-u 3 RET * a> Z/ C-u 2 RET * 
;; RET [] TAB Z{ RET 10 % RET C-u 4 C-M-i v k C-u 3 TAB - RET 0 a= Z/ 10 \ Z} DEL RET v v a=
RET RET 0 TAB Z{ RET 0 a= Z/ RET 10 \ TAB 10 % C-u 3 C-M-i 10 * + TAB Z} DEL a=
Z[ C-u 4 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 4 TAB Z] DEL 1 - RET 100 a< Z/ Z} DEL 1 - RET 101 a< Z/ Z} DEL
```

## Project Euler 5: Smallest Multiple


_2520 is the smallest number that can be divided by each of the numbers from 1 to 10 without any remainder. What is the smallest positive number that is evenly divisible by all of the numbers from 1 to 20?_ [(source)](https://projecteuler.net/problem=5)

```
v x 20 RET       ;; iota: returns [1, ..., 20]
v R k l          ;; reduce with 'k l' i.e. reduce with lcm
```

It yields the correct result (voluntarily not shown here).

<!--- 232792560 --->

## Project Euler 6: Sum Square Difference

_The sum of the squares of the first ten natural numbers is  
1^2 + 2^2 + ... + 10^2 = 385  
The square of the sum of the first ten natural numbers is  
(1 + 2 + ... + 10)^2 = 552 = 3025  
Hence the difference between the sum of the squares of the first ten natural numbers and the square of the sum is 3025 − 385 = 2640.  
Find the difference between the sum of the squares of the first one hundred natural numbers and the square of the sum._  
[(source)](https://projecteuler.net/problem=6)

```
0 SPC 1 SPC 100 Z( + 1 Z) RET *  ;; (1 + ... + 100)^2
0 SPC 1 SPC 100 Z( RET * + 1 Z)  ;; 1^2 + ... + 100^2
-
```

It yields the correct result (voluntarily not shown here).

<!-- 25164150 --> 

A variant using algebraic expressions:
```
'k RET 'k RET 1 SPC 100 a+ RET RET *
'k*k RET 'k RET 1 SPC 100 a+ RET
-
```

## Project Euler 7: 10 001st Prime

_By listing the first six prime numbers: 2, 3, 5, 7, 11, and 13, we can see that the 6th prime is 13.  
What is the 10 001st prime number?_ [(source)](https://projecteuler.net/problem=7)

Let's take advantage of `k n` which returns next prime.

```
2 SPC                       ;; first prime
10001 SPC 1 - Z< k n Z>     ;; repeat 10000 times 'k n', i.e. 'next prime'
```

It yields the correct result (voluntarily not shown here).

<!--- 104743 --->

## Project Euler 8: Largest Product in a Series

_The four adjacent digits in the 1000-digit number that have the greatest product are 9x9x8x9 = 5832.  
73167176531330624919225119674426574742355349194934  
96983520312774506326239578318016984801869478851843  
85861560789112949495459501737958331952853208805511  
12540698747158523863050715693290963295227443043557  
66896648950445244523161731856403098711121722383113  
62229893423380308135336276614282806444486645238749  
30358907296290491560440772390713810515859307960866  
70172427121883998797908792274921901699720888093776  
65727333001053367881220235421809751254540594752243  
52584907711670556013604839586446706324415722155397  
53697817977846174064955149290862569321978468622482  
83972241375657056057490261407972968652414535100474  
82166370484403199890008895243450658541227588666881  
16427171479924442928230863465674813919123162824586  
17866458359124566529476545682848912883142607690042  
24219022671055626321111109370544217506941658960408  
07198403850962455444362981230987879927244284909188  
84580156166097919133875499200524063689912560717606  
05886116467109405077541002256983155200055935729725  
71636269561882670428252483600823257530420752963450  
Find the thirteen adjacent digits in the 1000-digit number that have the greatest product.   What is the value of this product?_  
[(source)](https://projecteuler.net/problem=8)

```
73167176531330624919225119674426574742355349194934 SPC
96983520312774506326239578318016984801869478851843 SPC
85861560789112949495459501737958331952853208805511 SPC
12540698747158523863050715693290963295227443043557 SPC
66896648950445244523161731856403098711121722383113 SPC
62229893423380308135336276614282806444486645238749 SPC
30358907296290491560440772390713810515859307960866 SPC
70172427121883998797908792274921901699720888093776 SPC
65727333001053367881220235421809751254540594752243 SPC
52584907711670556013604839586446706324415722155397 SPC
53697817977846174064955149290862569321978468622482 SPC
83972241375657056057490261407972968652414535100474 SPC
82166370484403199890008895243450658541227588666881 SPC
16427171479924442928230863465674813919123162824586 SPC
17866458359124566529476545682848912883142607690042 SPC
24219022671055626321111109370544217506941658960408 SPC
07198403850962455444362981230987879927244284909188 SPC
84580156166097919133875499200524063689912560717606 SPC
05886116467109405077541002256983155200055935729725 SPC
71636269561882670428252483600823257530420752963450 SPC
19 Z< RET H L F 1 + 10 TAB ^ C-u 3 C-M-i * + Z>                            ;; concatenate numbers
0 SPC TAB
   ;; Stack:
   ;;   2: current max (0)
   ;;   1: number
Z{ RET 10 SPC 13 ^ a< Z/                                                   ;; while number < 10^13
   RET 10 SPC 13 ^ %
   1 TAB Z{ RET 10 % RET C-u 4 C-M-i * C-u 3 TAB - RET 0 a= Z/ 10 \ Z} DEL ;; product of digits
   ;; Stack:
   ;;   3: current max
   ;;   2: number
   ;;   1: product of 13 last digits
   C-u 3 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 3 TAB DEL                    ;; update current max if relevant
   10 \
Z} DEL
```

It yields the correct result (voluntarily not shown here), in about 30 seconds on my standard laptop.

<!-- 23514624000 -->

## Project Euler 9: Special Pythagorean Triplet
    
_A Pythagorean triplet is a set of three natural numbers, a < b < c, for which,
a2 + b2 = c2  
For example, 32 + 42 = 9 + 16 = 25 = 52.  
There exists exactly one Pythagorean triplet for which a + b + c = 1000.  
Find the product abc._  
[(source)](https://projecteuler.net/problem=9)

```
1000 SPC 3 Z(                     ;; for c from n downto 3
   RET 1 - TAB RET 1 + 1000 - n C-u 3 C-M-i f n
   TAB RET 1000 - n 2 \ 2 f x C-u 3 C-M-i
   ;; Stack
   ;;   3: c
   ;;   2: max(2, (n-c)\2)
   ;;   1: min(c-1, n-c-1)
   C-u 2 RET a> Z[ DEL DEL Z: TAB ;; if min(...) > max(...)
      Z(                          ;; for b = min(...) downto max(...)
         C-u 2 RET + 1000 - n
         ;; Stack
         ;;    3: c
         ;;    2: b
         ;;    1: a = 1000-b-c
         C-u 3 RET RET * TAB RET * + TAB RET * a= ;; if a^2+b^2 = c^2
         Z[ C-u 3 RET * * C-u 4 TAB Z]            ;; keep a*b*c above on the stack
      DEL DEL
      1 n Z)
   Z]
   DEL
1 n Z)
```

It yields the correct result (voluntarily not shown here), after a certain time on my standard laptop.

<!-- 31875000 --> 

## Project Euler 10: Summation of Primes

_The sum of the primes below 10 is 2 + 3 + 5 + 7 = 17.  
Find the sum of all the primes below two million._  
[(source)](https://projecteuler.net/problem=10)

The following code should work but it does not finish in a reasonable time on my standard laptop:
```
0 SPC 2 SPC 19999 Z(
   RET 'prime($) RET
   Z[ + Z: DEL Z]
1 Z)
```

This one is fine:
```
'prime(k)*k RET  ;; = k if k prime and 0 otherwise
'k RET 
2 SPC
1999999
a+
```

It yields the correct result (voluntarily not shown here).

<!-- 142913828922 -->

## Project Euler 11: Largest Product in a Grid

_In the 20 x 20 grid below, four numbers along a diagonal line have been marked in red.  
08 02 22 97 38 15 00 40 00 75 04 05 07 78 52 12 50 77 91 08  
[...]  
01 70 54 71 83 51 54 69 16 92 33 48 61 43 52 01 89 19 67 48  
The product of these numbers is 26 x 63 x 78 x 14 = 1788696.  
What is the greatest product of four adjacent numbers in the same direction (up, down, left, right, or diagonally) in the 20 x 20 grid?_  
[(source)](https://projecteuler.net/problem=11)


```
[ [08, 02, 22, 97, 38, 15, 00, 40, 00, 75, 04, 05, 07, 78, 52, 12, 50, 77, 91, 08],
  [49, 49, 99, 40, 17, 81, 18, 57, 60, 87, 17, 40, 98, 43, 69, 48, 04, 56, 62, 00],
  [81, 49, 31, 73, 55, 79, 14, 29, 93, 71, 40, 67, 53, 88, 30, 03, 49, 13, 36, 65],
  [52, 70, 95, 23, 04, 60, 11, 42, 69, 24, 68, 56, 01, 32, 56, 71, 37, 02, 36, 91],
  [22, 31, 16, 71, 51, 67, 63, 89, 41, 92, 36, 54, 22, 40, 40, 28, 66, 33, 13, 80],
  [24, 47, 32, 60, 99, 03, 45, 02, 44, 75, 33, 53, 78, 36, 84, 20, 35, 17, 12, 50],
  [32, 98, 81, 28, 64, 23, 67, 10, 26, 38, 40, 67, 59, 54, 70, 66, 18, 38, 64, 70],
  [67, 26, 20, 68, 02, 62, 12, 20, 95, 63, 94, 39, 63, 08, 40, 91, 66, 49, 94, 21],
  [24, 55, 58, 05, 66, 73, 99, 26, 97, 17, 78, 78, 96, 83, 14, 88, 34, 89, 63, 72],
  [21, 36, 23, 09, 75, 00, 76, 44, 20, 45, 35, 14, 00, 61, 33, 97, 34, 31, 33, 95],
  [78, 17, 53, 28, 22, 75, 31, 67, 15, 94, 03, 80, 04, 62, 16, 14, 09, 53, 56, 92],
  [16, 39, 05, 42, 96, 35, 31, 47, 55, 58, 88, 24, 00, 17, 54, 24, 36, 29, 85, 57],
  [86, 56, 00, 48, 35, 71, 89, 07, 05, 44, 44, 37, 44, 60, 21, 58, 51, 54, 17, 58],
  [19, 80, 81, 68, 05, 94, 47, 69, 28, 73, 92, 13, 86, 52, 17, 77, 04, 89, 55, 40],
  [04, 52, 08, 83, 97, 35, 99, 16, 07, 97, 57, 32, 16, 26, 26, 79, 33, 27, 98, 66],
  [88, 36, 68, 87, 57, 62, 20, 72, 03, 46, 33, 67, 46, 55, 12, 32, 63, 93, 53, 69],
  [04, 42, 16, 73, 38, 25, 39, 11, 24, 94, 72, 18, 08, 46, 29, 32, 40, 62, 76, 36],
  [20, 69, 36, 41, 72, 30, 23, 88, 34, 62, 99, 69, 82, 67, 59, 85, 74, 04, 36, 16],
  [20, 73, 35, 29, 78, 31, 90, 01, 74, 31, 49, 71, 48, 86, 81, 16, 23, 57, 05, 54],
  [01, 70, 54, 71, 83, 51, 54, 69, 16, 92, 33, 48, 61, 43, 52, 01, 89, 19, 67, 48] ] 
;; rows are counted from top to bottom (visually) 1...20
;; columns are counted from left to right 1...20
;; if stack is:
;;     5: [[ ... ]]  ;; the matrix
;;     4:
;;     3: 6          ;; row number
;;     2; 6          ;; column number
;;     1: 5          ;; offset (stack position of matrix)
;; then the macro 1 - ~ C-j TAB ~ v c TAB ~ v c returns the relevant element of the matrix
;;    (matrix is not consumed, but row number, column number and offset are consumed) 
0 SPC           ;; current max
;;
;; (1) HORIZONTALLY:
;; -----------------
;;
1 SPC 20 Z(     ;; for each line 1...20
   ;; Stack:
   ;;   3: [[ ... ]]  ;; the matrix
   ;;   2: 0          ;; current max
   ;;   1: r          ;; row number
   1 SPC 17 Z(           ;; for each column 1...17
      1 SPC              ;; current 4-item product
      ;; Stack:
      ;;   5: [[ ... ]]  ;; the matrix
      ;;   4: 0          ;; current max
      ;;   3: r          ;; row number
      ;;   2: c          ;; column number
      ;;   1: 1          ;; current 4-item product
      C-u 3 C-j C-u 3 C-j 0 + 8 SPC
      ;; Stack:
      ;;   8: [[ ... ]]  ;; the matrix
      ;;   7: 0          ;; current max
      ;;   6: r          ;; row number
      ;;   5: c          ;; column number
      ;;   4: 1          ;; current 4-item product
      ;;   3: r          ;; row number
      ;;   2: c          ;; column number
      ;;   1: 8          ;; matrix position on stack
      1 - ~ C-j TAB ~ v c TAB ~ v c
      *
      ;; Stack:
      ;;   5: [[ ... ]]  ;; the matrix
      ;;   4: 0          ;; current max
      ;;   3: r          ;; row number
      ;;   2: c          ;; column number
      ;;   1: x          ;; current 4-item product
      C-u 3 C-j C-u 3 C-j 1 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 3 C-j C-u 3 C-j 2 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 3 C-j C-u 3 C-j 3 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 4 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 4 TAB               ;; replace current max if relevant
      DEL
   DEL 1 Z)
DEL 1 Z)
;;
;; (2) VERTICALLY
;; --------------
;;
1 SPC 20 Z(           ;; for each column 1...20
   ;; Stack:
   ;;   3: [[ ... ]]  ;; the matrix
   ;;   2: 0          ;; current max
   ;;   1: c          ;; col number
   1 SPC 17 Z(           ;; for each row 1...17
      1 SPC              ;; current 4-item product
      ;; Stack:
      ;;   5: [[ ... ]]  ;; the matrix
      ;;   4: 0          ;; current max
      ;;   3: c          ;; col number
      ;;   2: r          ;; row number
      ;;   1: 1          ;; current 4-item product
      C-u 3 C-j C-u 3 C-j 0 + TAB 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 3 C-j C-u 3 C-j 1 + TAB 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 3 C-j C-u 3 C-j 2 + TAB 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 3 C-j C-u 3 C-j 3 + TAB 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 4 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 4 TAB               ;; replace current max if relevant
      DEL
   DEL 1 Z)
DEL 1 Z)
;;
;; (3) DIAGONALLY \
;; ----------------
;;
1 SPC 17 Z(           ;; for each line 1...17
   ;; Stack:
   ;;   3: [[ ... ]]  ;; the matrix
   ;;   2: 0          ;; current max
   ;;   1: r          ;; row number
   1 SPC 17 Z(           ;; for each column 1...17
      1 SPC              ;; current 4-item product
      C-u 3 C-j 0 + C-u 3 C-j 0 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 3 C-j 1 + C-u 3 C-j 1 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 3 C-j 2 + C-u 3 C-j 2 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 3 C-j 3 + C-u 3 C-j 3 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 4 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 4 TAB               ;; replace current max if relevant
      DEL
   DEL 1 Z)
DEL 1 Z)
;;
;; (4) DIAGONALLY /
;; ----------------
;;
4 SPC 20 Z(           ;; for each line 4...20
   ;; Stack:
   ;;   3: [[ ... ]]  ;; the matrix
   ;;   2: 0          ;; current max
   ;;   1: r          ;; row number
   1 SPC 17 Z(           ;; for each column 1...17
      1 SPC              ;; current 4-item product
      C-u 3 C-j 0 + C-u 3 C-j 0 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 3 C-j 1 - C-u 3 C-j 1 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 3 C-j 2 - C-u 3 C-j 2 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 3 C-j 3 - C-u 3 C-j 3 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c *
      C-u 4 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 4 TAB                   ;; replace current max if relevant
      DEL
   DEL 1 Z)
DEL 1 Z)
;;
TAB DEL
```

Compact form:

```
[ [08, 02, 22, 97, 38, 15, 00, 40, 00, 75, 04, 05, 07, 78, 52, 12, 50, 77, 91, 08],
  [49, 49, 99, 40, 17, 81, 18, 57, 60, 87, 17, 40, 98, 43, 69, 48, 04, 56, 62, 00],
  [81, 49, 31, 73, 55, 79, 14, 29, 93, 71, 40, 67, 53, 88, 30, 03, 49, 13, 36, 65],
  [52, 70, 95, 23, 04, 60, 11, 42, 69, 24, 68, 56, 01, 32, 56, 71, 37, 02, 36, 91],
  [22, 31, 16, 71, 51, 67, 63, 89, 41, 92, 36, 54, 22, 40, 40, 28, 66, 33, 13, 80],
  [24, 47, 32, 60, 99, 03, 45, 02, 44, 75, 33, 53, 78, 36, 84, 20, 35, 17, 12, 50],
  [32, 98, 81, 28, 64, 23, 67, 10, 26, 38, 40, 67, 59, 54, 70, 66, 18, 38, 64, 70],
  [67, 26, 20, 68, 02, 62, 12, 20, 95, 63, 94, 39, 63, 08, 40, 91, 66, 49, 94, 21],
  [24, 55, 58, 05, 66, 73, 99, 26, 97, 17, 78, 78, 96, 83, 14, 88, 34, 89, 63, 72],
  [21, 36, 23, 09, 75, 00, 76, 44, 20, 45, 35, 14, 00, 61, 33, 97, 34, 31, 33, 95],
  [78, 17, 53, 28, 22, 75, 31, 67, 15, 94, 03, 80, 04, 62, 16, 14, 09, 53, 56, 92],
  [16, 39, 05, 42, 96, 35, 31, 47, 55, 58, 88, 24, 00, 17, 54, 24, 36, 29, 85, 57],
  [86, 56, 00, 48, 35, 71, 89, 07, 05, 44, 44, 37, 44, 60, 21, 58, 51, 54, 17, 58],
  [19, 80, 81, 68, 05, 94, 47, 69, 28, 73, 92, 13, 86, 52, 17, 77, 04, 89, 55, 40],
  [04, 52, 08, 83, 97, 35, 99, 16, 07, 97, 57, 32, 16, 26, 26, 79, 33, 27, 98, 66],
  [88, 36, 68, 87, 57, 62, 20, 72, 03, 46, 33, 67, 46, 55, 12, 32, 63, 93, 53, 69],
  [04, 42, 16, 73, 38, 25, 39, 11, 24, 94, 72, 18, 08, 46, 29, 32, 40, 62, 76, 36],
  [20, 69, 36, 41, 72, 30, 23, 88, 34, 62, 99, 69, 82, 67, 59, 85, 74, 04, 36, 16],
  [20, 73, 35, 29, 78, 31, 90, 01, 74, 31, 49, 71, 48, 86, 81, 16, 23, 57, 05, 54],
  [01, 70, 54, 71, 83, 51, 54, 69, 16, 92, 33, 48, 61, 43, 52, 01, 89, 19, 67, 48] ] 
0 SPC 1 SPC 20 Z( 1 SPC 17 Z( 1 SPC C-u 3 C-j C-u 3 C-j 0 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 3 C-j C-u 3 C-j 1 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 3 C-j C-u 3 C-j 2 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 3 C-j C-u 3 C-j 3 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 4 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 4 TAB DEL DEL 1 Z) DEL 1 Z) 1 SPC 20 Z( 1 SPC 17 Z( 1 SPC C-u 3 C-j C-u 3 C-j 0 + TAB 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 3 C-j C-u 3 C-j 1 + TAB 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 3 C-j C-u 3 C-j 2 + TAB 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 3 C-j C-u 3 C-j 3 + TAB 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 4 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 4 TAB DEL DEL 1 Z) DEL 1 Z) 1 SPC 17 Z( 1 SPC 17 Z( 1 SPC C-u 3 C-j 0 + C-u 3 C-j 0 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 3 C-j 1 + C-u 3 C-j 1 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 3 C-j 2 + C-u 3 C-j 2 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 3 C-j 3 + C-u 3 C-j 3 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 4 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 4 TAB DEL DEL 1 Z) DEL 1 Z) 4 SPC 20 Z( 1 SPC 17 Z( 1 SPC C-u 3 C-j 0 + C-u 3 C-j 0 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 3 C-j 1 - C-u 3 C-j 1 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 3 C-j 2 - C-u 3 C-j 2 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 3 C-j 3 - C-u 3 C-j 3 + 8 SPC 1 - ~ C-j TAB ~ v c TAB ~ v c * C-u 4 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 4 TAB DEL DEL 1 Z) DEL 1 Z) TAB DEL
```

It yields the correct result (voluntarily not shown here).

<!-- 70600674 --> 

## Project Euler 12: Highly Divisible Triangular Number

_The sequence of triangle numbers is generated by adding the natural numbers. So the 7th triangle number would be 1 + 2 + 3 + 4 + 5 + 6 + 7 = 28. The first ten terms would be:
1, 3, 6, 10, 15, 21, 28, 36, 45, 55, ...  
Let us list the factors of the first seven triangle numbers:  
1: 1  
3: 1,3  
6: 1,2,3,6  
10: 1,2,5,10  
15: 1,3,5,1  
21: 1,3,7,21  
28: 1,2,4,7,14,28  
We can see that 28 is the first triangle number to have over five divisors.  
What is the value of the first triangle number to have over five hundred divisors?_ [(source)](https://projecteuler.net/problem=12)

```
7 SPC 28 Z{
   TAB 1 + RET C-u 3 C-M-i +
   ;; Stack:
   ;;    2: n
   ;;    1: nth triangular number
   RET
   k f 1 SPC TAB 0 SPC TAB 0 SPC TAB RET v l 1 SPC TAB Z( C-j TAB ~ v r RET C-u 4 C-j a= Z[ DEL C-u 3 C-M-i 1 + C-u 3 TAB Z: C-u 4 C-M-i 1 + C-u 5 C-M-i * C-u 4 TAB 1 SPC C-u 4 TAB C-u 3 M-DEL TAB Z] 1 Z) DEL DEL 1 + *  ;; nb of divisors
   ;; Stack:
   ;;    3: n
   ;;    2: nth triangular number
   ;;    1: nb of divisors of nth triangular number
   500 a> Z/
Z} 
TAB DEL
```

Compact form:
```
7 SPC 28 Z{ TAB 1 + RET C-u 3 C-M-i + RET k f 1 SPC TAB 0 SPC TAB 0 SPC TAB RET v l 1 SPC TAB Z( C-j TAB ~ v r RET C-u 4 C-j a= Z[ DEL C-u 3 C-M-i 1 + C-u 3 TAB Z: C-u 4 C-M-i 1 + C-u 5 C-M-i * C-u 4 TAB 1 SPC C-u 4 TAB C-u 3 M-DEL TAB Z] 1 Z) DEL DEL 1 + * 500 a> Z/ Z} TAB DEL
```

It yields the correct result (voluntarily not shown here), after 2-3 minutes on my standard laptop.

<!--  76576500 -->

<!-- see also
https://www.gnu.org/software/emacs/manual/html_node/calc/List-Answer-4.html
-->

## Project Euler 13: Large Sum

_Work out the first ten digits of the sum of the following one-hundred 50-digit numbers.  
37107287533902102798797998220837590246510135740250  
[...]  
53503534226472524250874054075591789781264330331690_  
[(source)](https://projecteuler.net/problem=13)

```
37107287533902102798797998220837590246510135740250 SPC
46376937677490009712648124896970078050417018260538 SPC
74324986199524741059474233309513058123726617309629 SPC
91942213363574161572522430563301811072406154908250 SPC
23067588207539346171171980310421047513778063246676 SPC
89261670696623633820136378418383684178734361726757 SPC
28112879812849979408065481931592621691275889832738 SPC
44274228917432520321923589422876796487670272189318 SPC
47451445736001306439091167216856844588711603153276 SPC
70386486105843025439939619828917593665686757934951 SPC
62176457141856560629502157223196586755079324193331 SPC
64906352462741904929101432445813822663347944758178 SPC
92575867718337217661963751590579239728245598838407 SPC
58203565325359399008402633568948830189458628227828 SPC
80181199384826282014278194139940567587151170094390 SPC
35398664372827112653829987240784473053190104293586 SPC
86515506006295864861532075273371959191420517255829 SPC
71693888707715466499115593487603532921714970056938 SPC
54370070576826684624621495650076471787294438377604 SPC
53282654108756828443191190634694037855217779295145 SPC
36123272525000296071075082563815656710885258350721 SPC
45876576172410976447339110607218265236877223636045 SPC
17423706905851860660448207621209813287860733969412 SPC
81142660418086830619328460811191061556940512689692 SPC
51934325451728388641918047049293215058642563049483 SPC
62467221648435076201727918039944693004732956340691 SPC
15732444386908125794514089057706229429197107928209 SPC
55037687525678773091862540744969844508330393682126 SPC
18336384825330154686196124348767681297534375946515 SPC
80386287592878490201521685554828717201219257766954 SPC
78182833757993103614740356856449095527097864797581 SPC
16726320100436897842553539920931837441497806860984 SPC
48403098129077791799088218795327364475675590848030 SPC
87086987551392711854517078544161852424320693150332 SPC
59959406895756536782107074926966537676326235447210 SPC
69793950679652694742597709739166693763042633987085 SPC
41052684708299085211399427365734116182760315001271 SPC
65378607361501080857009149939512557028198746004375 SPC
35829035317434717326932123578154982629742552737307 SPC
94953759765105305946966067683156574377167401875275 SPC
88902802571733229619176668713819931811048770190271 SPC
25267680276078003013678680992525463401061632866526 SPC
36270218540497705585629946580636237993140746255962 SPC
24074486908231174977792365466257246923322810917141 SPC
91430288197103288597806669760892938638285025333403 SPC
34413065578016127815921815005561868836468420090470 SPC
23053081172816430487623791969842487255036638784583 SPC
11487696932154902810424020138335124462181441773470 SPC
63783299490636259666498587618221225225512486764533 SPC
67720186971698544312419572409913959008952310058822 SPC
95548255300263520781532296796249481641953868218774 SPC
76085327132285723110424803456124867697064507995236 SPC
37774242535411291684276865538926205024910326572967 SPC
23701913275725675285653248258265463092207058596522 SPC
29798860272258331913126375147341994889534765745501 SPC
18495701454879288984856827726077713721403798879715 SPC
38298203783031473527721580348144513491373226651381 SPC
34829543829199918180278916522431027392251122869539 SPC
40957953066405232632538044100059654939159879593635 SPC
29746152185502371307642255121183693803580388584903 SPC
41698116222072977186158236678424689157993532961922 SPC
62467957194401269043877107275048102390895523597457 SPC
23189706772547915061505504953922979530901129967519 SPC
86188088225875314529584099251203829009407770775672 SPC
11306739708304724483816533873502340845647058077308 SPC
82959174767140363198008187129011875491310547126581 SPC
97623331044818386269515456334926366572897563400500 SPC
42846280183517070527831839425882145521227251250327 SPC
55121603546981200581762165212827652751691296897789 SPC
32238195734329339946437501907836945765883352399886 SPC
75506164965184775180738168837861091527357929701337 SPC
62177842752192623401942399639168044983993173312731 SPC
32924185707147349566916674687634660915035914677504 SPC
99518671430235219628894890102423325116913619626622 SPC
73267460800591547471830798392868535206946944540724 SPC
76841822524674417161514036427982273348055556214818 SPC
97142617910342598647204516893989422179826088076852 SPC
87783646182799346313767754307809363333018982642090 SPC
10848802521674670883215120185883543223812876952786 SPC
71329612474782464538636993009049310363619763878039 SPC
62184073572399794223406235393808339651327408011116 SPC
66627891981488087797941876876144230030984490851411 SPC
60661826293682836764744779239180335110989069790714 SPC
85786944089552990653640447425576083659976645795096 SPC
66024396409905389607120198219976047599490197230297 SPC
64913982680032973156037120041377903785566085089252 SPC
16730939319872750275468906903707539413042652315011 SPC
94809377245048795150954100921645863754710598436791 SPC
78639167021187492431995700641917969777599028300699 SPC
15368713711936614952811305876380278410754449733078 SPC
40789923115535562561142322423255033685442488917353 SPC
44889911501440648020369068063960672322193204149535 SPC
41503128880339536053299340368006977710650566631954 SPC
81234880673210146739058568557934581403627822703280 SPC
82616570773948327592232845941706525094512325230608 SPC
22918802058777319719839450180888072429661980811197 SPC
77158542502016545090413245809786882778948721859617 SPC
72107838435069186155435662884062257473692284509516 SPC
20849603980134001723930671666823555245252804609722 SPC
53503534226472524250874054075591789781264330331690 SPC
99 Z< + Z>                                              ;; sum of the above
10 TAB RET H L F 1 + C-u 3 C-M-i - n f S F              ;; first 10 digits
```

It yields the correct result (voluntarily not shown here).

<!-- 5500276230 -->

## Project Euler 97: Large Non-Mersenne Prime
 
_The first known prime found to exceed one million digits was discovered in 1999, and is a Mersenne prime of the form 2^6972593 - 1; it contains exactly 2 098 960 digits. Subsequently other Mersenne primes, of the form 2^p - 1, have been found which contain more digits.  
However, in 2004 there was found a massive non-Mersenne prime which contains 2 357 207 digits: 28433 x 2^7830457 + 1.  
Find the last ten digits of this prime number._
[(source)](https://projecteuler.net/problem=97)

A possible solution directly derives from modular exponentiation algorithm presented above:

```
2 SPC 7830457 SPC 10000000000 SPC 1 SPC C-u 4 C-M-i C-u 3 C-j % C-u 4 TAB Z{ C-u 3 C-j 0 a= Z/ C-u 3 C-j 2 SPC % 1 a= Z[ C-u 4 C-j * C-j % Z] C-u 3 C-M-i b r C-u 3 TAB C-u 4 C-M-i RET * C-u 3 C-j % C-u 4 TAB Z} TAB DEL TAB DEL TAB DEL 28433 * 1 + 10000000000 %
```

It immediately yields the correct result (voluntarily not shown here).

<!-- 8739992577 -->

## Annex: Emacs functions to quickly test Calc macros in Calc

When the macro is written in any file:
``` emacs-lisp
(defun my/calc-read-macro ()
  "Interpret selected region as a Calc macro and jump to Calc stack, where the macro can be executed by pressing X (2024-01-03, last modification 2024-01-27)."
  (interactive)
  (if (use-region-p)
      (let* ((region-as-string (buffer-substring-no-properties (region-beginning) (region-end))))
        (read-kbd-macro (region-beginning) (region-end))
        (message "Ready to execute (X): %s ... %s"
                 (substring region-as-string 0 10)
                 (substring region-as-string -10 nil))
        (let ((w (get-buffer-window "*Calculator*")))
          (if (null w)
              (message "No window is displaying Calc!")
            (select-window w))))
    (message "No region selected!")))
```

When the macro is written in markdown file:
```emacs-lisp
(defun my/calc-select-markdown-code-and-read-calc-macro ()
  "Select markdown code block surrounding the cursor, interpret it as a Calc macro and jump to Calc stack, where the macro can be executed by pressing X (2024-01-03, last modification 2024-01-27)."
  (interactive)
  (search-backward "```")
  (next-line)
  (beginning-of-line)
  (call-interactively 'set-mark-command)
  (search-forward "```")
  (beginning-of-line)
  (my/calc-read-macro))
```

## (end of file)
