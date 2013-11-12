---
layout: post
title: Key-Value Coding
framework: ""
rating: 0.0
description: ""
---

Key-Value Coding, or KVC, is a Cocoa technology that allows an object's properties to be accessed indirectly, using strings as identifiers. This functionality is offered through the `NSKeyValueCoding` informal protocol, for which `NSObject` provides a base implementation.

Properties can be accessed with `valueForKey:`:

~~~{objective-c}
[person valueForKey:@"name"];
~~~

They can also be mutated with `setValue:forKey:`:

~~~{objective-c}
[person setValue:@"Diana" forKey:@"name"];
~~~

## Key Paths

## Bulk Access / Assignment

~~~{objective-c}
NSArray *keys = @[@"name", @"price"];
NSDictionary *valuesByKey = [product dictionaryWithValuesForKeys:keys];
~~~

~~~{objective-c}
NSDictionary *updatedValuesByKey = @{
  @"name": "Premium Moustache Wax",
  @"price": @(8.99),
  @"category": @"Ironic Personal Care"
}

[product setValuesForKeysWithDictionary:updatedValuesByKey];
~~~

## Key-Value Coding Compliance

> - Implement a method named -<key>, -is<Key>, or have an instance variable <key> or _<key>.
Although key names frequently begin with a lowercase letter, KVC also supports key names that begin with an uppercase letter, such as URL.
> - If the property is mutable, then it should also implement -set<Key>:.
> -Your implementation of the -set<Key>: method should not perform validation.
> - Your class should implement -validate<Key>:error: if validation is appropriate for the key.

## Validation
