---
title: Weak Reference
date: 2017-07-01 18:13:40
tags: Java
---

Weak reference is a kind of reference comparing to strong refernce.

# Strong refernce
A strong reference is just an ordinary Java reference, the kind we use every day. For exmaple:

{% codeblock lang:java %}
String city = "Shanghai";
{% endcodeblock %}

creates a new String and stores a strong reference in the variable city. As the name implies, strong references prevent garbage collector from garbage collecting any object that is reachable via a chain of strong references.

# Weak reference
Imagine you are building a cache for large images, and you want to remove the images no longer needed. This is when weak reference comes in handy. Objects only have weak references to them is candidates of garbage collection. Here's an example of creation/destory of weak refernces:

{% codeblock lang:java %}
Car car = new Car(2000, "silver");
WeakReference<Car> carWeakRef = new WeakReference<>(car);

while(true) {
    if (carWeakRef.get() != null) {
        System.out.println("exist");
    }
    else {
        System.out.println("garbage collected");
        break;
    }
}
{% endcodeblock %}

car will be garbage collected since there's no strong reference refers it starting from the loop.

# WeakHashMap
An implementation of HashMap that the keys are referred to using weak references. If a WeakHashMap key becomes garbage, its entry is removed automatically.

# Soft reference
A soft reference is exactly like a weak reference, except that it is less eager to throw away the object to which it refers. An object which is only weakly referenced will be garbage collected at the next GC cycle, but an object which is softly referenced will generally stick around for a while. In pratice, softly referenced objects are generally retained as long as memory is sufficient. 

# Phantom reference
A phantom reference is very different from either weak reference or soft reference. It's get() method always returns null. The only use of such a reference is keeping track of when it gets enqueued into a ReferenceQueue. Weak references are enqueued as soon as the object to which they refer becomes weakly referenced. This is before finalization or garbage collection has actually happened. Phanom references are enqueued only when the object is physically removed from memory.
So what good are phanom references? One is that it allows you to determine when an object was removed from memory.  
