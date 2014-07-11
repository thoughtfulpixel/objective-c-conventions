These conventions build on the following Apple documentation:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

Unless explicitly contradicted below, assume that all of Apple's guidelines in the above documents also apply.


## Language

US English should be used.

**Preferred:**
```objc
UIColor *myColor = UIColor.whiteColor;
```

**Not Preferred:**
```objc
UIColor *myColour = UIColor.whiteColor;
```

## Code Organization

 * Use `#pragma - mark`s to categorize methods into functional groupings and protocol implementations, following this general structure:

```objc
#pragma mark NSObject

- (void)dealloc {}

#pragma mark - CBNClassName

+ (instancetype)objectWithThing:(id)thing {}
- (instancetype)init {}

#pragma mark - CBNSuperclass

- (void)someOverriddenMethod {}

#pragma mark - Accessors

- (void)setCustomProperty:(id)value {}

#pragma mark - CBNProtocol

#pragma mark - Public

#pragma mark - Private

```

## Naming

* Use the three letter prefix `CBN` for class names and constants, including Core Data entity names.

## Whitespace

* Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
* Control flow braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

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
    //Do something
}
else {
    //Do something else
}
```

* Method braces always open and close on a new line.

**Preferred:**
```objc
- (void)someMethod
{
    //Do something
}
```

**Not Preferred:**
```objc
- (void)someMethod {
    //Do something
}
```

* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
* If required, `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.
* Colon-aligning method invocation should often be avoided.  There are cases where a method signature may have >= 3 colons and colon-aligning makes the code more readable. Please do **NOT** however colon align methods containing blocks because Xcode's indenting makes it illegible.

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

 * Never declare an ivar unless you need to change its type from its declared property.
 * Don’t use line breaks in method declarations.
 * Prefer exposing an immutable type for a property if it being mutable is an implementation detail. This is a valid reason to declare an ivar for a property.
 * Always declare memory-management semantics even on `readonly` properties.
 * Declare properties `readonly` if they are only set once in `-init`.
 * Don't use `@synthesize` unless the compiler requires it. Note that optional properties in protocols must be explicitly synthesized in order to exist.
 * Declare properties `copy` if they return immutable objects and aren't ever mutated in the implementation. `strong` should only be used when exposing a mutable object, or an object that does not conform to `<NSCopying>`.
 * Instance variables should be prefixed with an underscore (just like when implicitly synthesized).
 * Don't put a space between an object type and the protocol it conforms to.
 
```objc
@property (attributes) id<CBNProtocol> object;
@property (nonatomic, strong) NSObject<CBNProtocol> *object;
```
 
 * C function declarations should have no space before the opening parenthesis, and should be namespaced just like a class.

```objc
void CBNAwesomeFunction(BOOL hasSomeArgs);
```

 * Constructors should return [`instancetype`](http://clang.llvm.org/docs/LanguageExtensions.html#related-result-types) rather than `id`.


## Expressions

 * Don't access an ivar unless you're in `-init`, `-dealloc` or a custom accessor.
 * Use dot-syntax when invoking idempotent methods, including setters and class methods (like `NSFileManager.defaultManager`).
 * Use object literals, boxed expressions, and subscripting over the older, grosser alternatives.
 * Comparisons should be explicit for everything except `BOOL`s.
 * Prefer positive comparisons to negative.
 * Long form ternary operators should be wrapped in parentheses and only used for assignment and arguments.

```objc
Blah *a = (stuff == thing ? foo : bar);
```

* Short form, `nil` coalescing ternary operators should avoid parentheses.

```objc
Blah *b = thingThatCouldBeNil ?: defaultValue;
```

 * Separate binary operands with a single space, but unary operands and casts with none:

```c
void *ptr = &value + 10 * 3;
NewType a = (NewType)b;

for (int i = 0; i < 10; i++) {
    doCoolThings();
}
```

## Control Structures

 * Always surround `if` bodies with curly braces if there is an `else`. Single-line `if` bodies without an `else` should be on the same line as the `if`. 
 * All curly braces should begin on the same line as their associated statement. They should end on a new line.
 * Put a single space after keywords and before their parentheses.
 * Return and break early.
 * No spaces between parentheses and their contents.

```objc
if (somethingIsBad) return;

if (something == nil) {
	// do stuff
} else {
	// do other stuff
}
```

## Blocks

 * Blocks should have a space between their return type and name.
 * Block definitions should omit their return type when possible.
 * Block definitions should omit their arguments if they are `void`.
 * Parameters in block types should be named unless the block is initialized immediately.

```objc
void (^blockName1)(void) = ^{
    // do some things
};

id (^blockName2)(id) = ^ id (id args) {
    // do some things
};
```

## Literals

 * Avoid making numbers a specific type unless necessary (for example, prefer `5` to `5.0`, and `5.3` to `5.3f`).
 * The contents of array and dictionary literals should have no space on either side.
 * Dictionary literals should have no space between the key and the colon, and a single space between colon and value.

``` objc
NSArray *theStuff = @[@1, @2, @3];

NSDictionary *keyedStuff = @{CBNDidCreateStyleGuide: @YES};
```

 * Longer or more complex literals should be split over multiple lines (optionally with a terminating comma):

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

 * Categories should be named for the sort of functionality they provide. Don't create umbrella categories.
 * Category methods should always be prefixed.
 * If you need to expose private methods for subclasses or unit testing, create a class extension named `Class+Private`.


## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

**Preferred:**

```objc
static NSString * const CBNAboutViewControllerCompanyName = @"caarbon.com";

static CGFloat const CBNImageThumbnailHeight = 50.0;
```

**Not Preferred:**

```objc
#define CompanyName @"caarbon.com"

#define thumbnailHeight 2
```


## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

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

When coding with conditionals, the left hand margin of the code should be the "golden" or "happy" path.  That is, don't nest `if` statements.  Multiple return statements are OK.

**Preferred:**

```objc
- (void)someMethod
{
  if (![someOther boolValue]) {
	return;
  }

  //Do something important
}
```

**Not Preferred:**

```objc
- (void)someMethod
{
  if ([someOther boolValue]) {
    //Do something important
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

Singleton objects should be used sparingly. Use a thread-safe pattern for creating their shared instance.
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


## Acknowledgements

Inspired by [GitHub](https://github.com/github/objective-c-conventions),  [RayWenderlich.com](https://github.com/raywenderlich/objective-c-style-guide) and [Mattt Thompson](https://github.com/mattt/)'s Obj-C code style.
