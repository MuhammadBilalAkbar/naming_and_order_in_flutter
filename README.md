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

## Naming

### Begin global constant names with prefix "k"
Examples:
```dart
const double kParagraphSpacing = 1.5;
const String kSaveButtonTitle = 'Save';
const Color _kBarrierColor = Colors.black54;
```
However, where possible avoid global constants. Rather than `kDefaultButtonColor`, consider `Button.defaultColor`. If necessary, consider creating a class with a private constructor to hold relevant constants. It’s not necessary to add the k prefix to non-global constants.

### Avoid abbreviations
Unless the abbreviation is more recognizable than the expansion (e.g. XML, HTTP, JSON), expand abbrevations when selecting a name for an identifier. In general, avoid one-character names unless one character is idiomatic (for example, prefer index over i, but prefer x over `horizontalPosition`).

### Avoid anonymous parameter names
Provide full type information and names even for parameters that are otherwise unused. This makes it easier for people reading the code to tell what is actually going on (e.g. what is being ignored). For example:
```dart
onTapDown: (TapDownDetails details) { print('hello!'); }, // GOOD
onTapUp: (_) { print('good bye'); }, // BAD
```

### Naming rules for typedefs and function variables
When naming callbacks, use `FooCallback` for the typedef, `onFoo` for the callback argument or property, and `handleFoo` for the method that is called. If `Foo` is a verb, prefer the present tense to the past tense (e.g. `onTap` instead of `onTapped`).
<br/><br/>
If you have a callback with arguments but you want to ignore the arguments, give the type and names of the arguments anyway. That way, if someone copies and pastes your code, they will not have to look up what the arguments are.
<br/><br/>
Never call a method `onFoo`. If a property is called `onFoo` it must be a function type. (For all values of "Foo".)
<br/><br/>
Prefer using `typedef`s to declare callbacks. Typedefs benefit from having documentation on the type itself and make it easier to read and find common callsites for the signature.

### Spell words in identifiers and comments correctly
Our primary source of truth for spelling is the Material Design Specification. Our secondary source of truth is dictionaries.
<br/><br/>
Avoid "cute" spellings. For example, 'colors', not 'colorz'.
<br/><br/>
Prefer US English spellings. For example, 'colorize', not 'colourise', and 'canceled', not 'cancelled'.
<br/><br/>
Prefer compound words over "cute" spellings to avoid conflicts with reserved words. For example, 'classIdentifier', not 'klass'.

### Capitalize identifiers consistent with their spelling
In general, we use [Dart’s recommendations](https://dart.dev/guides/language/effective-dart/style#identifiers)'s for naming identifiers. Please consider the following additional guidelines:
<br/><br/>
If a word is correctly spelled (according to our sources of truth as described in the previous section) as a single word, then it should not have any inner capitalization or spaces.
<br/><br/>
For examples, prefer `toolbar`, `scrollbar`, but `appBar` ('app bar' in documentation), `tabBar` ('tab bar' in documentation).
<br/><br/>
Similarly, prefer `offstage` rather than `offStage`.
<br/><br/>
Avoid using class names with `iOS` when possible. The capitalization of `iOS` is supposed to be exactly that, but that doesn’t work well with camelCase and even less with UpperCamelCase; use alternatives like "Cupertino" or "UIKit" instead when possible. If you really really must use "iOS" in an identifier, capitalize it to `IOS`. Whether or not the two-letter exception applies to "iOS" is debatable, but `IOS` is consistent with Dart APIs, and the alternatives (`IOs`, `Ios`) are even more jarring. (Previous versions of this guide incorrectly indicated that `Ios` was the correct capitalization when necessary; this form should not be used in new code.)

### Avoid double negatives in APIs
Name your boolean variables in positive ways, such as "enabled" or "visible", even if the default value is true.
<br/><br/>
This is because, when you have a property or argument named "disabled" or "hidden", it leads to code such as `input.disabled = false` or `widget.hidden = false` when you’re trying to enable or show the widget, which is very confusing.

### Prefer naming the argument to a setter value
Unless this would cause other problems, use `value` for the name of a setter’s argument. This makes it easier to copy/paste the setter later.

### Qualify variables and methods used only for debugging
If you have variables or methods (or even classes!) that are only used in debug mode, prefix their names with `debug` or `_debug` (or, for classes, `_Debug`).
<br/><br/>
Do not use debugging variables or methods (or classes) in production code.

### Avoid naming undocumented libraries
In other words, do not use the `library` keyword, unless it is a documented top-level library intended to be imported by users.