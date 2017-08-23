---
layout: note
type: Note
category: Notes
tags: [math]
title: Number Theory
---

Number theory is the study of whole, positive numbers. Much of the concepts behind number theory were a part of my math classes as far back as elementary school. However, number theory is particularly important, especially as it relates to prime numbers, to cryptography. It turns out number theory has a real application in fields outside of math and physics which led me to revisit these more abstract concepts.

<h2>Natural Numbers</h2>
Natural numbers are what is often referred to as the counting numbers. They include 0, 1, 2, 3, ... etc. Though there is some argument on whether or not 0 should be including in this set.

<h2>Negative Numbers</h2>
Obviously, negative numbers include -1, -2, -3, ... etc.

<h2>Rational and Irrational Numbers</h2>
A <i>rational number</i>  is any number that can be represented as the quotient of two integers ($$\frac{x}{y}$$, where x and y are integers). Common rational numbers are fractions like $$\frac{1}{2}$$ or $$\frac{3}{4}$$.

If a real number cannot be expressed as the quotient of two integers, it is called an <i>irrational number</i>. The common example comes from geometry in referring to the ration between a circle's circumference and it's radius (i.e. $$\pi$$). $$\pi$$ is usually approximated to 3.14159 but the decimals continue on with no repeating patterns, making it infinite.

<h2>Real Numbers</h2>
Real numbers are the superset of all rational and irrational numbers. Most numbers that you encounter on a day-to-day basis (unless you are a physicist) are real numbers.

<h2>Imaginary Numbers</h2>
Imaginary numbers were developed to address a flaw in logic that came about in the handling of negative numbers. As everyone knows, when you multiply a negative with a negative, you get a positive ($$-1 \times -1 = 1$$). However, what about the square root of a negative number? If you take the square root of a positive number you get a positive number ($$\sqrt 4 = 2$$ and $$\sqrt 1 = 1$$). If you take the square root of a negative number though you cannot get a negative number because a negative times a negative is always positive. Thus, imaginary numbers are defined as $$i^2 = -1$$ or $$i = \sqrt {-1}$$ to address this fallacy in logic. This allows the square root of any negative number to be expressed as a number multiplied by $$i$$. Any real number times an imaginary number is called a <i>complex number</i>.

<h2>Prime Numbers</h2>
A prime number is a number greater than 1 that has no positive divisors other than itself and 1. Any integer can be written as the product of primes (per the fundamental theoren of arithmetic). Any integers that is not prime is referred to as a composite number. Note that 1 is neither a prime number or a composite number. As proven by Euclid, an infinite number of primes exist. As you progress in order through positive integers, the higher you go, the more sparse primes are. 

Prime numbers are key to cryptographic algorithms such as the RSA. There have been many attempts to create an algorithm guaranteed to produce a prime such as those of Mersenne and Fermat. These alogirthms can be either deterministic (guaranteed to produce a prime) or probablistic (may produce a prime with a given probability). Several "sieves" have been developed to find all prime numbers in a range of integers in polynomial time. The most well known sieve is the Sieve of Eratosthenes.

<h2>Relatively Prime Numbers</h2>
Relatively prime, often called co-prime, numbers are two numbers that have no common divisor between them other than 1. Or rather, m1 and m2 are relatively prime if gcd(m1,m2) = 1. For example, 9 and 14 are co-prime. 9 factors into 1 and 3. 14 factors into 1, 2, and 7. So the gcd(9,14) = 1.

Euler introduced a number he called the totient number $$\varphi(n)$$. It represents that total amount of co-primes of $$n$$ that are less than $$n$$. For any prime number p, the totient of p is p - 1. If you have two prime numbers m and n and you multiple them together to get k then the totient of k is the $$\varphi(m) \times \varphi(n)$$. Or rather $$\varphi(k) = (m - 1)(n - 1)$$, where $$m \times n = k$$ and $$m,n$$ are prime. This is the key concept behind the RSA encryption algorithm which relies on the fact that finding the totient of a prime number is easy and finding the totient of a composite number is hard.

<h2>References</h2>
<a href="/book%20reviews/2017/07/16/moderncryptography/">Eastton, Chuck. <i>Modern Cryptography: Applied Mathematics for Encryption and Information Security</i>. McGraw-Hill Education. 2016.</a>
