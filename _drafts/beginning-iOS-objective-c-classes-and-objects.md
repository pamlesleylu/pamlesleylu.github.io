---
layout: post
title: "Beginning iOS Programming: Classes and Objects in Objective-C"
tags: [Objective-C, iOS]
---

Started with the first video lecture of Standford's [Developing iOS 7 Apps for iPhone and iPad](https://itunes.apple.com/en/course/developing-ios-7-apps-for/id733644550).
I find the syntax to be counter-intuitive. The colon (:) has different meanings on different contexts. The dash (-) is used in place of the function keyword.
I wondered if I should learn swift instead. There are still a lot of applications written in Objective-C. Besides knowing Objective-C might make me appreciate Swift more.

Objective-C is the language
Cocoa Touch is the API, frameworks and user interface libraries used to create iOS applications.
Objective-C is a superset of C that adds object-oriented programming methodologies.

## Class Declaration

Objects are first-class citizens in Objective-C.
A class must be defined to create objects. The create a class, two files must be created--the header file (.h) which contains member variables and method definitions and the implementation file (.m) which contains the implementation of the methods.

In the SimpleClass.h file:

```objective_c
@interface SimpleClass : NSObject
{
  @public
  int firstInt;
  int secondInt;
}

- (int)sum;
- (int)sumWithThirdInt:(int)thirdInt fourthInt:(int)fourthInt;
@end
```

In the SimpleClass.m file:

```objective_c
#import "SimpleClass.h"

@implementation SimpleClass

- (int)sum
{
  return firstInt + secondInt;
}

- (int)sumWithThirdInt:(int)thirdIntValue fourthInt:(int)fourthIntValue
{
  return firstInt + secondInt + thirdIntValue + fourthIntValue;
}

- (int)sub
{
  return firstInt - secondInt;
}

@end
```

- There are no namespaces or packages in Objective-C
- `@<start>` and `@end` symbols are demarcation for the compiler to start using Objective-C compiler instead of pure C compiler.
- All objects must have a parent. There is no implicit parent for Objective-C and the parent class must always be declared. Most objects extend from NSObject. In Objective-C, how the object is managed in memory is determined by the parent object.
- Methods that you want to be available to other classes must be declared in the header file. The methods in the implementation file only are private within the class.
- The `-` symbol is used to declare a method. The return type is followed by the `-` and is enclosed within a parenthisis.
- The method is name is declared after the return type declaration.
- The first parameter does not have a name and can follow immediately after the `:` after the method name.
- Succeeding parameters are separated by spaces and are in the format *&lt;parameter name when calling the method&gt;*__:____(__*&lt;parameter type&gt;*__)__*&lt;parameter name used within the function&gt;*

Here is the summary of the syntax for method signature (descriptions are enclosed in angled brackets):

`- (<return type>)<method name>:(<type of first parameter>)<parameter name within the method> <parameter name when the method is called>:<parameter name within the method>`


## Object Instantiation

In the header file:

```objective_c
@interface SimpleClass : NSObject
{
  @public
  int firstInt;
  int secondInt;
}

- (id)initWithFirstInt:(int)firstIntValue secondInt:(int)secondIntValue;
@end
```

In the implementation file:

```objective_c
@implementation SimpleClass

- (id)initWithFirstInt:(int)firstIntValue secondInt:(int)secondIntValue
{
  self = [super init];
  if (!self)
    return nil;

  firstInt = firstIntValue;
  secondInt = secondIntValue;

  return self;
}

SimpleClass *aSimpleClassInstance = [[SimpleClass alloc] initWithFirstInt:1 secondInt:2];
@end
```

- Method calls in Objective-C are called message passing
  - To pass a message to an object or class, the message must be enclosed in square brackets ([]). In the message passing of `[SimpleClass alloc]`. The `alloc` method of SimpleClass (inherited from the `NSObject`) is called.
  - Use the `:` to pass the parameter. Use space and the parameter name declared in the method signature to pass in the second parameter. In `[[SimpleClass alloc] initWithFirstInt:1 secondInt:2]`, after the class is returned from the call to `alloc`, the `initWithFirstInt` method is called. The first parameter is just passed after the :. The name of the second parameter, which is `secondInt`, is used to pass in the value of the 2nd parameter.
- To instantiate an object in Objective-C, `alloc` must be called first to allocate memory for the object. `init` is called to initialize the object.
- The `(id)` type can hold a pointer to any object. `initWithFirstInt` can be used to initialize an object with specific parameters.
- NSObject's `init` method can be called by using the `super` keyword.
- The `if (!self)` condition is used for defensive programming.

## Factory Methods
Static methods can be defined to create and instantiate an object.

In the header file:

```objective_c
@interface SimpleClass : NSObject
{
  @public
  int firstInt;
  int secondInt;
}

+ (id)simpleClassWithFirstInt:(int)firstIntValue secondInt:(int)secondIntValue;

@end
```

In the implementation file:

```objective_c
@implementation SimpleClass

+ (id)simpleClassWithFirstInt:(int)firstIntValue secondInt:(int)secondIntValue
{
  SimpleClass *simpleClass = [[SimpleClass alloc] init];
  simpleClass->firstInt = firstIntValue;
  simpleClass->secondInt = secondIntValue;

  return simpleClass;
}
@end
```

- + is used to declare a static method
- -> is used to refer to a public parameter of an object

To create an instance

```objective_c
SimpleClass *simpleClass = [SimpleClass simpleClassWithFirstInt:1 secondInt:2];
```

## References
* [Developing iOS 7 Apps for iPhone and iPad](https://itunes.apple.com/en/course/developing-ios-7-apps-for/id733644550)
