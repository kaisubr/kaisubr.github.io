---
layout: post
category: blog
title: Integer Representation
---

<!-- # integer representation via bases -->

A binary number of base 2 (i.e. *radix* 2) is a number where each digit is zero or one. 

* Each `0` or `1` according to a computer is a *bit*. 
* A byte is a group of 8 bits. 
* It should also be noted that grouping bits into groups of $p$ allows easy conversion to numbers of radix $2^p$. (You take the group, convert to decimal, then convert to radix $2^p$).

Denotation: `0b` preceding it, or subscript 2 after it:
$$
\text{0b1011} = 1011_2
$$
Similarly, a number of base $r$ is a number with digits from 0 to $r$. Once we pass digit 9, we resort to alphabets: a number of base 16 (hexadecimal) has digits 0 to 9, then A to F.

Padding zeroes to the front of a number typically does not change its value.

## introduction

Let's expand from base 2 to base 10. Simply take the bit at each index as $\text{bit}*2^{\text{index}}$, starting from the right-most bit.
$$
1011_2 = 1(2^0) + 1(2^1) + 0(2^2) + 1(2^3) = 11_{10}
$$

## conversion

We can convert between numbers of base $r$ to base $n$:

### when $r,n > 0$

When we convert decimal to binary, we are essentially looking at the way to split a number $x$ of base 10 to fit some combination of numbers in $2^n$. The best way is to start with $2^n$ closest to $x$, ensuring we get the ordering right. 

Example: For $11_{10}$ we can start with $n=3$ for $2^3$, leaving a remainder of $11-8=3$, which can be removed from $2^1$, leaving a remainder of 1, which can be removed from $2^0$, leaving a remainder of $0$.

$$
11_{10} = 8 + 2 + 1 = 1(2^3) + 0(2^2) + 1(2^1) + 1(2^0) = 1011_2
$$

We can convert binary to decimal (see expansion), and also convert binary to other radices, like octal. 

*In general*:
 
