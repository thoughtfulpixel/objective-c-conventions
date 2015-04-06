These conventions build on the following Apple documentation:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

Unless explicitly contradicted below, assume that all of Apple's guidelines in the above documents also apply.

## Language

Languages permitted in Xcode projects:
* Objective C
* C99
* Bash shell scripts
* Python (build scripting only)

We'll avoid Swift until both the language and tools have reached a level of maturity. Don't introduce other languages without an overwhelming need and before discussing & and agreeing with the rest of the team.

Incorporate external dependencies carefully. Consider the following:
* is it easy to integrate, i.e. can it be built/ dropped in as a fat framework?
* is it 64-bit compliant?
* if it's open source - is it widely used and well written (would we want to fork/ support it ourselves)?

Prefer reducing dependencies over relying on a tool a-la-Cocoapods or git submodules. If you feel we need one, discuss with the rest of the team before incorporating.

## Abstractions

POSIX is our platform abstraction layer for C code and  Cocoa and UIKit are our abstraction layers for ObjC code. Don't abstract a standard abstraction.

Do not create definitions like `STATIC static;`, or `INT32 int32_t;`. Please remove as you find them

Prefer class extension and delegation over inheritance. For more pattern goodness, see Apple's [Cocoa Encyclopedia](https://developer.apple.com/library/mac/documentation/General/Conceptual/CocoaEncyclopedia/Introduction/Introduction.html).

## Language

US English only.

**Preferred:**
```objc
UIColor *myColor = UIColor.whiteColor;
```

**Not Preferred:**
```objc
UIColor *myColour = UIColor.whiteColor;
```

## Code Organization

Use `#pragma - mark`s rather than comments to categorize methods into functional groupings and protocol implementations, following this general structure:

**Preferred:**
```objc
#pragma mark NSObject

- (void)dealloc {}

#pragma mark - JAYClassName

+ (instancetype)objectWithThing:(id)thing {}
- (instancetype)init {}

#pragma mark - JAYSuperclassName

- (void)someOverriddenMethod {}

#pragma mark - Accessors

- (void)setCustomProperty:(id)value {}

#pragma mark - JAYProtocolName

#pragma mark - Public

#pragma mark - Private

```

## Naming

Name classes, methods and functions (both public and private) clearly. Prefer longer, explanatory names to short obfuscated ones.

Prefix class names and constants with `JAY`, including Core Data entity names.

## Documentation and comments
Document header files where it makes sense, e.g. for oft used or complex internal interfaces.

