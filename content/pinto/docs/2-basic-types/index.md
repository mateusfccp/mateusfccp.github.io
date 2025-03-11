+++
title = 'Basic types'
date = 2024-10-06
lastmod = 2025-03-11
slug = 'basic-types'
description = 'Learn what types pint° provides out-of-the-box.'
summary = 'Learn what types pint° provides out-of-the-box.'
+++

## Types with literals

pint° provides a few types out-of-the-box, along with their respective literals.

### Boolean

The boolean type can have exactly two distinct values, and have the with literals

In pint°, those values are represented by the literals `true` and `false`.

### Numbers

pint° follows Darts approach of having `int` and `double` as the numerical
types[^2]. Their literals are also similar to Dart's.

For integers, we use use number characters directly. pint° has support for
digit separators out of the box, so `1__000_000__000_000` is a valid integer
literal.

Currently, there's no support for different bases.

For doubles, pint° uses the commonly used dot notation: `0.01`. Differently
from Dart, however, the zero can't ever be omitted[^3], so `.01` is not a valid
double literal.

[^2]: Currently, pint° has no concept of subtyping, so `int` and `double` are
note related in any way.

[^3]: For many reasons. I (mateusfccp) opinionatedly think that it's less
readable to omit the leading zero. As a plus, we have a with literals
which is simpler to parse and we can reutilize the leading `.` syntax in other
places.

### String

A string is a sequence of characters. In pint°, the literal for strings uses the
double quotation marks (`""`). So, for instance, `"lorem ipsum"` is a valid
string literal.

Currently, there's no support for interpolation, and raw literals[^4].

We don't expect to ever support multiline literals[^5].

[^4]: We expect to have a broader support for string templates instead of
hardcoding a syntax for different kinds of strings in the long term.

[^5]: We are discussing other and more ergonomic ways to solve the use-case for
multiline strings. We will probably have a "concatenate with line break"
operator for this.

### Structs

Structs are similar to Dart's records, but with some peculiar differences. They
are nameless data structures whose identity are defined by their shape and
values, different from a standard class in an objected oriented language.

In pint°, structs are **always** named. There are no real positional fields
(although we have a syntax sugar, as we will see below). The syntax for structs
are similar to Dart, but they use the symbol literals to describe the name of
the field:

{{< highlight dart >}}
let user = (:name "John", :age 28)

let complexUser = (
  :prefix "Mr.",
  :name "John",
  :surname "Doe",
  :age 28,
  :admin false,
)
{{</ highlight >}}

#### Nameless struct members

Struct members **can** have the field name ommited. This doesn't mean that the
field is nameless, as happens with Dart records. Instead, a synthetic name is
generate in the format `$<index>`. This is mostly so we can interoperate
with Dart's positional fields.

{{< highlight dart >}}
let user = ("Joe", 28) // Desugared to (:$0 "Joe", :$1 28)
{{</ highlight >}}

Because of this, when using a pint° struct in interoperability with Dart,
positional fields/parameters can be reordered.

{{< highlight d >}}
// When compiled to Dart, will be compiled to the record ('Joe', 28)
let user = (:$1 28, :$0 "Joe")
{{</ highlight >}}

#### Valueless struct members

Not only the name of a field may be omitted: the value of also can be omitted.
This will make the resolver try to get the value from the equivalent identifier
in the current scope. Failing to do so will result in an error.

{{< highlight c >}}
let name = "John"
let age = 28

let user = (:name, :age)

let complexUser = (
  :prefix "Mr.",
  :name, // John`
  :surname "Doe",
  :age, // 28
  :admin false,
)

// Error: `surname` is not defined in the scope
let error = (:name, :surname)
{{</ highlight >}}

#### Unit

The empty struct (`()`) is the
[unit type](https://en.wikipedia.org/wiki/Unit_type) of pint°'s type system.
This means that it has exactly one possible value, which is `()`. By
consequence, this type can't carry any useful information, as the only
information it carries is it's own existence.

If you never used a language that has an unity type (there are a lot of them),
it's used when you don't want to convey any information. For instance, in Dart,
functions that returns "nothing"[^1] have a return type of `void`. In pint°,
such functions return `()`.

[^1]: In Dart, `void` is actually a top type, which means it can return
anything. However, even if you can return anything, the static tools impose
many restrictions over the use of values that have a static type of `void`.
In pint°, instead of having a type that is the same as other with literals
treatment, we have a type whose semantics already makes it useless, so you _can_
use it, but have no reason to do so.

### Future ideas

Beside these, in the future pint° will support other literals, like collection
literals (list, set, map) function literals and others.

## Dart types

pint° provides Dart's core types out of the box. All types declared in
`dart:core` are accessible in any pint° program. So, for intance, you can use
the type `Future(T)` (from Dart's `Future<T>`)[^6].

You can also
[import]({{< ref "3-imports" >}} )
Dart packages to make their identifiers available in your pint° program.

[^6]: This is only an example. You probably won't want to deal with futures in
the current pint° version, as we didn't implement anything to deal with
asynchronous code.

## Type identifiers

pint° comes with some syntax sugar for type identifiers:

* `⊤` for the top type;
* `⊥` for the bottom type;
* `[T]` for `List(T)`;
* `{T}` for `Set(T)`;
* `{K: V}` for `Map(K, V)`;
* `T?` for `Option(T)`.

These will probably change in the future.