* When we convert from smaller base to a larger one, i.e.: $\text{#}_r = \text{?}_n \text{ for } r < n$ , we find that $\text{#}$ can be grouped into groups of $p$ units for $r^p = n$.  Intuitively, the smaller base $r$ has more digits, so we group those digits up and convert each grouping to a base of $n$.
  * Consider converting $1011_2$ to an octal representation. 
    * Here, the octal representation should have fewer digits. The grouping is 3. Since the number of bits aren't divisible by 3, we can pad them as: 
    * $001\text{ }011_2$ so each grouping would be converted into individual units $001_{2} =$ $1 +$ $1_8$ and $001_{2} =$ $3 +$ $3_8$, thus the octal representation would be $13_8$.
* When we convert from larger base to a smaller one, $r > n$, we find that each individual digit in the number of base $r$ is converted. Intuitively, the larger base $r$ has fewer digits, so we take those digits individually and expand them to groups (size $p$, when $n^p=r$) of base $n$.
  * Consider converting $1234_{9}$ to a representation in base 3. 
    * We inspect that the grouping size is 2. Since we convert to a smaller base, we expect more numbers, so intuitively, we can take individual digits and convert to base 3 of unit size 2. So: $4_9\rightarrow11_3$,  $3_9\rightarrow10_3$, $2_9\rightarrow02_3$, and $1_9\rightarrow01_3$. Then the base 3 representation is $01021011_3$, or without the padding: $1021011_3$.
* If a grouping size can't be determined, we can always convert to an easier base to work with, such as base 10, 8, or 16, then convert from there.
  * Consider converting $1414_{8}$ to a hexadecimal representation.
    * For a hexadecimal ("hexa" for $6$ and "decimal" for $10$ means the radix is $16$) representation in base 16, we could convert base 8 to base 2, then convert base 2 to base 16. Following this process, we show $1414_8 = 001100001100_2 \text{ for } p=3$, and $001100001100_2=30\text{C}_{16} \text{ for } p=4$.

### what about a negative radix?

Let's use the same algorithm mentioned above.

* Consider converting $11_{10}$ to radix $-2$. We notice that the positions alternate in signs, $...64,-32,16,-8,4,-2,1$. We can quickly express something like $+16 $, but if we want to express in the opposite sign, such as $+32$, we would need to take both $64$ and $-32$.
  * So, in general, think about what you want to express in the positive radix. That means we want to try and find 8, 2, and 1.
  * $8$ can be shown by $16 + -8$, and when we start from 11 we get remainder 3.
    $2$ can be shown by $4 + -2$, starting from 3 we get remainder 1.
    $1$ is shown by $(-2)^0$. This is quickly expressed.
  * Thus we show $11_{10} = 11111_{-2}$.

### by summation

Consider the integer $a_{n}$ that we convert to base $r$. This conversion has unique string representation:

$$
a = \sum_{i=0}^{k}{d_i(r)^i}
$$

$d_i$ represents the digit at index $i$ (i.e. the bit at index $i$) starting from the right-most digit, and $0 \le d_i <r-1$. The string would be the linear combination (coefficients $d_i$) of $r^{k}$. (The summation goes from $i=0$ to $k$ inclusive.)

$$
a = d_0 + d_1(r)^1 + d_2(r)^2 + \text{ ... } + d_{k-1}(r)^{k-1} + d_k(r)^k
$$

When given a negative base, you would instead consider $(-r)^k$.

### the number $a = 0$

Can we show that 0 has a unique representation in radix $r$ and $-r$? 

Let us consider $r=2$. Intuitively we produce, $0 =0_2$. From summation, we produce $0 = d_0(2^0) + d_1(2^1)+d_2(2^2)$. This is possible only for $d_0,d_1,d_2=0$ when $0 \le d_0,d_1,d_2 < 2$. From the mathematical perspective we can notice the one-dimensional vectors resemble linear independence. 

Could this be proved by induction: the <u>base case</u> being $r = 0$ producing $a = 0$ (see next section); <u>assuming</u> $r = k$ produces $a = 0$ and <u>showing</u> $r = k+1$ produces $a = 0$ as $r \in \Bbb \{0, R^+\}$. Then, we could try the same for $-r$.

### when $r=0$ 

The only number we can represent in base 0 is the number $a=0_{n}$. In base $r=0$ that would be $a=0_n=0_r$ because, by the definition through summation,  when we let $r=0$: 

$$
a = \sum_{i=0}^{k}{d_i(0)^i} = \sum_{i=0}^{k}{0} = 0 + 0 + \text{ ... } + 0 = 0
$$


## addition

The addition of two binary numbers $a$ and $b$ considers their individual bits, where we compute $d_i(a) + d_i(b) = v_i + 2c_{i+1}$. Here, we let the function $d_i(x)$ represent the unit of $x$ at index $i$. We are interested in $v_i$, the value we place at index $i$, and the carried value $c_{i+1}$ that we put over index $i+1$. We can always pad $0$ to support undefined indices at $i+1$.

In general, we add the two values at the index $i$ through a useful base, like decimal, convert it to the original $r$-base notation, keep the right-most unit, and carry the rest. 

* In base 2, we find that there are few possibilities.
  * $1 + 1$ yields 0, then carrying a 1, because the binary representation is $10_2$.
  * $1 + 0$ or $0 + 1$ yields 1.
  * $0 + 0$ yields 0.
  * $1 + 1 + 1$ yields 1, then carrying a 1, because the binary representation is $11_2$. 

## multiplication

When we multiply binary numbers $a$ and $b$, we treat it like normal long multiplication, which includes the sum of partial products between the bits in $a$ and $b$. 

* In base 2, there are few possibilities for multiplying bits $0$ and $1$.
  * $0 * 1$ yields 0
  * $0 * 0$ yields 0
  * $1 * 1$ yields 1
* The sum of partial products follows addition rules mentioned in the section above.