Use [HeaderDoc](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/HeaderDoc/tags/tags.html#//apple_ref/doc/uid/TP40001215-CH346-CHDJFEGF) so it can be parsed by Xcode's Quick Documentation lookup.

Use comments sparingly, predominantly for TODOs. Instead of commenting the intent of a block of code, use more explanatory local variable names and/ or break into appropriately named subroutines.

## Whitespace

Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.

Control flow braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

**Preferred:**
```objc
if (user.isHappy) {
  //Do something
} else {
  //Do something else
}
```

**Not Preferred:**
```objc
if (user.isHappy)
{
    // do something
}
else {
    // do something else
}
```

Method braces always open on the definition line and close on a new line.

**Preferred:**
```objc
- (void)someMethod {
    // do something
}
```

**Not Preferred:**
```objc
- (void)someMethod
{
    // do something
}
```

There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.

If required, `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.

Colon-aligning method invocation should often be avoided.  There are cases where a method signature may have >= 3 colons and colon-aligning makes the code more readable. Please do **NOT** however colon align methods containing blocks because Xcode's indenting makes it illegible.

**Preferred:**
```objc
// blocks are easily readable
[UIView animateWithDuration:1.0 animations:^{
  // something
} completion:^(BOOL finished) {
  // something
}];
```

**Not Preferred:**
```objc
// colon-aligning makes the block indentation hard to read
[UIView animateWithDuration:1.0
                 animations:^{
                     // something
                 }
                 completion:^(BOOL finished) {
                     // something
                 }];
```

## Declarations

Never declare an ivar unless you need to change its type from its declared property.
 
Avoid use line breaks in method declarations except where it aids legibility.

Always declare memory-management semantics even on `readonly` properties.
 
Declare properties `readonly` if they are only set once in `-init`.
 
Don't use `@synthesize` unless the compiler requires it. Note that optional properties in protocols must be explicitly synthesized in order to exist.

Declare properties `copy` if they return immutable objects and aren't ever mutated in the implementation. `strong` should only be used when exposing a mutable object, or an object that does not conform to `<NSCopying>`.

Instance variables should be prefixed with an underscore (just like when implicitly synthesized).

Don't put a space between an object type and the protocol it conforms to.

**Preferred:**
```objc
@property (attributes) id<JAYProtocol> object;
@property (nonatomic, strong) NSObject<JAYProtocol> *object;
```

C function declarations should have no space before the opening parenthesis, and should be namespaced just like a class.
 
**Preferred:**
```objc
void JAYAwesomeFunction(BOOL hasSomeArgs);
```

Constructors should return [`instancetype`](http://clang.llvm.org/docs/LanguageExtensions.html#related-result-types) not `id`.

## Expressions

Don't access an ivar unless you're in `-init`, `-dealloc` or a custom accessor.
 
Prefer dot-syntax for properties and invoking idempotent methods, including setters and class methods (like `NSFileManager.defaultManager`).
 
Use object literals, boxed expressions, and subscripting over the older, grosser alternatives.

**Preferred:**
```objc
NSString *theString = myDictionary[@"someKey"];
NSObject *anObjectAtSomeIndex = myArray[someIndex];
NSNumber *aBoolValue = @YES;
```

Prefer positive comparisons to negative. Comparisons should be explicit for everything except `BOOL`s and testing or nil. Test for, and execute, the positive, non-error path.

**Preferred:**
```objc
if (pointerThatMightNotBeInitialized) { 
	// do stuff with pointer 
} else {
	// do failure path
}
```

If the correct behavior is to attempt to correct the situation and retry, the first test should be for the negative, attempt to fix if needed, return to prior scope, and test for the positive.

**Preferred:**
```objc
if (!self.filePointerThatShouldBeOpen) { // Is our file ready to read?
	// No, can we fix it?
	self.filePointerThatShouldBeOpen = fopen(self.streamFileName, "r+");
}
 
if (self.filePointerThatShouldBeOpen) {
	// do stuff with file 
} else {
	// do failure path
}
```

Ternary operators, both long and short (`nil` coalescing) form, should be used only for assignment and arguments and wrapped in parentheses.

**Preferred:**
```objc
Blah *a = (stuff == thing ? foo : bar);
Blah *b = (thingThatCouldBeNil ?: defaultValue); 
```

Separate binary operands with a single space, but unary operands and casts with none:

**Preferred:**
```c
void *ptr = &value + 10 * 3;
NewType a = (NewType)b;

for (int i = 0; i < 10; i++) {
    doCoolThings();
}
```

## Control Structures

Don't use multiple return statements.

Always surround `if` bodies with curly braces if there is an `else`. Single-line `if` bodies without an `else` should be on the same line as the `if`.

All curly braces should begin on the same line as their associated statement. They should end on a new line.

Put a single space after keywords and before their parentheses.

For simple non-mainline conditions, return early.

**Preferred:**
```objc
- (void)someMethod {
 
    if (someFlag) {
	    return;
    }

    if (anotherBool) {
        // do stuff
    } else {
        // do other stuff
    }
} 
```

## Design by contract

Use NSAssert() and NSParameterAssert() liberally.

## Blocks

Blocks should have a space between their return type and name.

Block definitions should omit their return type when possible.

Block definitions should omit their arguments if they are `void`.

Parameters in block types should be named unless the block is initialized immediately.

**Preferred:**
```objc
void (^aBlock)(void) = ^{
    // do stuff
};

id (^aBlockWithArgs)(id) = ^ id (id args) {
    // do stuff
};

id (^anotherBlockWithArgs)(id args);
 
anotherBlockWithArgs = ^ id (id args) {
    // do stuff
}; 
```

**Not Preferred:**
```objc
id (^anotherBlockWithArgs)(id);

anotherBlockWithArgs = ^ id (id args) {
    // do stuff
};
```

## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as static constants and not `#defines` unless explicitly being used as a macro.

**Preferred:**
```objc
static NSString * const JAYAboutViewControllerCompanyDomainName = @"jaybirdsport.com";

static CGFloat const JAYImageThumbnailHeight = 50.0;
```

**Not Preferred:**
```objc
#define CompanyDomainName @"jaybirdsport.com"

#define thumbnailHeight 50
```

## Literals

Avoid making numbers a specific type unless necessary.

**Preferred:**
```objc
static const float positioningFactor = 5.0;
...
[someThing moveBy:positioningFactor];
```

**Not Preferred:**
```objc
[someThing moveBy:5.f];
```

The contents of array and dictionary literals should have no space on either side. Dictionary literals should have no space between the key and the colon, and a single space between colon and value.

**Preferred:**
``` objc
NSArray *theStuff = @[@1, @2, @3];

NSDictionary *keyedStuff = @{JAYDidCreateStyleGuide: @YES};
```

Longer or more complex literals should be split over multiple lines (optionally with a terminating comma):

**Preferred:**
``` objc
NSArray *theStuff = @[
    @"Got some long string objects in here.",
    [AndSomeModelObjects too],
    @"Moar strings."
];

NSDictionary *keyedStuff = @{
    @"this.key": @"corresponds to this value",
    @"otherKey": @"remoteData.payload",
    @"some": @"more",
    @"JSON": @"keys",
    @"and": @"stuff",
};
```

## Categories

Categories should be named for the sort of functionality they provide. Don't create umbrella categories. Rather, define separate interfaces and implementations.

Use categories rather than preprocessor macros for custom named UIColor and UIFonts.
 
Prefix category methods on framework classes with jay_.

**Preferred:**
``` objc
UIColor *theColor = UIColor.jay_nearGreyColor; 
NSTimeInterval aDouble = [[NSDate date] jay_someCategoryMethodThatReturnsADouble];
```
 
If you need to expose private methods for subclasses or unit testing, create a class extension named `Class+Private`.

## CGRect Functions

Prefer Auto Layout. Avoid setting or getting frames directly, unless it helps either performance or code clarity.

Where you really need to access the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**Preferred:**
```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
CGRect frame = CGRectMake(0.0, 0.0, width, height);
```

**Not Preferred:**
```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
CGRect frame = (CGRect){ .origin = CGPointZero, .size = frame.size };
```

## Golden Path

When coding with conditionals, the left hand margin of the code should be the "default" or "mainline" path.

**Preferred:**
```objc
bool JAYCriticalSecurityValidation(UUID *token, string *user, hash *passHash) {
    if (TokenStillValid(token) {
        UserRecord* ur = [UserRecord recordForUserFromString:user]; // returns nil if not found
        if (ur && [ur passMatchesHash:passHash]) {
            return true; // only good exit
        }
    }
 
    return false; // the default exit, coding flaws are almost certain to hit here, instead of falling through to a 'false' success.
} 
 
- (void)someMethod {
    
    if (![someOther boolValue]) {
        return;
    }

    // do something really important
}
```

**Not Preferred:**
```objc
bool JAYCriticalSecurityValidation(UUID *token, string *user, hash *passHash) {
    if (!TokenStillValid(token) {
        UserRecord* ur = [UserRecord recordForUserFromString:user];
        return (!ur && ![ur passMatchesHash:passHash]);
    }
    return true;
}
 
- (void)someMethod {
  if ([someOther boolValue]) {
    // do something really important
  }
}
```

## Error handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

**Preferred:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
  // Handle Error
}
```

**Not Preferred:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
  // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).


## Singletons

Shared instances should be used vary sparingly and with great thought.

When you decide to use them, use the thread-safe pattern for creating.

```objc
+ (instancetype)sharedInstance
{
  static id sharedInstance = nil;

  static dispatch_once_t onceToken;
  dispatch_once(&onceToken, ^{
    sharedInstance = [[self alloc] init];
  });

  return sharedInstance;
}
```
This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Copying versus refactoring

Don't duplicate code.

Rather, identify the common elements in the several functions and either refactor them into subroutines, or reconsider the intent of the functions and refactor the functions themselves.

## Minimize dependencies

Minimize and normalize `#include` and `#import` usage. Prefer the forward declaration, `@class`, over `#import` in header files. Where possible, use `#import` rather than `#include`.

Every time you start to type #import or #include, stop and consider why you're adding an additional dependency to this file. Must this class have a dependency on the other? Why must it? Have you reversed the dependency tree? Are they co-dependent? Why? Should they both be dependent on a third class instead? How will introducing such a dependency impact future maintenance and re-use?

When a pair (or more) of header files are explicitly codependent, and will always be included together when used properly, either merge them into one file, or include one in the other, as appropriate. Then sweep the codebase to remove any redundant includes of the pairs.

The appearance of application headers (as opposed to system and library headers) in the project's PCH (precompiled header) file is invariably an indication of design failure. Rethink your reasons for adding them, and justify doing so in comments in the PCH. If you find unjustified inclusions in the PCH file, remove them. If the build slows down, fix THAT problem, by reducing co-dependencies.

Classes and function collections should have the smallest possible API. Declare NOTHING in the header file that isn't required by external users of the class or collection.

## Acknowledgements

Inspired by [GitHub](https://github.com/github/objective-c-conventions),  [RayWenderlich.com](https://github.com/raywenderlich/objective-c-style-guide) and [Mattt Thompson](https://github.com/mattt/)'s Obj-C code style.
