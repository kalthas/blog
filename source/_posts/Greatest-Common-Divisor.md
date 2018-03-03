---
title: Greatest Common Divisor
date: 2018-03-03 21:30:31
tags: Math
---

In mathmatics, the greatest common divisor (gcd) of two or more integers is the largest positive integre that divides both. E.g. gcd(4,6) is 2.

There is a very simple and straitforward way to calculate gcd of any given integres called [Euclidian Algorithm](https://en.wikipedia.org/wiki/Euclidean_algorithm)

{% codeblock lang:scala %}
def gcd(a: Int, b: Int): Int = if (b==0) a else gcd(b, a%b)
{% endcodeblock %}

{% codeblock lang:scala %}
gcd(0, 10)
gcd(20, 10)
{% endcodeblock %}
yields
{% codeblock lang:scala %}
10
10
{% endcodeblock %}

For three integress, we can apply 
gcd(a, b, c) = gcd(a, gcd(b, c)) = gcd(gcd(a, b), c) = gcd(gcd(a, c), b)
So actually eculidian algorithm can be leveraged to calculate greatest common divisor for any number of integres.
