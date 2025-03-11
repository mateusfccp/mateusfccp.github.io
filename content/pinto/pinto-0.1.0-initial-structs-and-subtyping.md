+++
title = 'pint° 0.1.0: initial structs and subtyping'
date = 2025-03-10T18:30:00-03:00
tags = ['Language', 'Flutter', 'Dart', 'pint°', 'Announcement', 'Release']
+++

As discussed in [our roadmap]({{< ref "advent-of-pinto" >}}), for this quarter
the objective is to provide basis support for functions parameters, structs and
subtyping. All three concepts are related somehow, so they had to be made
together.

pint° 0.1.0 solves this objective, at least in an initial form; there's already
some discussions I am thinking related to structs, and the subtyping relation is
still very primitive.

## Symbol literals

As a prerequisite for structs, symbols literals are being introduced. Symbol
literals are exactly the same as regular identifiers, with the same rules, but
they don't evaluate.

Their syntax (inspired by Common Lisp's keywords) is to simply prepend a colon
to an identifier:

{{< highlight alloy >}}
:any_valid_identifier
{{</ highlight >}}

I'm not sure about the future of symbol literals. They may be important for
macros in the future, or they may be replaced by something else entirely. For
now, however, they are an important part of structs.

## Structs

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

### Nameless struct members

Struct members **can** have the field name ommited. This doesn't mean that the
field is nameless, as happens with Dart records. Instead, a synthetic name is
generate in the format `$<index>`[^1]. This is mostly so we can interoperate
with Dart's positional fields.

[^1]: In the future, we may remove the `$`, becoming `:0` and `:1`, instead of
`:$0` and `:$1`. This will be part of an effort to independentize pint°'s
identifier rules from Dart's with some kind of name mangling in the
transpilation process.

{{< highlight dart >}}
let user = ("Joe", 28) // Desugared to (:$0 "Joe", :$1 28)
{{</ highlight >}}

Because of this, when using a pint° struct in interoperability with Dart,
positional fields/parameters can be reordered.

{{< highlight d >}}
// When compiled to Dart, will be compiled to the record ('Joe', 28)
let user = (:$1 28, :$0 "Joe")
{{</ highlight >}}

### Valueless struct members

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

This will be even more useful in function arguments, as we will see below.

### The death of the unit literal

The unit literal died. At least in a syntactical sense. Now, the unit is just
the empty struct, `()`, so nothing changes for the final code.

## Function arguments

Up to 0.0.4, we couldn't properly define arguments in functions. We had to use
`_` as a placeholder until the feature was implemented, and it would only work
with parameterless functions.

Syntactically, the placeholder indicated something that didn't change: functions
in pint° have **exactly**  one paremeter. There are no parameterless functions,
and no function can have more than one parameter. And this parameter **has** to
be a struct.

This, however, is a syntactic detail. In practice, you can define parameterless
functions by having the unit (empty struct) as your parameter, and regular
structs describe multiple parameters.

{{< highlight c >}}
let receiveNoParameter () =
  print "No parameters"

let receiveSingleParameter (:param String) =
  print "Single parameter"

let receiveMultipleParameters (:param1 String, :param2 int) =
  print "Multiple parameters"
  {{</ highlight >}}
  
You may ask: if all functions receive structs as its arguments, why can I call
`print` with a string literal? Shouldn't it be `print ("foo")` instead? Well, 
because of the singleton structs special property.

### Singleton structs

Singleton structs are structs that have exactly one field, and have a special
property: they are subtype of their member, and vice-versa.

{{< mermaid >}}
flowchart LR
    A["(T)"] --> B[T]
	B --> A
{{< /mermaid >}}

This is what allows one to simply omit the parenthesis when calling functions
like `print`.[^2]

[^2]: I am thinking if I want to keep this symmetry or not. It _is_ interesting
to be able to omit parenthesis. Some people may think that it makes the code
look less regular, and would prefer the parenthesis to be always mandatory.
This is a valid point, but more than that, forcing parenthesis may facilitate
the identification of invocations in the parsing step, which may allow for other
improvements later. Thus, this is still an open topic (much like everything in
pint° 😅)

## Subtyping

Basic subtyping has been implemented. The following hard rules were implemented:

* Every type is subtype of the top type;
* The bottom type is subtype of all types;
* Subtyping for structs based on their shape;
* Singleton struct special property.

Aside from this, the rules for "declared" supertypes were introduced. Except
that we can't declare classes nor anything like this in pint° currently, so they
are basically a mock. They work only for `int` and `double`, because they were
the only literals implemented whose type have a declared subtype relation to the
`num` type.

Thus, this should work:

{{< highlight c >}}
let id (num) = $0

let printNum (:number num) = print id number

let main () = printNum 42
{{</ highlight >}}

## Breaking changes to types

There was a breaking change to types (thus the bump to `0.1.0` instead of
`0.5.0`). Now, types definitions are more aligned with function definitions by
using structs as their argument.

For instance, the declaration of a `Option` type, before, was:

```
type Option(T) = Some(T value) + None
```

Now, it is:

```
type Option(T) = Some(:value T) + None
```

This will keep changing as we converge the language to be more consistent. In
the future, when we have macros, the idea is that `type` will be a macro from
the standard library, so we will build it completely with regular language
constructs. Thus, it is interesting that we slowly converge towards a more
regular syntax, avoding having many special cases.


## Smaller changes

There were some other smaller changes done to the tooling, not the language. I
improved the output of the static tests to better indicate what errors were
expected and which ones were emitted and should not have been. I also made some
improvements to the repository and implemented basic Github Actions to check for
issues and formatting and run the tests for all PRs.

## What is next?

According to our roadmap, in the next quarter we will tackle basic control flow,
which is much needed to do anything useful. Considering that we are still in the
first quarter, I will take this time to make some minor polishments and
decisions, and I will try to come up with a LUB algorithm which will be
necessary to proceed with some of the control flow constructs.[^3]

[^3]: Remember, in pint° everything is a expression, so an `if` will also be an
expression. This means that we have to be able to compute the type of the
expression. If an expressions has branches with different types, we will have to
use the LUB between those types to be the type of the expression.

