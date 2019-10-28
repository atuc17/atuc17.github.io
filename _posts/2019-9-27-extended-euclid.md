---
layout: post
title: "Chapter 2: Extended Euclidean Algorithm"
categories: [cryptography]
---
As I mentioned before, cryptography bases on mathematics. And from now, we will take a deep look in priciple algorithms and number theories, and know how to use it in practice. I know it's kind of boring and difficult =))) but believe me, it's really cool and attracive :v
# 1. Euclidean Algorithm
This is one of the most essential algorithm in not only mathematics but also cryptography. But first, you need to know some definitions in mathematics. For integers *a* and *b*:
- symbol **%**: a % b <=> a mod b, the remainder of division a / b.
- When *b* != 0, we say that *b divides a* if *a = bc* for some integer *c*. We denote this by `b|a`. b is called *divisor* or *factor* of a, and a is called to be *divisible* by b or a *multiple* of b. For example: divisors of 6 are 1, -1, 2, -2, 3, -3, 6 and -6; divisors of 7 is 1 and 7.
- If a number only has 2 divisors (1 and itself), it's called **prime number**. Prime number plays a crucial role in many algorithms and theories. A number is called **composite number** if it isn't a prime number.
- **common divisor** of 2 integers is a number divides both 2 integers. Ex: 3 is a common divisor of 6 and 9, 4 is a common divisor of 8 and 12. **greatest common divisor** of 2 integers *a* and *b*, symbolized by **gcd(a, b)**, will be discussed most.
- **b<>0**: b is not equal to 0.

**Euclidean Algorithm** says that gcd(a, b) = gcd(b, a % b). Therefore, the algorithm could be written as a sequence of equations:

a = q<sub>0</sub>b + r<sub>0</sub>

b = q<sub>1</sub>r<sub>0</sub> + r<sub>1</sub>

r<sub>0</sub> = q<sub>2</sub>r<sub>1</sub> + r<sub>2</sub>

r<sub>1</sub> = q<sub>3</sub>r<sub>2</sub> + r<sub>3</sub>

......

r<sub>k</sub> = q<sub>k+2</sub>r<sub>k+1</sub> ========> remainder equals to 0

What does it means? :))) It means that we keep dividing until the remainder equals to 0, and the result is r<sub>k+1</sub> for some k. When computing in computer, it means gcd(a, b) = gcd(b, r<sub>0</sub>) = gcd(r<sub>0</sub>, r<sub>1</sub>) = gcd(r<sub>1</sub>, r<sub>2</sub>) = ...... = gcd(r<sub>k</sub>, r<sub>k+1</sub>) = r<sub>k+1</sub> or a simple python code: 
```python
def gcd(a, b):
	while b != 0:
		a, b = b, a % b
	return a
```
Proof of this algorithm you can find [here](https://en.wikipedia.org/wiki/Euclidean_algorithm#Proof_of_validity).

### Stop reading and think about this algorithm!!! =))))

And now you know how to compute c = gcd(a, b), but what is it for? =))))

The interesting thing here is that we can find 2 integers *x* and *y* satisfying equation: ax + by = c. This equation is calles Diophantine equation and this helps us solve many problems that will be demonstrated later.

# 2. Extended Euclidean Algorithm
This is an extension of Euclidead Algorithm. For 2 integers a, b and c = gcd(a, b), this algorithm helps us calculate 2 integers x and y satisfying ax + by = c. **NOTE**: given 2 integers a and b, with b != 0, there exist unique integers q, r such that *a = bq + r* and 0 <= r < b. a is called the **dividend**, b is the called **divisor**, q is **quotient** and r is **remainder**. As you can see, in Euclidean Algorithm, in each step we only need the divisor and the remainder of previous step. Now, in extended algorithm, we need to maintain quotient as well.

```
EXTENDED_EUCLID(a,b)
	A1 = 1; A2 = 0; A3 = a;
	B1 = 0; B2 = 1; B3 = b;
	while (B3<>0) AND (B3<>1) do
		Q = A3 div B3;
		R1 = A1 - QB1;
		R2 = A2 - QB2;
		R3 = A3 - QB3; /* A3 mod B3 */
		A1 = B1; A2 = B2; A3 = B3;
		B1 = R1; B2 = R2; B3 = R3;
	end while
	If B3=0 then return A3; no inverse;
	If B3=1 then return 1; B2;
```
This algorithm return: the gcd(a, b) and the inverse of b modulo a.

Let's consider this algorithm. First, we need to initiate A1 = 1, A2 = 0, A3 = a, B1 = 0, B2 = 1 and B3 = b. Before the loop, we have 
``` 
a*A1 + b*A2 = A3 (= a) (1)
a*B1 + b*B2 = B3 (= b) (2)
```
Now look at the loop, at the first step: 
```
a*R1 + b*R2 = a*(A1 - Q*B1) + b*(A2 - Q*B2) 
= a*A1 + b*A2 - Q*(a*B1 + b*B2)
= A3 - Q*B3 ====> original Euclidean algorithm (3)
```
Therefore, equations (1), (2) and (3) are always satisfied, and the third equation gives us the gcd(a, b), and 2 others give us 2 integers x, y that ax + by = gcd(a, b). Proof of algorithm you can find at the link above.

Let's take an example: find x, y that 15x + 8y = 1.

Set A1 = 1, A2 = 0, A3 = 15, B1 = 0, B2 = 1, B3 = 8.
```
First, 15*A1 + 8*A2 = A3 and 15*B1 + 8*B2 = B3.
Step 1: Q = A3 div B3 = 1
	R1 = A1 - Q*B1 = 1
	R2 = A2 - Q*B2 = -1
	R3 = A3 - Q*B3 = 7
	=> A1 = B1 = 0, A2 = B2 = 1, A3 = B3 = 8
	=> B1 = R1 = 1, B2 = R2 = -1, B3 = R3 = 7
Step 2: Q = A3 div B3 = 1
	R1 = A1 - Q*B1 = -1
	R2 = A2 - Q*B2 = 2
	R3 = 1
	=> A1 = B1 = 1, A2 = B2 = -1, A3 = B3 = 7
	=> B1 = R1 = -1, B2 = R2 = 2, B3 = R3 = 1 => B3 == 1, end the loop.
```
`Here, A2 and B2 are x, y. 15*(-1) + 8*2 = 1. =))))) Surprise!!!`
In cryptography, when working with large or super large numbers, we usually let computer calculate for us, and we should know clearly how the algorithm works and initiate on computer :D :D Especially, in the equation `15*(-1) + 8*2 = 1`, if we modulo 2 sides by 8, we have `15*(-1) = 1 mod 8`. This means that -1, or 7, is the inverse of 15 modulo 8. The inversion in modulo n plays an essential role in many algorithm, not only in crytography but also in information theory. Hence, I suggest you consider this algorithm carefully before continuing learning cryptography.

Thanks for reading!!!! Best regard.