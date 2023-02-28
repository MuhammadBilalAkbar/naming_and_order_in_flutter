# Naming and Order in Flutter

## Names

### DO use terms consistently.
Use the same name for the same thing, throughout your code. If a precedent already exists outside your API that users are likely to know, follow that precedent.
<br />good:
```dart
pageCount         // A field.
updatePageCount() // Consistent with pageCount.
toSomething()     // Consistent with Iterable's toList().
asSomething()     // Consistent with List's asMap().
Point             // A familiar concept.
```
bad:
```dart
renumberPages()      // Confusingly different from pageCount.
convertToSomething() // Inconsistent with toX() precedent.
wrappedAsSomething() // Inconsistent with asX() precedent.
Cartesian            // Unfamiliar to most users.
```
The goal is to take advantage of what the user already knows. This includes their knowledge of the problem domain itself, the conventions of the core libraries, and other parts of your own API. By building on top of those, you reduce the amount of new knowledge they have to acquire before they can be productive.

### AVOID abbreviations.
Unless the abbreviation is more common than the unabbreviated term, don’t abbreviate. If you do abbreviate, capitalize it correctly.
<br />good:
```dart
pageCount
buildRectangles
IOStream
HttpRequest
```
bad:
```dart
numPages    // "Num" is an abbreviation of "number (of)".
buildRects
InputOutputStream
HypertextTransferProtocolRequest
```

### PREFER putting the most descriptive noun last.
The last word should be the most descriptive of what the thing is. You can prefix it with other words, such as adjectives, to further describe the thing.
<br />good:
```dart:
pageCount             // A count (of pages).
ConversionSink        // A sink for doing conversions.
ChunkedConversionSink // A ConversionSink that's chunked.
CssFontFaceRule       // A rule for font faces in CSS.
```
bad:
```dart
numPages                  // Not a collection of pages.
CanvasRenderingContext2D  // Not a "2D".
RuleFontFaceCss           // Not a CSS.
```

### CONSIDER making the code read like a sentence.
When in doubt about naming, write some code that uses your API, and try to read it like a sentence.
<br />good:
```dart
// "If errors is empty..."
if (errors.isEmpty) ...

// "Hey, subscription, cancel!"
subscription.cancel();
```
bad:
```dart
// "Get the monsters where the monster has claws."
monsters.where((monster) => monster.hasClaws);
// Telling errors to empty itself, or asking if it is?
if (errors.empty) ...

// Toggle what? To what?
subscription.toggle();

// Filter the monsters with claws *out* or include *only* those?
monsters.filter((monster) => monster.hasClaws);
```
It’s helpful to try out your API and see how it “reads” when used in code, but you can go too far. It’s not helpful to add articles and other parts of speech to force your names to literally read like a grammatically correct sentence.
<br />bad:
```dart
if (theCollectionOfErrors.isEmpty) ...

monsters.producesANewSequenceWhereEach((monster) => monster.hasClaws);
```

### PREFER a noun phrase for a non-boolean property or variable.
The reader’s focus is on what the property is. If the user cares more about how a property is determined, then it should probably be a method with a verb phrase name.
<br />good:
```dart
list.length
context.lineWidth
quest.rampagingSwampBeast
```
bad:
```dart
list.deleteItems
```

### PREFER a non-imperative verb phrase for a boolean property or variable.
Boolean names are often used as conditions in control flow, so you want a name that reads well there. Compare:
```dart
if (window.closeable) ...  // Adjective.
if (window.canClose) ...   // Verb.
```
What separates all these verb phrases from method names is that they are not imperative. A boolean name should never sound like a command to tell the object to do something, because accessing a property doesn’t change the object. (If the property does modify the object in a meaningful way, it should be a method.)
<br />good:
```dart
isEmpty
hasElements
canClose
closesWindow
canShowPopup
hasShownPopup
```
bad:
```dart
empty         // Adjective or verb?
withElements  // Sounds like it might hold elements.
closeable     // Sounds like an interface.
// "canClose" reads better as a sentence.
closingWindow // Returns a bool or a window?
showPopup     // Sounds like it shows the popup.
```

### CONSIDER omitting the verb for a named boolean parameter.
This refines the previous rule. For named parameters that are boolean, the name is often just as clear without the verb, and the code reads better at the call site.
<br />good:
```dart
Isolate.spawn(entryPoint, message, paused: false);
var copy = List.from(elements, growable: true);
var regExp = RegExp(pattern, caseSensitive: false);
```

