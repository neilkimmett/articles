---
layout: post
title: The Principle of Least Surprise
framework: ""
rating: 0.0
description: ""
---

Unlike other disciplines, where a hard day's work is rewarded with a physical manifestation of your efforts, software is invisible, intangible, and—in many ways—imaginary. Things happen in a millionth of a second, on top of a dozen layers of abstraction. By all accounts, modern software just shouldn't be possible. The fact that any of this works is a miracle. 

It's the least we can do to salvage any scraps of causality we can muster in our own operational domain.

"The Principle of Least Surprise" describes a general approach to software that celebrates sanity over cleverness. Clear intent, reasonable defaults, and providing a sense of control to the developer.

## Side Effects & Unintended Consequences

State is the enemy of logical consistency. To mix idiomatic metaphors, it's the wrench in the machine that gums up the works. Anything a language or framework can do to reduce or the occasion for changing state, the more robust an application built on it will be.

It is therefore useful to make a distinction between methods that change state, "mutators", and those that don't, "accessors". `@synthesize` generates an accessor / mutator pair for `@properties` in the form of a getter and setter.

In most cases, accessors act as wrappers for direct ivar access. Accessors may memoize a computed result, provide a lazily-initialized default value, or keep track of when a value is accessed, but that's about as complicated as they should get.

Mutators range in complexity from directly setting an ivar value to performing a series of destructive actions across a system. Methods that create large amounts of entropy should be wielded with caution. Remember: predictable systems are always preferable to clever ones.

For lack of language features to enforce this distinction, Objective-C must rely on conventions to communicate the difference:

- **Accessors** are named with a _Noun Phrase (NP)_, (e.g., `name`, `attributedString`), with the exception of methods with a `BOOL` return type, which can precede with `is` (e.g. `isDirectory`).
- **Mutators** are named with a _Verb Phrase (VP)_, (e.g. `setName:`, `resetCache`, `performSelector:`.

## Breaches of Contract

This matter of naming conventions as an indicator of method characteristics is a subset of the more general principle of establishing and fulfilling contracts in code.


## Too Clever By Half

The mark of an expert beginner programmer is the desire to be "clever", by invoking obscure design patterns, elaborate meta-programming, or runtime manipulations teetering on the brink of calamity.

In reality, most programs just aren't that interesting. There's no reason to get fancy.

...but of course, everyone would like to think that they are the exception. The sooner you can start thinking of the project in front of you in terms of engineering rather than performance art, the better off you'll be.

Objective-C provides a number of avenues for creative hackery, from method swizzeling and associated objects, to forward invocations and compiler directives. Powerful, though they are, these advanced tricks should be seen as a last resort, reserved for cases when no reasonable alternative exists.

What scares the hell out of veteran programmers is code that messes with underlying system assumptions. Re-ordering internal view hierarchies? Stomping on top of private methods in subclass implementations? Swizzling a base implementation on `NSObject`? This is the stuff of headaches and App Store rejections.

Good code is pretty boring on the atomic level. Don't make things too complicated for yourself (or others). 

* * *

Follow the example of a bistro restaurant: simple dishes with good ingredients, well-executed. Good taste is rooted in restraint.
