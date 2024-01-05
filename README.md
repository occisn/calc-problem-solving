# calc-programming

Hobby project using the macro programming features of stack-based GNU Emacs Calc, leading to esoteric-looking code, to solve numeric puzzles as those proposed by Project Euler or Rosetta code.

_All information and codes are included in the present README file._

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
                      where n is the top element of the stak
```

For basic stack manipulation commands, see [relevant entry of the manual](https://www.gnu.org/software/emacs/manual/html_mono/calc.html#Stack-Manipulation).
On the use of `~`, see the section of the manual on [prefix argument](https://www.gnu.org/software/emacs/manual/html_node/calc/Prefix-Arguments.html).

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

## Project Euler 1 (multiples of 3 or 5)

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

## Project Euler 2 (even Fibonacci numbers)

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

## Project Euler 3 (largest prime factor)

_The prime factors of 13195 are 5, 7, 13 and 29. What is the largest prime factor of the number 600851475143?_ [(source)](https://projecteuler.net/problem=3)

We use the macro seen above to calculate the largest prime factor of a number.
```
600851475143 k f v v v r 1
```

It yields the correct result (voluntarily not shown here).

<!--- 6857 --->

## Project Euler 4 (largest palindrome product)

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

## Project Euler 5 (smallest multiple)


_2520 is the smallest number that can be divided by each of the numbers from 1 to 10 without any remainder. What is the smallest positive number that is evenly divisible by all of the numbers from 1 to 20?_ [(source)](https://projecteuler.net/problem=5)

```
v x 20 RET       ;; iota: returns [1, ..., 20]
v R k l          ;; reduce with 'k l' i.e. reduce with lcm
```

It yields the correct result (voluntarily not shown here).

<!--- 232792560 --->

## Project Euler 7 (10 001st prime)

_By listing the first six prime numbers: 2, 3, 5, 7, 11, and 13, we can see that the 6th prime is 13.
What is the 10 001st prime number?_ [(source)](https://projecteuler.net/problem=7)

Let's take advantage of `k n` which returns next prime.

```
2 SPC                       ;; first prime
10001 SPC 1 - Z< k n Z>     ;; repeat 10000 times 'k n', i.e. 'next prime'
```

It yields the correct result (voluntarily not shown here).

<!--- 104743 --->

## Annex: Emacs functions to quickly test Calc macros in Calc

When the macro is written in any file:
``` emacs-lisp
(defun my/calc-read-macro ()
  "Interpret selected region as a Calc macro and jump to Calc stack, where the macro can be executed by pressing X (2024-01-03)."
  (interactive)
  (if (use-region-p)
      (progn
        (read-kbd-macro (region-beginning) (region-end))
        (let ((w (get-buffer-window "*Calculator*")))
          (if (null w)
              (message "No window is displaying Calc!")
            (select-window w))))
    (message "No region selected!")))
```

When the macro is written in markdown file:
```emacs-lisp
(defun my/calc-select-markdown-code-and-read-calc-macro ()
  "Select markdown code block surrounding the cursor, interpret it as a Calc macro and jump to Calc stack, where the macro can be executed by pressing X (2024-01-03)."
  (interactive)
  (search-backward "```")
  (next-line)
  (call-interactively 'set-mark-command)
  (search-forward "```")
  (beginning-of-line)
  (my/calc-read-macro))
  ```

## (end of file)