### PREFER the “positive” name for a boolean property or variable.
Most boolean names have conceptually “positive” and “negative” forms where the former feels like the fundamental concept and the latter is its negation—”open” and “closed”, “enabled” and “disabled”, etc. Often the latter name literally has a prefix that negates the former: “visible” and “in-visible”, “connected” and “dis-connected”, “zero” and “non-zero”.
<br /><br />
When choosing which of the two cases that true represents—and thus which case the property is named for—prefer the positive or more fundamental one. Boolean members are often nested inside logical expressions, including negation operators. If your property itself reads like a negation, it’s harder for the reader to mentally perform the double negation and understand what the code means.
<br />good:
```dart
if (socket.isConnected && database.hasData) {
socket.write(database.read());
}
```
bad:
```dart
if (!socket.isDisconnected && !database.isEmpty) {
socket.write(database.read());
}
```
For some properties, there is no obvious positive form. Is a document that has been flushed to disk “saved” or “un-changed”? Is a document that hasn’t been flushed “un-saved” or “changed”? In ambiguous cases, lean towards the choice that is less likely to be negated by users or has the shorter name.
<br /><br />
`Exception`: With some properties, the negative form is what users overwhelmingly need to use. Choosing the positive case would force them to negate the property with ! everywhere. Instead, it may be better to use the negative case for that property.

### PREFER an imperative verb phrase for a function or method whose main purpose is a side effect.
Callable members can return a result to the caller and perform other work or side effects. In an imperative language like Dart, members are often called mainly for their side effect: they may change an object’s internal state, produce some output, or talk to the outside world.
<br /> <br />
Those kinds of members should be named using an imperative verb phrase that clarifies the work the member performs.
<br />good:
```dart
list.add('element');
queue.removeFirst();
window.refresh();
```
This way, an invocation reads like a command to do that work.

### PREFER a noun phrase or non-imperative verb phrase for a function or method if returning a value is its primary purpose.
Other callable members have few side effects but return a useful result to the caller. If the member needs no parameters to do that, it should generally be a getter. But sometimes a logical “property” needs some parameters. For example, `elementAt()` returns a piece of data from a collection, but it needs a parameter to know which piece of data to return.
<br /><br />
This means the member is syntactically a method, but conceptually it is a property, and should be named as such using a phrase that describes what the member returns.
<br />good:
```dart
var element = list.elementAt(3);
var first = list.firstWhere(test);
var char = string.codeUnitAt(4);
```
This guideline is deliberately softer than the previous one. Sometimes a method has no side effects but is still simpler to name with a verb phrase like `list.take()` or `string.split()`.

### CONSIDER an imperative verb phrase for a function or method if you want to draw attention to the work it performs.
When a member produces a result without any side effects, it should usually be a getter or a method with a noun phrase name describing the result it returns. However, sometimes the work required to produce that result is important. It may be prone to runtime failures, or use heavyweight resources like networking or file I/O. In cases like this, where you want the caller to think about the work the member is doing, give the member a verb phrase name that describes that work.
<br/>good:
```dart
var table = database.downloadData();
var packageVersions = packageGraph.solveConstraints();
```
Note, though, that this guideline is softer than the previous two. The work an operation performs is often an implementation detail that isn’t relevant to the caller, and performance and robustness boundaries change over time. Most of the time, name your members based on what they do for the caller, not how they do it.

### AVOID starting a method name with get.
In most cases, the method should be a getter with get removed from the name. For example, instead of a method named getBreakfastOrder(), define a getter named breakfastOrder.
<br/>
Even if the member does need to be a method because it takes arguments or otherwise isn’t a good fit for a getter, you should still avoid get. Like the previous guidelines state, either:
- Simply drop get and use a noun phrase name like breakfastOrder() if the caller mostly cares about the value the method returns.
- Use a verb phrase name if the caller cares about the work being done, but pick a verb that more precisely describes the work than get, like create, download, fetch, calculate, request, aggregate, etc.

### PREFER naming a method to___() if it copies the object’s state to a new object.
Linter rule: use_to_and_as_if_applicable
<br/>
A conversion method is one that returns a new object containing a copy of almost all of the state of the receiver but usually in some different form or representation. The core libraries have a convention that these methods are named starting with to followed by the kind of result.
<br/>
If you define a conversion method, it’s helpful to follow that convention.
<br/>good:
```dart
list.toSet();
stackTrace.toString();
dateTime.toLocal();
```

### PREFER naming a method as___() if it returns a different representation backed by the original object.
Linter rule: use_to_and_as_if_applicable
<br/>
Conversion methods are “snapshots”. The resulting object has its own copy of the original object’s state. There are other conversion-like methods that return views—they provide a new object, but that object refers back to the original. Later changes to the original object are reflected in the view.
<br/>
The core library convention for you to follow is as___().
<br/>good:
```dart
var map = table.asMap();
var list = bytes.asFloat32List();
var future = subscription.asFuture();
```

