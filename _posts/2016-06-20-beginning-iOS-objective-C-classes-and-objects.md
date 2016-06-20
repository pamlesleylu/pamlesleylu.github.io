---
layout: post
title: "Beginning iOS Programming: Classes and Objects in Objective-C"
tags: [Objective-C, iOS, beginning-ios]
---

I set out wanting to learn iOS mobile programming this year so I started watching the Standford's [Developing iOS 7 Apps for iPhone and iPad](https://itunes.apple.com/en/course/developing-ios-7-apps-for/id733644550). It was in watching the first video of the lecture series that I found out about how unintuitive the Objective-C programming language is (well, at least it is to me). I decided to pick up [Beginning iOS Programming](http://www.wrox.com/WileyCDA/WroxTitle/Beginning-iOS-Programming-Building-and-Deploying-iOS-Applications.productCd-1118841476.html) to complement the lecture seriers. This blog post, along with some other future posts, will serve as notes as I acquire knowledge in the hopes that I would be able to help my future (forgetful) self and others who are starting out with iOS.  

## The Language

There are two languages to choose from to develop iOS mobile applications--Objective-C and Swift. Swift is newer and more intuitive. I decided to use Objective-C because there are still many mobile applications (or even libraries) which were coded using Objective-C. If I were to change any of those in the future, I need to know how to read and manipulate Objective-C programs.

Objective-C is a language and is a superset of C. This means that it can understand basic C plus some other additional constructs. Some of the additional constructs are for giving C an object-oriented flavor.

## Class Declaration

Objects are first-class citizens in Objective-C. A class must be defined to create objects. To create a class, two files must be created--the header file (.h) which contains member variables and method definitions and the implementation file (.m) which contains the implementation of the methods.

An example of a header file called `SimpleClass.h` is as follows:

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

The corresponding implementation `SimpleClass.m` file is as follows:

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
- The `-` symbol is used to declare a method. The return type is followed by the `-` and is enclosed within a pair of parenthisis.
- The method name is declared after the return type declaration.
- The first parameter does not have a name and can follow immediately after the `:` keyword.
- Succeeding parameters are separated by spaces and are in the format *&lt;parameter name when calling the method&gt;*__:____(__*&lt;parameter type&gt;*__)__*&lt;parameter name used within the function&gt;*

Here is the summary of the syntax for method signature (descriptions are enclosed in angled brackets):

`- (<return type>)<method name>:(<type of first parameter>)<parameter name within the method> <parameter name when the method is called>:<parameter name within the method>`


## Object Instantiation

`SimpleClass.h` file:

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

`SimpleClass.m` file:

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
- The `if (!self)` condition is used for defensive programming to check that the object was properly instantiated.

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

The following is an example of creating a `SimpleClass` object using the static method `simpleClassWithFirstInt`:

```objective_c
SimpleClass *simpleClass = [SimpleClass simpleClassWithFirstInt:1 secondInt:2];
```

## References
* [Developing iOS 7 Apps for iPhone and iPad](https://itunes.apple.com/en/course/developing-ios-7-apps-for/id733644550)
* [Beginning iOS Programming](http://www.wrox.com/WileyCDA/WroxTitle/Beginning-iOS-Programming-Building-and-Deploying-iOS-Applications.productCd-1118841476.html)
