---
title: Python Traitlets
date: 2017-07-02 16:41:23
tags: Python
---

[Traitlets](https://github.com/ipython/traitlets) is a pure Python library, and it is a lightweight pure-python alternative of the [traits library](http://code.enthought.com/projects/traits/) . The word traitlet is made from trait and -let. Trait is straightforward, and below is an explanation of -let.
{% blockquote @-let http://www.dictionary.com/browse/-let %}
a diminutive suffix attached to nouns ( booklet; piglet; ringlet), and, by extraction from bracelet, a suffix denoting a band, piece of jewelry, or article of clothing worn on the part of the body specified by the noun ( anklet; wristlet).
{% endblockquote %}   

In short, traitlets let the user define classes that have:
1. Attributes (traits) with type checking and dynamically computed values
2. Traits emit change events when attributes are modified
3. Traitlets perform some validation and allow coercion of new trait values on assignment. They also allow the user to define custom validation logic for attributes based on the value of other attributes.