### AVOID describing the parameters in the function’s or method’s name.
The user will see the argument at the call site, so it usually doesn’t help readability to also refer to it in the name itself.
<br/>good:
```dart
list.add(element);
map.remove(key);
```
bad:
```dart
list.addElement(element)
map.removeKey(key)
```
However, it can be useful to mention a parameter to disambiguate it from other similarly-named methods that take different types:
<br/>good:
```dart
map.containsKey(key);
map.containsValue(value);
```

### DO follow existing mnemonic conventions when naming type parameters.
Single letter names aren’t exactly illuminating, but almost all generic types use them. Fortunately, they mostly use them in a consistent, mnemonic way. The conventions are:
- E for the element type in a collection:
<br/>good:
```dart
class IterableBase<E> {}
class List<E> {}
class HashSet<E> {}
class RedBlackTree<E> {}
```
- K and V for the key and value types in an associative collection:
<br/>good:
```dart
class Map<K, V> {}
class Multimap<K, V> {}
class MapEntry<K, V> {}
```
- R for a type used as the return type of a function or a class’s methods. This isn’t common, but appears in typedefs sometimes and in classes that implement the visitor pattern:
<br/>good:
```dart
abstract class ExpressionVisitor<R> {
  R visitBinary(BinaryExpression node);
  R visitLiteral(LiteralExpression node);
  R visitUnary(UnaryExpression node);
}
```
- Otherwise, use T, S, and U for generics that have a single type parameter and where the surrounding type makes its meaning obvious. There are multiple letters here to allow nesting without shadowing a surrounding name. For example:
<br/>good:
```dart
class Future<T> {
Future<S> then<S>(FutureOr<S> onValue(T value)) => ...
}
```
Here, the generic method then`<S>()` uses `S` to avoid shadowing the `T` on `Future<T>`.
<br/>
If none of the above cases are a good fit, then either another single-letter mnemonic name or a descriptive name is fine:
<br/>good:
```dart
class Graph<N, E> {
final List<N> nodes = [];
final List<E> edges = [];
}

class Graph<Node, Edge> {
final List<Node> nodes = [];
final List<Edge> edges = [];
}
```
In practice, the existing conventions cover most type parameters.

## Libraries
A leading underscore character ( _ ) indicates that a member is private to its library. This is not mere convention, but is built into the language itself.

### PREFER making declarations private.
A public declaration in a library—either top level or in a class—is a signal that other libraries can and should access that member. It is also a commitment on your library’s part to support that and behave properly when it happens.
<br/><br/>
If that’s not what you intend, add the little _ and be happy. Narrow public interfaces are easier for you to maintain and easier for users to learn. As a nice bonus, the analyzer will tell you about unused private declarations so you can delete dead code. It can’t do that if the member is public because it doesn’t know if any code outside of its view is using it.

### CONSIDER declaring multiple classes in the same library.
Some languages, such as Java, tie the organization of files to the organization of classes—each file may only define a single top level class. Dart does not have that limitation. Libraries are distinct entities separate from classes. It’s perfectly fine for a single library to contain multiple classes, top level variables, and functions if they all logically belong together.
<br/><br/>
Placing multiple classes together in one library can enable some useful patterns. Since privacy in Dart works at the library level, not the class level, this is a way to define “friend” classes like you might in C++. Every class declared in the same library can access each other’s private members, but code outside of that library cannot.
<br/><br/>
Of course, this guideline doesn’t mean you should put all of your classes into a huge monolithic library, just that you are allowed to place more than one class in a single library.

## Classes and mixins
Dart is a “pure” object-oriented language in that all objects are instances of classes. But Dart does not require all code to be defined inside a class—you can define top-level variables, constants, and functions like you can in a procedural or functional language.

