---
layout: post
title: "Beginning iOS Programming: Object Properties in Objective-C"
tags: [Objective-C, iOS, beginning-ios]
---

_Checkcout [previous post](http://pamlesleylu.github.io/2016/06/20/beginning-iOS-objective-C-classes-and-objects/) for the basics of interfaces, classes, and methods._

## Instance Variables + Getter and Setter Methods

In object-oriented languages, classes can have both state and behavior. Behaviors are expressed through class methods, while a class' state is stored in its properties. Properties in Objective-C can be declared by using the `@property` keyword. Here is how properties can be declared:


```objective_c
@interface SimpleClass : NSObject
{
  int _firstInt;
  int _secondInt;
}

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

- (int)secondInt
{
  return _secondInt;
}
```

- `@property` is used to declare a property
- setter function takes on the pattern 'set' + capitalized property name for its method name
- getter function takes on the property name as the method name

## Object Properties

Public properties are generally frowned upon as they break the principle of encapsulation. This is the reason why properties should be accessed via their getters or setters. This has become such as common pattern in Objective-C that you can just use the `@property` keyword and the instance variable and the getter and setter methods will be automatically be created by the compiler.

```objective_c
@interface SimpleClass : NSObject

@property int firstInt;
@property int secondInt;

@end
```

- instance variables will be created with the prefix '_'
- getters will have names the same the those declared with the `@property` keyword
- setters will have names set + capitalized property name

## Dot Notation for Accessor Method Calls

Accessor method calls can be called via the dot notation.

```objective_c
SimpleClass *simpleClassInstance = [[SimpleClass alloc] init];

[simpleClassInstance setFirstInt:1];
[simpleClassInstance setSecondInt:2];

int firstIntValue = [simpleClassInstance firstInt];
```

```objective_c
SimpleClass *simpleClassInstance = [[SimpleClass alloc] init];

simpleClassInstance.firstInt = 1;
simpleClassInstance.secondInt =2;

int firstIntValue = simpleClassInstance.firstInt;
```

- To use the dot notation, just use the syntax object instance name + '.' + property name
- When the dot notation is used, the setter or getter method is automatically called

## Instance Variable Within Initializer Methods

When referring to properties within a method of the same class, it is advisable to use the getter and setter methods. Instance variables can be referred to directly within initialization methods. For example:

```objective_c
@interface SimpleClass : NSObject

@property firstInt;
@property secondInt;

- (int)sum;

@end
```

```objective_c
@implementation SimpleClass

- (id)init
{
  self = [super init];

  if (self)
  {
    // do something
  }

  return self;
}

- (int)sum
{
  return self.firstInt + self.secondInt;
}

@end
```

## Changing Accessor Method Name

The `@synthesize` keyword can be used to change which instance is referred to by the property getter and setter.

```objective_c
@implementation SimpleClass

  @synthesize propertyName = instanceName;

@end

## Pointers as Properties

So far, we've only looked at examples of properties with simple types. Properties can refer to other more complex types like classes. There are just some things that must be specified when a property is a pointer to other objects.
- **strong vs. weak** - tell compiler if your object is the owner of the other object
- **atomic vs. nonatomic** - how value should be set and retrieved in threaded environment

```objective_c
@interface SimpleClass

@property (atomic, strong) SecondClass *secondClassInstance;
@property (nonatomic, weak) SecondClass *secondClassInstance2;

@end
```

### atomic vs. nonatomic

Properties are `atomic` by default. This means that their getters and setters are synchronized such that their values are set or retrieved fully when accessed simultaneously by different threads. Atomic properties carry extra overhead and do not guarantee thread-safety. Using proper locks is more important for ensuring thread-safety. Most often, the `nonatomic` is used to avoid the extra overhead.

### strong vs. weak

The `strong` pointer means that your object owns the other object. This disallows the object from being deallocated as long as your object is alive. A `weak` object property can be deallocated when there are no other objects that have a strong pointer to it. Weak properties are used to avoid the circular reference between 2 objects that have instances of each other (i.e., they will never be deallocated if both of them are `strong`).

## References
* [Developing iOS 7 Apps for iPhone and iPad](https://itunes.apple.com/en/course/developing-ios-7-apps-for/id733644550)
* [Beginning iOS Programming](http://www.wrox.com/WileyCDA/WroxTitle/Beginning-iOS-Programming-Building-and-Deploying-iOS-Applications.productCd-1118841476.html)
