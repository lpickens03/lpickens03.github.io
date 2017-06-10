---
layout: note
type: Notes
title: Common Math Formulas
---

A collection of various math formulas I commonly encounter for quick reference. 
<br/><br/>

<h2>Modular Arithmetic</h2>
Suppose $$a$$ and $$b$$ are integers and $$m$$ is a positive integer. Then $$a \equiv b($$mod $$m)$$ if $$m$$ divides $$b - a$$. The phrase $$a \equiv b($$mod $$m)$$ is called a <i>congruence</i> and is read "$$a$$ is congruent to $$b$$ modulo $$m$$". The $$m$$ integer is called the <i>modulus</i>.

We use the notation $$a$$ mod $$m$$ (without paranthesis) to denote the remainder when $$a$$ is divided by $$m$$.

$$a \equiv b($$mod $$m)$$ if and only if $$a$$ mod $$m = b$$ mod $$m$$.

Examples:
<ul>
<li>101 mod 7 = 3, where 101 = 7 x 14 + 3</li>
<li>-101 mod 7 = 4, where 101 = 7 x -15 + 4</li>
</ul>

<h3 style="font-size: 120%">Addition and Multiplication in Modular Arithmentic</h3>
11 x 13 in modulo 16 is taking 11 x 13 = 143 and reducing by modulo 16. So 143 = 8 x 16 + 15 so 143 mod 16 = 15. A similar method is used for addition.

<br/>
<h2>Binary Arithmetic</h2>
See <a href="/2017/06/10/binarymath/">here</a>.

<br/>
<h2>Binomial Coefficient</h2>
Denotes the number of ways of choosing a subset of $$k$$ objects from a set of $$n$$ objects:<br/>
$$\binom{n}{k} = n!/(k!(n-k)!)$$

<br/>
<h2>Probability</h2>
See <a href="/2017/06/10/probability/">here</a>.