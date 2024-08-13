+++
title = 'pint° v0.0.1 released'
date = 2024-08-13T16:50:00-03:00
tags = ['Language', 'Flutter', 'Dart', 'pint°']
+++

In the [last post]({{< ref "the-pinto-programming-language" >}}) I said I was
working on a toy language called pint°. Today, I'm releasing its first version.
In this post, I will overview what has been implemented and the current
limitations.

The repository can be seen [here](https://github.com/mateusfccp/pinto), and the
`pub.dev` package [here](https://pub.dev/packages/pinto).

# Changes on this release

Not exactly changes, because it's the first release, but here are some
differences from what we had the last time I posted about this language.

## Scope

For this first zero-version, the scope is basically what was described in the
last post. However, as I implemented a basic type resolution step, I had to
implement [imports](#imports), and I implemented [commentaries](#commentaries)
to help me in testing a file without having to cut and paste code into another
places whenever I wanted to enable/disable a specific type definition.

The only intentional addition to the scope of the last post is the
[type identifier sugaring](#sweet_types), which is described below.

However, at the time I posted the last post, many things were underspecified and
there were a lot of changes I had to make to accommodate type resolving.

For instance, the type variants parameter before followed the `identifier`
grammar, so this was not a possible definition for a type:

{{< highlight io "linenos=table" >}}
type List(T) = Cons(T head, List(T) tail)
             + Nil
{{< /highlight >}}

The code before would not be parsed, as `List(T)` in the `tail` parameter is not
an identifier, only the `List` part is.

I had to tweak the grammar a lot and update the parser accordingly to
accommodate for these cases and others.

## Type resolution

I implemented a simple type resolver for pint°, which will check for common
issues when defining a type. For instance:

- we now check for types in scope. So, for instance, `type Foo = Foo(T value)`, 
  unless there's an imported package with a defined type `T`, will throw an
  error;
- we check for a type number of type parameters to check if the user passed the
  correct number of arguments. For instance, for `List(T)`, declaring a
  parameter of type `List` or type `List(T, U)` are both errors;[^1]
- we check for already defined type parameters. `type Foo(T, T) = ...` is now an
  error.

[^1]: For type definitions, type parameters can't be omitted. In Dart, omitted
      type parameters will either try to infer it from context, which is
	  impossible in this case, or fallback to `dynamic`. I _could_ make omitted
	  type parameters fallback to `⊤`, but I won't do that, at least for now.
	  Another alternative would be to apply it partially and return a
	  `Type → Type`.

## Commentaries

Commentaries are now a thing. This is not set in stone, but by now I have gone
to the easy way of C-like commentaries:

- `//` is a single-line commentary
- `/*` and `*/` respectively open and close a multiline commentary
  (nesting is supported)

## Sweet types

I introduced some sugared syntax for common type identifiers:

- `⊤` is the top type, which compiles to `Object?`;
- `⊥` is the bottom type, which compiles to `Never`;
- `[T]` means `List(T)`, which compiles to `List<T>`;
- `{T}` means `Set(T)`, which compiles to `Set<T>`;
- `{K: V}` means `Map(K, V)`, which compiles to `Map<K, V>`;
- `T?` means `Optional(T)`, which compiles to `T?`[^2].


[^2]: Currently, there's no distinct internal representation for optional types,
      and some workarounds exist to identify them. The idea, however, is to
	  introduce a proper optional type into the language, which will still be
	  compiled into a nullable type in Dart.

### Limitations

#### Records

Currently, there's no way to express complex records in Dart. As Dart records
not representable with regular type identifier syntax, this is a hard limitation
with no workaround currently available.

What you _can_ do, is use the `Record` supertype to receive any record, but
this is far from ideal.

I didn't want to touch records yet for two reasons:

- they are syntactically more complex than other types, so I have to think about
  how I want to describe then in pint°, considering out interoperability
  objective;
- I'm thinking of having records as first-class types in pint°, so they will
  probably work differently from how we use it on Dart.
  
#### Functions

There's also no way to describe function types in pint°. As currently pint° does
not have support for function definitions, this is not a big problem. Type
definitions _can_ have functions as parameters, but I wouldn't recommend it.

This can be worked around in a similar way to record, by using the `Function`
type as a catching-all type.

## Imports

I introduced an import syntax. I don't know if it will be final, but it's
currently as follows:

- `import @package` imports Dart packages, i.e. it's compiled to `dart:package`;
- `import package` imports an external package, according to what is described
  in the `pubspec.yaml` file. It imports the main file. For instance,
  `import foo` imports `package:foo/foo.dart`;
- `import package/library` imports `library` from the described package. For
  instance, `import foo/bar` imports `package:foo/bar.dart`.

All pint° programs come with `dart:core` implicitly imported, so basic symbols
like `int`, `String`, `List` etc are instantly available in all pint° programs.

### Limitations

#### Relative imports

Relative imports are not a thing. Some people may like it, as many use only
absolute imports in Dart. I'm still deciding whether we should or not support
relative imports.

For now, you can use absolute imports with the full notation. For instance, if
you are working in a file `<project>/lib/src/feature/implementation.dart` and
you want to import `<project/lib/src/feature/helper.dart`, you can import it
with `import project/src/feature/helper`.

#### Redundant imports

Importing the same package many times is an undefined behavior. I have
no idea if this breaks the resolver or will simply do nothing. Ideally, we want
it to be at least a warning to do this and be sure that doing this won't break
anything.

#### Modifiers

Modifiers like `shows`, `hides` and `as` are not available and there's no way to
workaround it.

## Other minor changes

- Now, type definitions generated code have a `toString` override that contains
  the values of all the fields.

# How to try it

The package is published in the `pub.dev`. It contains all the code for the
lexer, parser, resolver, and compiler.

For a quick test, an executable is also available, that can be installed by
activating it:

```sh
dart pub global activate pinto
```

From this, you can use `pinto <pinto_file>` to get the compiled file outputted
to your stdout.

Here's a sample code you can try:

{{< highlight io "linenos=table" >}}
import @async

type Unit = Unit

type Bool = True + False

type Option(T) = Some(T value)
               + None

type Either(T, U) = Left(T value)
                  + Right(U value)

type Complex(T) = Complex(
  [⊤] listOfAny,
  [T] listOfT,
  {T} set,
  {T: T} map,
  T? maybeT,
  Future(T) futureOfT,
  int aSimpleInt,
  {{T?} : [Future(T)]} aMonster // I was pretty sure I supported trailing commas, but I think we have a bug
)
{{< /highlight >}}

This generates the following Dart code:

{{< highlight dart "linenos=table" >}}

import 'dart:async';

final class Unit {
  const Unit();

  @override
  bool operator ==(Object other) => other is Unit;

  @override
  int get hashCode => runtimeType.hashCode;

  @override
  String toString() => 'Unit';
}

sealed class Bool {}

final class True {
  const True();

  @override
  bool operator ==(Object other) => other is True;

  @override
  int get hashCode => runtimeType.hashCode;

  @override
  String toString() => 'True';
}

final class False {
  const False();

  @override
  bool operator ==(Object other) => other is False;

  @override
  int get hashCode => runtimeType.hashCode;

  @override
  String toString() => 'False';
}

sealed class Option<T> {}

final class Some<T> implements Option<T> {
  const Some({required this.value});

  final T value;

  @override
  bool operator ==(Object other) {
    if (identical(this, other)) return true;
    return other is Some<T> && other.value == value;
  }

  @override
  int get hashCode => value.hashCode;

  @override
  String toString() => 'Some(value: $value)';
}

final class None implements Option<Never> {
  const None();

  @override
  bool operator ==(Object other) => other is None;

  @override
  int get hashCode => runtimeType.hashCode;

  @override
  String toString() => 'None';
}

sealed class Either<T, U> {}

final class Left<T> implements Either<T, Never> {
  const Left({required this.value});

  final T value;

  @override
  bool operator ==(Object other) {
    if (identical(this, other)) return true;
    return other is Left<T> && other.value == value;
  }

  @override
  int get hashCode => value.hashCode;

  @override
  String toString() => 'Left(value: $value)';
}

final class Right<U> implements Either<Never, U> {
  const Right({required this.value});

  final U value;

  @override
  bool operator ==(Object other) {
    if (identical(this, other)) return true;
    return other is Right<U> && other.value == value;
  }

  @override
  int get hashCode => value.hashCode;

  @override
  String toString() => 'Right(value: $value)';
}

final class Complex<T> {
  const Complex({
    required this.listOfAny,
    required this.listOfT,
    required this.set,
    required this.map,
    required this.maybeT,
    required this.futureOfT,
    required this.aSimpleInt,
    required this.aMonster,
  });

  final List<Object?> listOfAny;

  final List<T> listOfT;

  final Set<T> set;

  final Map<T, T> map;

  final T? maybeT;

  final Future<T> futureOfT;

  final int aSimpleInt;

  final Map<Set<T?>, List<Future<T>>> aMonster;

  @override
  bool operator ==(Object other) {
    if (identical(this, other)) return true;
    return other is Complex &&
        other.listOfAny == listOfAny &&
        other.listOfT == listOfT &&
        other.set == set &&
        other.map == map &&
        other.maybeT == maybeT &&
        other.futureOfT == futureOfT &&
        other.aSimpleInt == aSimpleInt &&
        other.aMonster == aMonster;
  }

  @override
  int get hashCode => Object.hash(
        listOfAny,
        listOfT,
        set,
        map,
        maybeT,
        futureOfT,
        aSimpleInt,
        aMonster,
      );

  @override
  String toString() =>
      'Complex(listOfAny: $listOfAny, listOfT: $listOfT, set: $set, map: $map, maybeT: $maybeT, futureOfT: $futureOfT, aSimpleInt: $aSimpleInt, aMonster: $aMonster)';
}

{{< /highlight >}}

I obviously wouldn't recommend using it in anything meant for production. We
are going to have breaking changes each new release, there won't be
any stability soon, but we can have some fun trying this language.

Please, if you find any bugs, feel free to open an issue or a pull request.

# Some future ideas

## An overview of the type system

I didn't think too much about the type system yet, but there's one problem in
Dart that I want to avoid: Dart's type system has three
[_top type_](https://en.wikipedia.org/wiki/Top_type)s, `Object?`, `dynamic` and
`void`.

<figure style="text-align: center">
{{< mermaid >}}
flowchart BT
  O[Object] --> O?
  Null --> O?
  O?[Object?] --> V[void]
  V --> O?
  O? --> D[dynamic]
  D --> V
  D --> O?
  V --> D
  T? --> O?
  T? --> V
  T? --> D
  T? --> Null
  T --> T?
  T --> O?
  T --> O
  T --> V
  T --> D
  Never --> T?
  Never --> Null
  Never --> T
{{< /mermaid >}}
<figcaption>It looks terrible. Because it is.</figcaption>
</figure>

There are many reasons for this, but I want to avoid it altogether. pint°'s
type system will streamline this by having a single top type, `⊤`[^3].

[^3]: Don't mix up the _verum_ symbol (`⊤`) with the capital letter `T`.
      Depending on the font, they can be very similar. The _verum_ symbol, also
	  called _tee_ or _up tack_, represents the truth value in logic, or the top
	  type in [type theory](https://en.wikipedia.org/wiki/Type_theory).

<figure style="text-align: center">
{{< mermaid >}}
flowchart BT
  Top[⊤]
  T --> Top
  Opt["Optional(T)"] --> Top
  Bottom[⊥] --> T
  Bottom --> Opt
{{< /mermaid >}}
<figcaption>By having a single <em>top type</em>, we have a simpler type system.
</figcaption>
</figure>

Another difference, which is demonstrated in the charts above, is the `Null`
type. In Dart, the `Null` type is a
[_singleton type_](https://en.wikipedia.org/wiki/Unit_type)[^4] inhabited by
`null` itself. There's nothing inherently bad to it, but it's a little confusing
that it's not a subtype of `Object`, and every nullable type `T?` (including
`Object?`, a _top type_) is actually `T & Null`. Dart doesn't even support
intersection types![^5]

[^4]: Dart currently has two singleton types by default, `Null` and `()`. You
      can also define your own singleton types with regular classes.
	  
[^5]: Dart has two built-in intersection types: `Object?`, which is
      `Object & Null`, and `FutureOr<T>`, which is `Future & T`. I'm considering
	  if I want to support user-defined intersection types in pint°. It would be
	  surely interesting, and also a challenge to compile it to Dart, although
	  not impossible.

pint° intends to have a simpler approach by having a single canonical _singleton
type_ (let's call it `()` for now), and by having it as a subtype of `⊤`.
Optionality is then not represented by an intersection type but by a proper sum
type `Option(T)`.[^6]

[^6]: Don't worry, I intend to provide all the ergonomics inherent to Darts
      nullable types, so it becomes easy to work with `Option(T)`, and even
	  more.

Considering interoperability heuristics, we will have to make some
considerations. Here is more-or-less a table of equivalencies.

| Dart                                | pint°         |
| ----------------------------------- | --------------|
| ` Object?`                          | `⊤`           |
| ` dynamic`                          | `⊤`           |
| `void`, in a covariant position     | `()`          |
| `void`, in a contravariant position | `⊤`           |
| `Object`,                           | `Some(⊤)`[^7] |
| `Null`                              | `()`          |
| `T & Null` (`T?`)                   | `Option(T)`   |
| `T`, when bound to `Object`         | `Some(T)`     |

[^7]: This is not quite true, as `Some(⊤)` allows for `Some(Option(⊤))`,
      which is isomorphic to `Option(⊤)`. I'm looking for a way to close this
	  gap statically (within the type system). There are many ways to do it, but
	  some of them would lead to more confusion. One clean but complex
	  way to do this is to have a dependent type system that provides a
	  constraint to `Some(T)` so that `T` would never be `None`. This may be a
	  thing in the future, as I may want to introduce a kind of dependent type
	  for dealing with default values on records.
	  
## A word on records

I have the idea to remove the wall between function parameters and records in
pint°. By doing so, every function will have a single parameter, and functions
with many parameters will be represented by functions with a record as
a parameter.

I'm not completely sold on this idea, but it's something that I would like to
experiment, and it allows for interesting expressiveness, as you can build
function parameters dynamically before passing them to the functions. It would
be even more interesting with additional operations for the record type, like
record spread and record built-in `with`.

Doing so has its intrinsic challenges. As we have Dart as our target, we have to
consider its type system when designing ours. In Dart, parameters can have
default values. If we go to this route of records isomorphic to arguments, 
probably have to introduce some type of dependent types

For instance:

{{< mermaid >}}
flowchart BT
  Def["(String name, Default(int, 18) age)"]
  Non["(String name)"]
  Non2["(String name, int age)"]
  Non --> Def
  Non2 --> Def
{{< /mermaid >}}

This is just a strawman for the idea proposed here. If we have both
`(String, int)` and `(String)` to be a subtype of `(String, Default(int, _))`,
then our type system is sound, and by having the default value encoded into the
type allows us to compile it to an equivalent Dart code.

# What's next?

I actually don't know what I'm going to do next. Some things come to my mind:

- Tests: the language has 0 tests. I actually don't know if I'm going to take
  this as the next step, as the language is ever-changing at all levels.
  Also, writing tests is boring;
- Some tooling: even if the language is small, I think it should have proper
  ergonomics from the start. I'm thinking of making a LSP server for the
  language, and an extension for VSCode to connect to this language server.
  Also, I would be surely tweaking more the resolver to have a more useful
  analysis;
- Some features: what I want to do the most is to keep designing the language
  itself. There are many things to do before it can be a Turing-complete
  language able to be fully integrated into a Dart project, and I have many
  ideas for it.
  
If you are interested in following the project, follow the GitHub repository and
the [RSS]({{< ref path="tags/pint°" outputFormat="rss" >}}) for this topic in
this blog, and if you want to contribute to the language, I'm fully open to
discussing ideas and reviewing MRs.