### AVOID defining a one-member abstract class when a simple function will do.
Linter rule: one_member_abstracts
<br/>
Unlike Java, Dart has first-class functions, closures, and a nice light syntax for using them. If all you need is something like a callback, just use a function. If you’re defining a class and it only has a single abstract member with a meaningless name like call or invoke, there is a good chance you just want a function.
<br/>good:
```dart
typedef Predicate<E> = bool Function(E element);
```
bad:
```dart
abstract class Predicate<E> {
bool test(E element);
}
```
### AVOID defining a class that contains only static members.
Linter rule: avoid_classes_with_only_static_members
<br/>
In Java and C#, every definition must be inside a class, so it’s common to see “classes” that exist only as a place to stuff static members. Other classes are used as namespaces—a way to give a shared prefix to a bunch of members to relate them to each other or avoid a name collision.
<br/><br/>
Dart has top-level functions, variables, and constants, so you don’t need a class just to define something. If what you want is a namespace, a library is a better fit. Libraries support import prefixes and show/hide combinators. Those are powerful tools that let the consumer of your code handle name collisions in the way that works best for them.
<br/><br/>
If a function or variable isn’t logically tied to a class, put it at the top level. If you’re worried about name collisions, give it a more precise name or move it to a separate library that can be imported with a prefix.
<br/>good:
```dart
DateTime mostRecent(List<DateTime> dates) {
return dates.reduce((a, b) => a.isAfter(b) ? a : b);
}

const _favoriteMammal = 'weasel';
```
bad:
```dart
class DateUtils {
static DateTime mostRecent(List<DateTime> dates) {
return dates.reduce((a, b) => a.isAfter(b) ? a : b);
   }
 }

class _Favorites {
static const mammal = 'weasel';
}
```
In idiomatic Dart, classes define kinds of objects. A type that is never instantiated is a code smell.
<br/>
However, this isn’t a hard rule. With constants and enum-like types, it may be natural to group them in a class.
<br/>good:
```dart
class Color {
static const red = '#f00';
static const green = '#0f0';
static const blue = '#00f';
static const black = '#000';
static const white = '#fff';
}
```
### AVOID extending a class that isn’t intended to be subclassed.
If a constructor is changed from a generative constructor to a factory constructor, any subclass constructor calling that constructor will break. Also, if a class changes which of its own methods it invokes on this, that may break subclasses that override those methods and expect them to be called at certain points.
<br/><br/>
Both of these mean that a class needs to be deliberate about whether or not it wants to allow subclassing. This can be communicated in a doc comment, or by giving the class an obvious name like IterableBase. If the author of the class doesn’t do that, it’s best to assume you should not extend the class. Otherwise, later changes to it may break your code.

### DO document if your class supports being extended.
This is the corollary to the above rule. If you want to allow subclasses of your class, state that. Suffix the class name with Base, or mention it in the class’s doc comment.

### AVOID implementing a class that isn’t intended to be an interface.
Implicit interfaces are a powerful tool in Dart to avoid having to repeat the contract of a class when it can be trivially inferred from the signatures of an implementation of that contract.
<br/><br/>
But implementing a class’s interface is a very tight coupling to that class. It means virtually any change to the class whose interface you are implementing will break your implementation. For example, adding a new member to a class is usually a safe, non-breaking change. But if you are implementing that class’s interface, now your class has a static error because it lacks an implementation of that new method.
<br/><br/>
Library maintainers need the ability to evolve existing classes without breaking users. If you treat every class like it exposes an interface that users are free to implement, then changing those classes becomes very difficult. That difficulty in turn means the libraries you rely on are slower to grow and adapt to new needs.
<br/><br/>
To give the authors of the classes you use more leeway, avoid implementing implicit interfaces except for classes that are clearly intended to be implemented. Otherwise, you may introduce a coupling that the author doesn’t intend, and they may break your code without realizing it.

### DO document if your class supports being used as an interface.
If your class can be used as an interface, mention that in the class’s doc comment.

### DO use `mixin` to define a mixin type.
Linter rule: prefer_mixin
<br/>
Dart originally didn’t have a separate syntax for declaring a class intended to be mixed in to other classes. Instead, any class that met certain restrictions (no non-default constructor, no superclass, etc.) could be used as a mixin. This was confusing because the author of the class might not have intended it to be mixed in.
<br/>
Dart 2.1.0 added a mixin keyword for explicitly declaring a mixin. Types created using that can only be used as mixins, and the language also ensures that your mixin stays within the restrictions. When defining a new type that you intend to be used as a mixin, use this syntax.
<br/>good:
```dart
mixin ClickableMixin implements Control {
bool _isDown = false;

void click();

void mouseDown() {
_isDown = true;
}

void mouseUp() {
if (_isDown) click();
_isDown = false;
  }
}
```
You might still encounter older code using class to define mixins, but the new syntax is preferred.

### AVOID mixing in a type that isn’t intended to be a mixin.
Linter rule: prefer_mixin
<br/>
For compatibility, Dart still allows you to mix in classes that aren’t defined using mixin. However, that’s risky. If the author of the class doesn’t intend the class to be used as a mixin, they might change the class in a way that breaks the mixin restrictions. For example, if they add a constructor, your class will break.
<br/><br/>
If the class doesn’t have a doc comment or an obvious name like IterableMixin, assume you cannot mix in the class if it isn’t declared using mixin.





