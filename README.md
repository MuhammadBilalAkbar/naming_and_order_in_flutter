# Naming and Order in Flutter

##### Table of Contents
[Names](#Names)
[Naming](#Naming)
[Formatting](#Formatting)

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

## Formatting
These guidelines have no technical effect, but they are still important purely for consistency and readability reasons.
<br/><br/>
We do not yet use `dartfmt` (except in flutter/packages). Flutter code tends to use patterns that the standard Dart formatter does not handle well. Flutter team is [working with Dart team](https://github.com/flutter/flutter/issues/2025) to make `dartfmt` aware of these patterns.

### In defense of the extra work that hand-formatting entails
Flutter code might eventually be read by hundreds of thousands of people each day. Code that is easier to read and understand saves these people time. Saving each person even a second each day translates into hours or even days of saved time each day. The extra time spent by people contributing to Flutter directly translates into real savings for our developers, which translates to real benefits to our end users as our developers learn the framework faster.

### Constructors come first in a class
The default (unnamed) constructor should come first, then the named constructors. They should come before anything else (including, e.g., constants or static methods).
<br/><br/>
This helps readers determine whether the class has a default implied constructor or not at a glance. If it was possible for a constructor to be anywhere in the class, then the reader would have to examine every line of the class to determine whether or not there was an implicit constructor or not.

### Order other class members in a way that makes sense
The methods, properties, and other members of a class should be in an order that will help readers understand how the class works.
<br/><br/>
If there’s a clear lifecycle, then the order in which methods get invoked would be useful, for example an `initState` method coming before `dispose`. This helps readers because the code is in chronological order, so they can see variables get initialized before they are used, for instance. Fields should come before the methods that manipulate them, if they are specific to a particular group of methods.
<br/><br/>For example, RenderObject groups all the layout fields and layout methods together, then all the paint fields and paint methods, because layout happens before paint.
<br/><br/>If no particular order is obvious, then the following order is suggested, with blank lines between each one:
1. Constructors, with the default constructor first.
2. Constants of the same type as the class. 
3. Static methods that return the same type as the class.
4. Final fields that are set from the constructor. 
5. Other static methods.
6. Static properties and constants.
7. Members for mutable properties, without new lines separating the members of a property, each property in the order:
   - getter
   - private field
   - setter
8. Read-only properties (other than `hashCode`).
9. Operators (other than `==`). 
10. Methods (other than `toString` and `build`). 
11. The `build` method, for `Widget` and `State` classes. 
12. `operator ==`, `hashCode`, `toString`, and diagnostics-related methods, in that order.
<br/>
Be consistent in the order of members. If a constructor lists multiple fields, then those fields should be declared in the same order, and any code that operates on all of them should operate on them in the same order (unless the order matters).

### Constructor syntax
If you call `super()` in your initializer list, put a space between the constructor arguments' closing parenthesis and the colon. If there’s other things in the initializer list, align the `super()` call with the other arguments. Don’t call `super` if you have no arguments to pass up to the superclass.
```dart
// one-line constructor example
abstract class Foo extends StatelessWidget {
  Foo(this.bar, { Key key, this.child }) : super(key: key);
  final int bar;
  final Widget child;
  // ...
}

// fully expanded constructor example
abstract class Foo extends StatelessWidget {
  Foo(
    this.bar, {
    Key key,
    Widget childWidget,
  }) : child = childWidget,
       super(
         key: key,
       );
  final int bar;
  final Widget child;
  // ...
}
```

### Prefer a maximum line length of 80 characters
Aim for a maximum line length of roughly 80 characters, but prefer going over if breaking the line would make it less readable, or if it would make the line less consistent with other nearby lines. Prefer avoiding line breaks after assignment operators.
```dart
// BAD (breaks after assignment operator and still goes over 80 chars)
final int a = 1;
final int b = 2;
final int c =
    a.very.very.very.very.very.long.expression.that.returns.three.eventually().but.is.very.long();
final int d = 4;
final int e = 5;

// BETTER (consistent lines, not much longer than the earlier example)
final int a = 1;
final int b = 2;
final int c = a.very.very.very.very.very.long.expression.that.returns.three.eventually().but.is.very.long();
final int d = 4;
final int e = 5;
```
```dart
// BAD (breaks after assignment operator)
final List<FooBarBaz> _members =
  <FooBarBaz>[const Quux(), const Qaax(), const Qeex()];

// BETTER (only slightly goes over 80 chars)
final List<FooBarBaz> _members = <FooBarBaz>[const Quux(), const Qaax(), const Qeex()];

// BETTER STILL (fits in 80 chars)
final List<FooBarBaz> _members = <FooBarBaz>[
  const Quux(),
  const Qaax(),
  const Qeex(),
];
```

### Indent multi-line argument and parameter lists by 2 characters
When breaking an argument list into multiple lines, indent the arguments two characters from the previous line.
<br/>
Example:
```dart
Foo f = Foo(
bar: 1.0,
quux: 2.0,
);
```
When breaking a parameter list into multiple lines, do the same.

### If you have a newline after some opening punctuation, match it on the closing punctuation.
And vice versa.
<br/>Example:
```dart
// BAD:
  foo(
    bar, baz);
  foo(
    bar,
    baz);
  foo(bar,
    baz
  );

// GOOD:
  foo(bar, baz);
  foo(
    bar,
    baz,
  );
  foo(bar,
    baz);
```

### Use a trailing comma for arguments, parameters, and list items, but only if they each have their own line.
Example:
```dart
List<int> myList = [
  1,
  2,
];
myList = <int>[3, 4];

foo1(
  bar,
  baz,
);
foo2(bar, baz);
```
Whether to put things all on one line or whether to have one line per item is an aesthetic choice. We prefer whatever ends up being most readable. Typically this means that when everything would fit on one line, put it all on one line, otherwise, split it one item to a line.
<br/><br/>
However, there are exceptions. For example, if there are six back-to-back lists and all but one of them need multiple lines, then one would not want to have the single case that does fit on one line use a different style than the others.
```dart
// BAD (because the second list is unnecessarily and confusingly different than the others):
List<FooBarBaz> myLongList1 = <FooBarBaz>[
  FooBarBaz(one: firstArgument, two: secondArgument, three: thirdArgument),
  FooBarBaz(one: firstArgument, two: secondArgument, three: thirdArgument),
  FooBarBaz(one: firstArgument, two: secondArgument, three: thirdArgument),
];
List<Quux> myLongList2 = <Quux>[ Quux(1), Quux(2) ];
List<FooBarBaz> myLongList3 = <FooBarBaz>[
  FooBarBaz(one: firstArgument, two: secondArgument, three: thirdArgument),
  FooBarBaz(one: firstArgument, two: secondArgument, three: thirdArgument),
  FooBarBaz(one: firstArgument, two: secondArgument, three: thirdArgument),
];

// GOOD (code is easy to scan):
List<FooBarBaz> myLongList1 = <FooBarBaz>[
  FooBarBaz(one: firstArgument, two: secondArgument, three: thirdArgument),
  FooBarBaz(one: firstArgument, two: secondArgument, three: thirdArgument),
  FooBarBaz(one: firstArgument, two: secondArgument, three: thirdArgument),
];
List<Quux> myLongList2 = <Quux>[
  Quux(1),
  Quux(2),
];
List<FooBarBaz> myLongList3 = <FooBarBaz>[
  FooBarBaz(one: firstArgument, two: secondArgument, three: thirdArgument),
  FooBarBaz(one: firstArgument, two: secondArgument, three: thirdArgument),
  FooBarBaz(one: firstArgument, two: secondArgument, three: thirdArgument),
];
```

### Prefer single quotes for strings
Use double quotes for nested strings or (optionally) for strings that contain single quotes. For all other strings, use single quotes.
<br/>Example:
```dart
print('Hello ${name.split(" ")[0]}');
```

### Consider using ⇒ for short functions and methods
But only use `⇒` when everything, including the function declaration, fits on a single line.
<br/>Example:
```dart
// BAD:
String capitalize(String s) =>
  '${s[0].toUpperCase()}${s.substring(1)}';

// GOOD:
String capitalize(String s) => '${s[0].toUpperCase()}${s.substring(1)}';

String capitalize(String s) {
  return '${s[0].toUpperCase()}${s.substring(1)}';
}
```

### Use `⇒` for inline callbacks that just return list or map literals
If your code is passing an inline closure that merely returns a list or map literal, or is merely calling another function, then if the argument is on its own line, then rather than using braces and a `return` statement, you can instead use the `⇒` form. When doing this, the closing `]`, `},` or `)` bracket will line up with the argument name, for named arguments, or the `(` of the argument list, for positional arguments.
<br/>For example:
```dart
  // GOOD, but slightly more verbose than necessary since it doesn't use =>
  @override
  Widget build(BuildContext context) {
    return PopupMenuButton<String>(
      onSelected: (String value) { print('Selected: $value'); },
      itemBuilder: (BuildContext context) {
        return <PopupMenuItem<String>>[
          PopupMenuItem<String>(
            value: 'Friends',
            child: MenuItemWithIcon(Icons.people, 'Friends', '5 new')
          ),
          PopupMenuItem<String>(
            value: 'Events',
            child: MenuItemWithIcon(Icons.event, 'Events', '12 upcoming')
          ),
        ];
      }
    );
  }

  // GOOD, does use =>, slightly briefer
  @override
  Widget build(BuildContext context) {
    return PopupMenuButton<String>(
      onSelected: (String value) { print('Selected: $value'); },
      itemBuilder: (BuildContext context) => <PopupMenuItem<String>>[
        PopupMenuItem<String>(
          value: 'Friends',
          child: MenuItemWithIcon(Icons.people, 'Friends', '5 new')
        ),
        PopupMenuItem<String>(
          value: 'Events',
          child: MenuItemWithIcon(Icons.event, 'Events', '12 upcoming')
        ),
      ]
    );
  }
```
The important part is that the closing punctuation lines up with the start of the line that has the opening punctuation, so that you can easily determine what’s going on by just scanning the indentation on the left edge.

### Prefer single line for short collection-if and collection-for
If the code fits in a single line don’t split it.
<br/>For example:
```dart
// BAD
final List<String> args = <String>[
  'test',
  if (useFlutterTestFormatter) '-rjson'
  else '-rcompact',
  '-j1',
  if (!hasColor)
    '--no-color',
  for (final String opt in others)
    opt,
];

// GOOD
final List<String> args = <String>[
  'test',
  if (useFlutterTestFormatter) '-rjson' else '-rcompact',
  '-j1',
  if (!hasColor) '--no-color',
  for (final String opt in others) opt,
];
```
Otherwise indent with 2 spaces
```dart
// GOOD
final List<String> args = <String>[
  'test',
  if (useFlutterTestFormatter)
    '-rjson.very.very.very.very.very.very.very.very.long'
  else
    '-rcompact.very.very.very.very.very.very.very.very.long',
  '-j1',
  if (!hasColor)
    '--no-color.very.very.very.very.very.very.very.very.long',
  for (final String opt in others)
    methodVeryVeryVeryVeryVeryVeryVeryVeryVeryLong(opt),
];
```

### Put spread inside collection-if or collection-for on the same line
Spreads inside collection-if or collection-for are used to insert several elements. It’s easier to read to have spread on the line of `if`, `else`, or `for`.
```dart
// BAD
final List<String> args = <String>[
  'test',
  if (condA) 
    ...<String>[
      'b',
      'c',
    ]
  else
    '-rcompact',
  for (final String opt in others)
    ...<String>[
      m1(opt),
      m2(opt),
    ],
];

// GOOD
final List<String> args = <String>[
  'test',
  if (condA) ...<String>[
    'b',
    'c',
  ] else
    '-rcompact',
  for (final String opt in others) ...<String>[
    m1(opt),
    m2(opt),
  ],
];
```

### Use braces for long functions and methods
Use a block (with braces) when a body would wrap onto more than one line (as opposed to using `⇒;` the cases where you can use `⇒` are discussed in the previous two guidelines).

### Separate the 'if' expression from its statement
(This is enforced by the `always_put_control_body_on_new_line` and `curly_braces_in_flow_control_structures` lints.)
<br/>Don’t put the statement part of an 'if' statement on the same line as the expression, even if it is short. (Doing so makes it unobvious that there is relevant code there. This is especially important for early returns.)
<br/>Example:
```dart
// BAD:
if (notReady) return;

// GOOD:
// Use this style for code that is expected to be publicly read by developers
if (notReady) {
  return;
}
```
If the body is more than one line, or if there is an `else` clause, wrap the body in braces:
```dart
// BAD:
if (foo)
  bar(
    'baz',
  );

// BAD:
if (foo)
  bar();
else
  baz();

// GOOD:
if (foo) {
  bar(
    'baz',
  );
}

// GOOD:
if (foo) {
  bar();
} else {
  baz();
}
```
We require bodies to make it very clear where the bodies belong.

### Align expressions
Where possible, subexpressions on different lines should be aligned, to make the structure of the expression easier. When doing this with a `return` statement chaining `||` or `&&` operators, consider putting the operators on the left hand side instead of the right hand side.
```dart
// BAD:
if (foo.foo.foo + bar.bar.bar * baz - foo.foo.foo * 2 +
    bar.bar.bar * 2 * baz > foo.foo.foo) {
  // ...
}

// GOOD (notice how it makes it obvious that this code can be simplified):
if (foo.foo.foo     + bar.bar.bar     * baz -
    foo.foo.foo * 2 + bar.bar.bar * 2 * baz   > foo.foo.foo) {
  // ...
}
// After simplification, it fits on one line anyway:
if (bar.bar.bar * 3 * baz > foo.foo.foo * 2) {
  // ...
}
```
```dart
// BAD:
return foo.x == x &&
    foo.y == y &&
    foo.z == z;

// GOOD:
return foo.x == x &&
       foo.y == y &&
       foo.z == z;

// ALSO GOOD:
return foo.x == x
    && foo.y == y
    && foo.z == z;
```

### Prefer += over ++
We generally slightly prefer `+=` over `++.`
<br/><br/>
In some languages/compilers postfix `++` is an antipattern because of performance reasons, and so it’s easier to just avoid it in general.
<br/><br/>
Because of the former, some people will use the prefix `++,` but this leads to statements that lead with punctuation, which is aesthetically displeasing.
<br/><br/>
In general, mutating variables as part of larger expressions leads to confusion about the order of operations, and entwines the increment with another calculation.
<br/><br/>
Using `++` does not make it obvious that the underlying variable is actually being mutated, whereas += more clearly does (it’s an assignment with an `=` sign).
<br/><br/>
Finally, `+=` is more convenient when changing the increment to a number other than 1.

### Use double literals for double constants
To make it clearer when something is a double or an integer, even if the number is a round number, include a decimal point in double literals. For example, if a function `foo` takes a double, write `foo(1.0)` rather than `foo(1)` because the latter makes it look like the function takes an integer.