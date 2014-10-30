#Inheritance

##Classical inheritance

Inheritance is the idea that one class can be derived from another, pre-existing "base" class, "super" class, or "parent" class (alternative names for the same thing). By "derived", we mean that a class shares the vast majority of the properties and behavior of another class, and can be described using the "is a" language. (e.g. A `SportsCar` is an `Automobile`; an `Automobile` is a `Vehicle`). A class that inherits from another class usually has added properties and behavior, and potentially "overrides" some of its base class' behavior. In fact, properly constructed base classes will often have "abstract" behaviors to be necessarily "overridden" with implementation by their "subclass" or "child" class.

A lot of inheritance vocabulary has been introduced in the above paragraph, so let's go through it all in more detail, via example.

A classic example of inheritance used in programming courses around the world is the card game model.

Let's imagine we want to develop a program to play the card game `War`. `War` is played with a standard deck of playing cards. So, we'll want to create a `PlayingCard` object. 

However, we also plan to create the game `Set`, which uses specialized `SetCard` objects with symbols in various quantities and colors on them (e.g. 3 orange squiggles, 2 blue circles, 1 red square, etc.)

A light bulb goes off: . Why not create a `Card` class that can be used with any card game. `Card` objects all have certain similar properties and behaviors. They have `content` on their front. They are able to be `flip`ed over. This way we only have to build out that functionality in one place. We can then create specific classes for `PlayingCard` and `SetCard` that implement the specific content of these games.

To take the example further, combined, a set of cards make a `Deck`. Regardless of what kind of `Card`, a `Deck` of `Card` objects can be `shuffle`d and `deal`t! Now we can subclass our `Deck` as a `PlayingCardDeck` and as a `SetCardDeck`.

Let's take a look at the interface for each of the card objects we have described thus far to further our explanation.


```objc
@interface FISCard : NSObject

@property (strong, nonatomic) NSString *content;

- (void)flipCard;

@end
```

```objc
#import <UIKit/UIKit.h>
#import "FISCard.h"

@interface FISPlayingCard : FISCard

@property (strong, nonatomic) NSString *suit;
@property (strong, nonatomic) NSNumber *rank;

- (instancetype)initWithContent:(NSString *)content;

@end
```

```objc
#import <UIKit/UIKit.h>
#import "FISCard.h"

@interface FISSetCard : FISCard

@property (strong, nonatomic) NSString *symbol;
@property (strong, nonatomic) NSNumber *quantity;
@property (strong, nonatomic) NSNumber *color;

- (instancetype)initWithContent:(NSString *)content;

@end
```

As seen in the above interface code snippets, `PlayingCard` and `SetCard` both inherit from `Card` (while `Card` inherits from `NSObject`). This means that they both have the content property and the ability to `flipCard`. The remainder of their properties are unique to the type of card they are. Note that "contents" is an abstract property in the `Card` class, as no undefined `Card` will have specific `contents`.

##Overriding methods

In order to add `content` to each subclass of `Card`, we need to "override" the `content` property's getter. Unlike languages in which you must use a keyword to indicate an override, in Objective-C, you can simply rewrite the methods as needed. So in this case we might have the following method in our `PlayingCard` implementation.

```objc
- (NSString *)contents {
	_content = [NSString stringWithFormat:@"%@ %@",self.rank, self.suit];
	return _content;
}
```

##Initializers with inheritance
```

Just as with all objects that inherit from another class, the methods of the super class are available in that subclass. Initializers are no different. However, there is a correct and an incorrect way to write additional convenience initializers in a subclass.

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

And the less perfect subclass initializer:

```objc

//Raccoon.m

- (instancetype)initWithName:(NSString *)name stripeColor {
	
	self = [super init];

	if (self) {

		_name = name;
		_stripeColor = stripeColor;
	}

	return self;
}

```

The issue with the above convenience initializer is that it does two checks. When we initialize the `Raccoon` object, it first goes through the `init` default initializer and `initWithName:` convenience initializer in the `Animal` class, setting the name of the `Raccoon` to be an empty `NSString`. Then it comes back to our `initWithName:stripeColor:` method and replaces the values for `name` and `stripeColor` to the values assigned as part of the initializer's method call. This is more code running than should be. 

Instead, we should call the designated initializer of our super class, like so:

######Example

```objc

//Raccoon.m

- (instancetype)initWithName:(NSString *)name StripeColor:(NSString *)stripeColor {
	
	self = [super initWithName:name];

	if (self) {
		_stripeColor = stripeColor;
	}

	return self;
}
```

Now we have avoided unnecessary calls to the default initializer of our superclass (or any convenience initializers that are not the designated initializer).


