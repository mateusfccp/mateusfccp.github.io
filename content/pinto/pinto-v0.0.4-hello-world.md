+++
title = 'pint° v0.0.4: hello, world!'
date = 2024-10-02T08:30:00-03:00
tags = ['Language', 'Flutter', 'Dart', 'pint°', 'Announcement', 'Release']
+++

pint° v0.0.4 introduces a few features that allow one to finally make a "hello,
world": basic literals, let declarations, method calling and non-class imports.
Two of them were already highlighted (although partially) in a
[previous post]({{< ref "let-declarations-literals-and-const-heuristics" >}}),
but we will show them again, now in their full implementation.

## Basic literals

The following literals are now supported in pint°:

### Unit literals

As basic as it can be, unit literals are `()`. They are currently their own
thing, but when we introduce records/tuples/structs, they will be generalized
to be part of them.

Dart has no inherent concept of a unit or unit literal (although it's not hard
to implement your own, and `Null` itself is a singletone type), so how unit
compiles to Dart will depend on some heuristics, described in a
[previous post]({{< ref "pinto-v0.0.1-released#an-overview-of-the-type-system" >}}).

### Boolean literals

Boolean are also as basic as they could be, and the follow what Dart (an many
other languages) do: `true` and `false`.

### String literals

For 0.0.4, we don't support advanced features that one could expect for a
string literal, like escaping, raw string, multi-line literals, or anything
fancy, but we included a basic support for them.

In pint°, string literals are always double-quoted[^1]. So, for instance:
```dart
"lorem ipsum dolor sit amet"
```

[^1]: Unlike Dart, our compilation target, which supports both single quotes
and double quotes. We decided for this because by doing so we can use the `'`
token for other things, aside from a more uniform syntax.

### Number literals

This one is new in relation to the last post. As said earlier, we are still
discussing some details about how we will handle numbers bases (and other
things), so we decided to ship a basic version of it that only supports the
most simple case for both integer and double literals.

Number literals support digit divisors out of the box, so here are some
examples of some number literals:

```
42
1_000
1__000_000__000_000
1.0
10_000.0
0.000_000_1
```

## Let declarations

pint° now supports let declarations. They come in two flavors: regular let
declarations and function let declarations.

### Regular let declarations

As the constrasting name implies, this one is the one presented in the post
aforementioned. It allows one to declare associate a name with a value[^2].

Here are some examples:

{{< highlight alloy "linenos=table" >}}
let name = "Mateus Pinto"
let catsHeHas = 1
{{</ highlight >}}

Let declarations introduces the identifier in the top-level scope of your
program[^3]. These identifiers can then be referred by other let declarations
or in function calls.

[^2]: Let declarations are not reassignable. They can be either be compiled
to Dart's `const` declarations or `final` declarations. This will depend on
some
[heuristics]({{< ref "let-declarations-literals-and-const-heuristics#const-heuristics" >}}),
but most of the time it will be compiled to `final`.

[^3]: Local bindings will be introduced later (probably in 0.0.5) with
let expressions. We may have other ways of introducing bindings, but let
expressions will be our first and main way of doing so.

### Function let declarations

By appending a parameter to a let declaration identifier, it is converted to
a function let declaration[^4].

{{< highlight alloy "linenos=table" >}}
let myFunction param = "returning a string"
{{</ highlight >}}

pint° functions have always a single parameter[^5], and current implementation
parameters are simply ignored, so you can't use them. However, this code works
and compiles to a parameterless function in Dart.

{{< highlight dart "linenos=table" >}}
String myFunction() => 'returning a string';
{{</ highlight >}}

[^4]: Implementation detail: currently, let function declarations are their own
thing, but in the future they will simply be a syntax sugar to a regular let
declaration to a function literal, which we still don't have.

[^5]: Multiple parameters will be handled in the future with
records/tuples/structs.

## Function calls

If an identifier has the function type, it can be called.

{{< highlight alloy "linenos=table" >}}
let stringFunction _ = "return string"
let otherStringFunction _ = stringFunction "this parameter will be ignored, but who cares?"
{{</ highlight >}}

## Non-classes imports

In 0.0.3, pint° could already import from Dart. However, it only imported
classes. From this version, it also import top-level identifiers and
methods[^6].

[^6]: Other things are imported, like type aliases, but I'm not sure if they
work, as I didn't specifically supported them. Also, some unsupported types
won't be imported, like records.

### This enables us to...

Finally have a (useless, but still) hello world in pint°.

{{< highlight alloy "linenos=table" >}}
let main _ = print "Hello, world!"
{{</ highlight >}}

This code will be compiled to a valid "hello world" Dart code:
{{< highlight dart "linenos=table" >}}
void main() => print('Hello, world!')
{{</ highlight >}}

## What's next?

These changes are already in the GitHub repository, but I still didn't release
it to pub.dev. We want to update the syntax highlighting for our VSCode
extension before doing so.

For the next version, three things will be the focus:
1. Implementing records/tuples/structs (the name is yet to be decided);
1. By consequence, implementing function parameters;
1. And, for this to be able to happen, we need to have some basic[^7] type
system implemented (subtype/supertype relation).

[^7]: With basic, I mean we won't have bounds for type parameters.

## Contributors

A special thanks to
[seha](https://github.com/seha-bot/pinto/commits?author=seha-bot),
that made his first contribution to our repository by implementing number
literals.
