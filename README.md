#Inheritance

##What is Inheritance?
Inheritance is the concept of having one class that is based on another pre-existing class (known as the *parent* or *super* class). Conversely, this *child* class has all of the properties and methods its parent has because the child **inherits** them from it. This is also called making a *subclass*. 

**Subclassing** (making a subclass) allows us to add more specific properties and methods on top of a more general base class. We can also **override** pre-existing properties or method to better suit the subclass. This allows us to specialize classes based on our needs.

For example, we could start with a `Dog` class, but eventually subclass it into `GoldenRetriever`, `GreatDane`, and `Yorkie`, each of which has its own characteristics (properties) and abilities (methods) on top of what all `Dog`s share in common. They all know the `bark` method (prints `@"woof!"`) because it is defined in the `Dog` class that they all inherit from. However, the `Yorkie` class has its own definition for `bark` (prints `@"yip!"`), which *overrides* the `bark` method it inherited. 

##Why Is This Useful?
Inheritance allows us to highly organize our classes and avoid duplicating code. It basically allows us to define a first class, then define new classes from the perspective "this will be the first class *and*..." or "same as the first class, *but*...". It also helps us break down complex objects into basic parts, making them easier to conceptualize and write. 

Imagine you were making a slew of car-based classes and were trying to be accurate when specifying the model. You can include everything they share in the `Car` super class and specify what each of their unique properties are in their respective subclass. For example, `hasAllWheelDrive == YES` for a `MercedesBenz` whereas `hasAllWheelDrive == NO` for a `HondaCivic`. A `MercedesBenz` could also have a `sunroof` property whereas some other subclasses would not.

All of these things said, **it's not always necessary to make a parent for your classes** to share; its common that classes don't have enough in common to warrant a super class. Inheritance is a tool, not a rule. 

##How Does A Class Inherit?
In any class's **header** file (*YourCustomClass*.h) there is a **@interface** for the class. It follows the format `@interface YourCustomClass : AParentClass`, signifying that `YourCustomClass` inherits from `AParentClass`. Open any .h file in xcode and you should be able to find something like this:

```objective-c
// Dog.h
@interface Dog : NSObject

@property (strong, nonatomic) NSString *name;
- (void) bark;

@end
```
This is the public interface for the `Dog` class — since its in the .h, any methods and properties listed here are considered **public** (*more on public vs. private later*). More importantly, note that the class inherits from `NSObject`, which you can consider the basic template for all objects since almost every single object eventually inherits from it. **Custom classes you make "from scratch" should subclass** `NSObject`. 

Here's what a header file for `GoldenRetriever` (a subclass of `Dog`) would look like: 

```obj-c
// GoldenRetriever.h
#import Dog.h

@interface GoldenRetriever : Dog

@property (strong, nonatomic) DogToy *favoriteToy;
- (DogToy *) fetch; 

@ end
```
First, we have to `#import Dog.h`, which gives this class access to the `Dog` class. It has a new property and method, as well as the ones it inherits — we can see that it inherits from `Dog`, which means it includes everything that we saw listed in 'Dog.h'. This inheritance can also be seen as a chain: *think `GoldenRetriever` is a `Dog` which is an `NSObject`*. (And remember, almost everything is ultimately an `NSObject`).

##Overriding Methods

In order to change what the `bark` method that all `Dog` subclasses inherit actually does, the subclass must **override** that method. To do this in Objective-C, you just simply rewrite it! So we could have the following definition in our `Yorkie` class:

```objc
// Yorkie.m

- (void)bark {
	NSLog(@"Yip!");
}
```
Now no matter what `bark` from a parent class would do, all instances of the `Yorkie` class do this instead.

##Initializers with Inheritance (Advanced)

Now we know that the methods of a super class are available in any of its subclasses, and that includes initializers. However, there is a correct and an incorrect way to write additional convenience initializers in a subclass.

Let's take an example of an incorrectly created convenience initializer in a subclass.

First, the superclass initializers:

######Example
```objc

//Animal.m

- (instancetype)initWithName:(NSString *)name {
	
	self = [super init];

	if (self) {
		_name = name;
	}

	return self;
}

- (instancetype)init {
	
	return [self initWithName:@""];
}

```

And the less perfect subclass initializer (remember, `Raccoon` inherits from `Animal`):

```objc

//Raccoon.m

- (instancetype)initWithName:(NSString *)name stripeColor:(UIColor*) stripeColor {
	
	self = [super init];

	if (self) {

		_name = name;
		_stripeColor = stripeColor;
	}

	return self;
}

```

The issue with this `Racoon` convenience initializer is that it does two things: 
   
   1. When we `[super init]` in `Racoon`, we're calling the default initializer from `Animal`, which consequently calls the `initWithName:` convenience initializer — setting the name of the `Raccoon` to be `@""`, an empty `NSString`. 
   2. Then it comes back to our `initWithName:stripeColor:` method and replaces the values for `_name` and `_stripeColor` to the values passed into the method call. **This is more code running than should be.** 

Instead, we should call the designated initializer of our super class, like so:

######Example

```objc

//Raccoon.m

- (instancetype)initWithName:(NSString *)name stripeColor:(UIColor*)stripeColor {
	
	self = [super initWithName:name];

	if (self) {
		_stripeColor = stripeColor;
	}

	return self;
}
```
Now we have avoided unnecessary calls to the default initializer of our superclass (or any convenience initializers that are not the designated initializer).


