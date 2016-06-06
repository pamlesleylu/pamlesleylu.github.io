---
layout: post
title: "Beginning iOS Programming: Object Properties in Objective-C"
tags: [Objective-C, iOS]
---

Public properties are generally frowned upon

```objective_c
@interface SimpleClass : NSObject
{
  int _firstInt;
  int _secondInt;
}

@property int firstInt;
@property int secondInt;

@end
```

```objective_c
@implementation SimpleClass

- (void)setFirstInt:(int)firstInt
{
  _firstInt = firstInt;
}

- (int)firstInt
{
  return _firstInt;
}

- (void)setSecondInt:(int)secondInt
{
  _secondInt = secondInt;
}

- (int)getSecondInt
{
  return _secondInt;
}
```

- `@public` keyword is now removed from the property declaration
- `@property` is used to declare the property names that will have the getter and setter functions
- setter function takes on the pattern 'set' + capitalized property name for its method name
- getter function takes on the property name as the method name

This has become a common pattern that Objective-C later on support the `@synthesize` keyword for the creation of object properties. In the latest version of Objctivee-C, even this keyword has been made optional.

The following code is the same as the code above

```objective_c
@interface SimpleClass : NSObject

@property int firstInt;
@property int secondInt;

@end
```

The properties `firstInt` and `secondInt` should be prefixed with `_` when referred to inside the object.

For example:

```objective_c
@interface SimpleClass : NSObject

@property firstInt;
@property secondInt;

- (int)sum;

@end
```

```objective_c
@implementation SimpleClass

- (int)sum
{
  return _firstInt + _secondInt;
}

@end
```

When used outside the class, the getter and setter methods can be used:

```objective_c
SimpleClass *simpleClassInstance = [[SimpleClass alloc] init];

[simpleClassInstance setFirstInt:1];
[simpleClassInstance setSecondInt:2];

int firstIntValue = [simpleClassInstance firstInt];
```

The dot notation was later on introduced to access the properties of objects.

```objective_c
SimpleClass *simpleClassInstance = [[SimpleClass alloc] init];

simpleClassInstance.firstInt = 1;
simpleClassInstance.secondInt =2;

int firstIntValue = simpleClassInstance.firstInt;
```
