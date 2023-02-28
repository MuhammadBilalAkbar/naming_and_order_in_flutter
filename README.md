# Naming and Order in Flutter

## Names

### DO use terms consistently
Use the same<br/> name for the same thing, throughout your code. If a precedent already exists outside your API that users are likely to know, follow that precedent.<br />
good:
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
