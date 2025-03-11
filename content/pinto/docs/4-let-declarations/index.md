+++
title = 'Let declarations'
date = 2024-10-04
lastmod = 2025-03-11
slug = 'let-declarations'
description = 'Declaring top-level names and functions.'
summary = 'Declaring top-level names and functions.'
+++

The `let` keyword allows you to declare names, both regular declartions and
function declarations. All names declared with `let` are available in the
top-level scope of the pint° program.

`let` declarations are always immutable, and thus cannot be redefined.

## Regular let declarations

A regular let declaration has the syntax `let a = b`, where `a` is a name and
`b` a expression which evaluated value will be assigned to `a`. The declared
identifiers can then be referred by other let declarations or in function calls.

Here are some examples:

{{< highlight alloy "linenos=table" >}}
let name = "Mateus Pinto"
let numberOfcatsHeHas = 1
{{</ highlight >}}

[^3]: Local bindings will be introduced later (probably in 0.0.5) with
let expressions. We may have other ways of introducing bindings, but let
expressions will be our first and main way of doing so.

## Function let declarations

By appending a parameter to a let declaration identifier, it is converted to
a function let declaration.

{{< highlight alloy "linenos=table" >}}
let printName (:name String) = print name
{{</ highlight >}}

A function has to have exactly one parameter, and it has to be a struct. You can
emulate parameterless functions by using the unit.

{{< highlight c >}}
let receiveNoParameter () =
  print "No parameters"

let receiveSingleParameter (:param String) =
  print "Single parameter"

let receiveMultipleParameters (:param1 String, :param2 int) =
  print "Multiple parameters"
{{</ highlight >}}

## Identifiers

As we are talking about name declarations, this may be a good place to define
what are valid or invalid identifiers in pint°.

For interoperability reasons, pint° chose to follow Dart's identifier rules, so:

* Identifiers can have all regular ASCII letters (no diacritics), numbers, `_`
and `$`.;
* Identifiers can't start with a number.

Thus, those are some example of valid identifiers:

- `myVariable`
- `_privateVar`
- `totalAmount`
- `calculate$um`
- `UserName`
- `MAX_VALUE`
- `isValid123`

And some invalid identifiers:

- `123start` (cannot start with a digit)
- `my-var` (hyphens are not allowed)
- `my/function` (operators can't be used)
