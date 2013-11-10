---
layout: post
title: Selectors
framework: ""
rating: 0.0
description: ""
---

The object-oriented paradigm describes programs in terms of objects passing messages back and forth. The original inspiration for this way of thinking about computation was the way biological cells interact with one another, by sending chemically-encoded signals.

In Objective-C, messages are comprised of a selector and a variable number of parameters. When a message is sent, the selector is resolved into the appropriate method call on the receiver dynamically at runtime. This is referred to as "dynamic binding" or "late binding".

What is a selector? In essence, it's little more than a string—the name of the method to be sent to the receiver. 

When compiled into an `SEL` type, the compiler can 


A selector can be created in one of two ways:

At compile time, the `@selector` directive can be used:

~~~{objective-c}
SEL s = @selector(methodName);
~~~

At runtime, a selector can be created from a string with `NSSelectorFromString()`:

~~~{objective-c}
SEL s = NSSelectorFromString(@"methodName");
~~~

## performSelector:

#### Note about the dangers of performSelector after delay

## NSStringFromSelector(@selector) trick for KVC and NSCoding

## NSInvocation

* * *
