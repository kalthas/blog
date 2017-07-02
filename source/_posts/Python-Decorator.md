---
title: Python Decorator
date: 2017-07-02 15:00:05
tags: Python
---

{% blockquote @PEP318 https://wiki.python.org/moin/PythonDecorators %}
A Python decorator is a specific change to the Python syntax that allows us to more conveniently alter functions and methods. This supports more readable applications of the DecoratorPattern but also other uses as well.
{% endblockquote %}   


Python decorator is straightforward to understand. Note that it's also possible to decorate whole class, like in below example:
{% codeblock lang:python %}
def cls_decorator(cls):
    class Wrapper(object):
        def __init__(self, *args):
            self.wrapped = cls(*args)
        def __getattr__(self, name):
            print('Property {}.{}:'.format(self.wrapped, name))
            return getattr(self.wrapped, name)
    return Wrapper

@cls_decorator
class Target(object):
    def __init__(self, x, y):
        self.x = x
        self.y = y

ins = Target('a', 1)
print(ins.x)
print(ins.y)
{% endcodeblock %}

Output:
{% codeblock %}
Property <__main__.Target object at 0x10b8ebdd8>.x:
a
Property <__main__.Target object at 0x10b8ebdd8>.y:
1
{% endcodeblock %}
