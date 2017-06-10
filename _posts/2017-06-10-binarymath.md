---
layout: note
type: Notes
title: Binary Arithmetic
---

You can't really "do" computer science without understanding your basic base 2 numbering system.

<h2>Conversion</h2>
Converting a binary number (base 2) to a decimal number (base 10) is pretty straight forward if you know your powers of 2:

<table>
<tr><td><strong>Power of 2</strong></td><td>$2^7$</td><td>$2^6$</td><td>$2^5$</td><td>$2^4$</td><td>$2^3$</td><td>$2^2$</td><td>$2^1$</td><td>$2^0$</td></tr>
<tr><td><strong>Decimal Place Value</strong></td><td>128</td><td>64</td><td>32</td><td>16</td><td>8</td><td>4</td><td>2</td><td>1</td></tr>
<tr><td><strong>Binary Numeral</strong></td><td>1</td><td>0</td><td>0</td><td>1</td><td>1</td><td>1</td><td>0</td><td>1</td></tr>
</table>
Decimal Value: $128 + 16 + 8 + 4 + 1 = 157$

<h2>Binary Operations</h2>
<h3 style="font-size: 120%">Binary AND</h3>
Binary AND operations work the same way as Boolean AND operations (duh). It combines two binary numbers, 1 bit at a time, AND'ing each bit. A 1 is treated as "True" in Boolean and 0 as False. So anytime there is a 0 in an AND operation the result is always 0. Only 1 $\land$ 1 = 1.

AND'ing anything with a string of 1's will always be the original binary number:
<table style="width: auto">
<tr><td></td><td>110100</td></tr>
<tr><td style="text-align: right">$\land$</td><td>111111</td></tr>
<tr><td style="text-align: right">$=$</td><td>110100</td></tr>
</table>

AND'ing anything with a string of 0's will always result in a string of 0's (set to null):
<table style="width: auto">
<tr><td></td><td>110100</td></tr>
<tr><td style="text-align: right">$\land$</td><td>000000</td></tr>
<tr><td style="text-align: right">$=$</td><td>000000</td></tr>
</table>

<h3 style="font-size: 120%">Binary OR</h3>
Binary OR operatios also act the same as Boolean OR operations (doi). It acts the opposite of AND: Anytime there is a 1 in an OR operation the result is 1. Only 0 $\lor$  0 = 0.

OR'ing anything with a string of 1's will always be a string of 1's:
<table style="width: auto">
<tr><td></td><td>110100</td></tr>
<tr><td style="text-align: right">$\lor$</td><td>111111</td></tr>
<tr><td style="text-align: right">$=$</td><td>111111</td></tr>
</table>

OR'ing anything with a string of 0's will always be the original binary number:
<table style="width: auto">
<tr><td></td><td>110100</td></tr>
<tr><td style="text-align: right">$\lor$</td><td>000000</td></tr>
<tr><td style="text-align: right">$=$</td><td>110100</td></tr>
</table>

<h3 style="font-size: 120%">Binary XOR</h3>
XOR is far more interesting than AND and OR. It only returns 1 if there is a 1 in one of the two operators and not in the other. So if the two bits do not match a 1 is returned and if they do, then a 0 is returned.

XOR'ing anything with a string of 1's will flip all the bits:
<table style="width: auto">
<tr><td></td><td>110100</td></tr>
<tr><td style="text-align: right">$\oplus$</td><td>111111</td></tr>
<tr><td style="text-align: right">$=$</td><td>001011</td></tr>
</table>

XOR'ing anything with a string of 0's will always be the original binary number:
<table style="width: auto">
<tr><td></td><td>110100</td></tr>
<tr><td style="text-align: right">$\oplus$</td><td>000000</td></tr>
<tr><td style="text-align: right">$=$</td><td>110100</td></tr>
</table>

One other interesting characteristic of XOR is that it is reversible. So if you take the result of the first XOR and XOR it with the second number in the original operation, you will get the first number:
<table style="width: auto">
<tr><td></td><td>001011</td></tr>
<tr><td style="text-align: right">$\oplus$</td><td>111111</td></tr>
<tr><td style="text-align: right">$=$</td><td>110100</td></tr>
</table>

<span class="notation"><strong>Note:</strong> <br/>
Bit-level XOR produces exactly the same results as doing addition mod 2:<br/>
$$( a + b )$$ mod $$2 = a \oplus b$$
</span>
