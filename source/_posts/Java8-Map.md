---
title: Java8 Map
date: 2017-08-12 15:33:08
tags: Java Java8
---

There're numerous enhancements to Map in Java 8. These enhancements either make Map faster or more convenient to use.

# New API

## getOrDefault
Nothing magical, but long-waited.

{% codeblock lang:java %}
Map<String, Integer> days = new HashMap<>();
days.put("Monday", 1);
System.out.println(days.getOrDefault("Monday", 0));
System.out.println(days.getOrDefault("Tuesday", 0));
{% endcodeblock %}

{% codeblock lang:java %}
1
0
{% endcodeblock %}

## putIfAbsent
Returns old value if absent.
{% codeblock lang:java %}
Map<String, Integer> days = new HashMap<>();
days.putIfAbsent("Monday", 1);
days.putIfAbsent("Monday", 2);
System.out.println(days.get("Monday"));
{% endcodeblock %}

{% codeblock lang:java %}
1
{% endcodeblock %}

## merge
If the key is not present or if the value is null, then adds the key-value pair to the map. If the key is present then replaces the value with the value from the remapping function. If the remapping function returns null then the key is removed from the map.

{% codeblock lang:java %}
Map<String, Integer> days = new HashMap<>();
System.out.println(days.containsKey("Monday"));
days.merge("Monday", 1, (existingValue, newValue) -> existingValue + 1);
System.out.println(days.get("Monday"));
days.merge("Monday", 1, (existingValue, newValue) -> existingValue + 1);
System.out.println(days.get("Monday"));
days.merge("Monday", 1, (existingValue, newValue) -> null);
System.out.println(days.containsKey("Monday"));
{% endcodeblock %}

{% codeblock lang:java %}
false
1
2
false
{% endcodeblock %}

