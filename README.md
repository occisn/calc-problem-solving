# calc-programming

Hobby project using the macro programming features of stack-based GNU Emacs Calc, leading to esoteric-looking code, to solve numeric puzzles as those proposed by Project Euler or Rosetta code.

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

## How to use the below codes

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

### Primes

```
k f   prime factorization as vector
k n   next prime
k p   is prime? (does not consume element, and print result in echo)
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
v p         ;; pack stack in a vector
v v         ;; reverse vector
v r 1       ;; extract 1st element of vector
v x 20 RET  ;; returns vector [1, ..., 20]
v R ...     ;; reduce vector according to macro '...'
V M ...     ;; map macro '...' to list
v k         ;; add at the beginning of the list (cons)
|           ;; append
```

### Control flow commands

```
Z[ ... Z]         ;; if top element of the stack is non-zero, execute '...'
Z[ ... Z: ... Z]  ;; if top element of the stack is non-zero, execute the first '...', else the second '...' 
Z( ... 1 Z)       ;; 'for' loop (*)
20 Z< ... Z>      ;; repeat '...' 20 times
Z{ ... Z/ ... Z}  ;; repeat until
```
(*) explanations for 'for' loop in exercise 6 of ['Programming tutorial'](https://www.gnu.org/software/emacs/manual/html_mono/calc.html#Programming-Tutorial) part of manual

### Stack manipulation commands

```
RET          (copy) duplicate top element; former stack content moves downward by 1 line
C-u 2 RET    (copy) copy first two elements of the stack
C-j          (copy) element #2 is copied on top of the stack; former stack content moves downward by 1 line
C-u 5 C-j    (copy) element #5 is copied on top of the stack; former stack content moves downward by 1 line
TAB          (move) swap two last elements
C-u 5 TAB    (move downward) element #1 is taken out, elements 2-5 move to the top and become 1-4,
                             and former element #1 is inserted as element #5
C-u 5 C-M-i  (move upward) element #5 is taken out, elements 1-4 move to the bottom and become 2-5, 
                           and former element #5 is inserted as element #1
DEL          (delete) remove top element from the stack
M-DEL        (delete) remove second element from the stack, former lines 3-... move towards the top
C-u 4 M-DEL  (delete) element #4 is deleted, former lines 5-... move towards the top
```

See [relevant entry of the manual](https://www.gnu.org/software/emacs/manual/html_mono/calc.html#Stack-Manipulation).

Derived more sophisticated macros:
```
C-u 3 C-M-i C-u 4 TAB                             ;; swap elements #3 and #4
C-u 4 C-M-i 1 + C-u 4 TAB                         ;; add 1 to element #4
C-u 4 C-M-i + C-u 3 TAB                           ;; consume element at top of the stack
                                                  ;; and add it to initial element #4
C-u 4 C-M-i C-u 2 RET a> Z[ DEL RET Z] C-u 4 TAB  ;; compare element #1 (not consumed) to                                                ;; element #4 of the stack;
                                                  ;; if element #1 > element #4, 
                                                  ;; element #4 is replaced by element #1
```

### Digits 

**Number of digits.** The following macro returns the number of digits of top element of the stack (consumed), through the formula 1 + floor(log10(n)). For instance: 145 --> 3.
```
H L F 1 +
```

**First m digits.** The following macro returns the first m (element #1 of stack, consumed) digits of number n (element #2 of stack, consumed). For instance: 12345678, 3 --> 123.
```
TAB RET H L F 1 + C-u 3 C-M-i - n f S F
```

**Last m digits.** The following macro returns the last m (element #1 of the stack, consumed) digits of number n (element #2 of the stack, consumed). For instance: 123456789, 3 --> 789.
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

**Reverse number.** By successive divisions by 10, the following macro reverses the number which is the first element of the stack (consumed). For instance: 145 --> 541
```
0 TAB Z{ RET 0 a= Z/ RET 10 \ TAB 10 % C-u 3 C-M-i 10 * + TAB Z} DEL
```

**List of digits.** By successive divisions by 10, the following macro returns the list of digits of the first element of the stack (consumed), as a list. For instance: 145 --> [1, 4, 5]
```
[] TAB Z{ RET 10 % RET C-u 4 C-M-i v k C-u 3 TAB - RET 0 a= Z/ 10 \ Z} DEL
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

### Nested 'for' loops
    
The below macro corresponds to nested loops `for n1 from a to b { for n2 from to d ...}`.
```
a SPC b Z( c d Z( C-j
      ;; Stack:
      ;; 3: n1
      ;; 2: n2
      ;; 1: n1

   ;; do something which consumes elements #1 and #2
      
1 Z) DEL 1 Z)
```

## (end of file)
