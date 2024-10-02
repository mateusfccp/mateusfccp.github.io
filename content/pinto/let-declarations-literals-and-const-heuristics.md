+++
title = 'Let declarations, literals and const heuristics'
date = 2024-09-22T09:00:00-03:00
tags = ['Language', 'Flutter', 'Dart', 'pint°']
+++

In the latest commit
([56c4c77](https://github.com/mateusfccp/pinto/commit/56c4c770f66e1bddb8926633cb6e493530ab090f))
I introduced two new possibilities for pint°:

* `let` declarations and
* some literals

They won't be relased immediately, but they are usable if you get pint° from master.

## `let` declarations

`let` declarations are top-level declarations that provides a symbol to the
global scope of the program.

For instance:
{{< highlight alloy "linenos=table" >}}
let language = "pint°"
let isAwesome = true
{{</ highlight >}}

They are barely usable now, but it's a start. You can use any expression, so,
for instance, in the future, you may be able to do this:

{{< highlight alloy "linenos=table" >}}
// This does not currently work, as let expressions has not been implemented yet.
let mySlightlyComplexComputation =
  let x = 10
      y = 10
  x + y
{{</ highlight >}}

## Basic literals

I introduced three types of literals to pint°:

* Unit literals: `()`;
* Boolean literals: `true` and `false`;
* Basic string literals: `"something"`.

Note that current string literals doesn't support nothing beside the most basic
of strings.

It doesn't support escaping, nor interpolation, nor multiline, nor anything.

Strings are probably the hardest literal to implement when you consider all
these possibilities.

One thing that I would like to say is that I don't pretend to support multiple
syntaxes for String literals in pint°. For instance, in Dart you may use both
single quoted strings (`'something'`) and double quoted strings (`"something"`).
pint° will only support the later. This allows us to simplify lexing/parsing and
to use `'` tokens to something else. Also, we have a more uniform syntax.

About numeral literals, we are still discussing how they will be. At first, I
though about making them with prefixed bases, but it introduces an issue when
dealing with hexadecimal literals:

* `107` (base 10)
* `100101b` (base 2)
* `777o` (base 8)
* `0FFFx` (base 16)

As seen above, hexadecimal would require an leading `0`, because `FFFx` is also
a valid identifier.

Another alternative is to simply use the same syntax as in Dart, with a leading
`0` + the base, so `0b100101`, `0xFFF` etc.

This is being
[discussed](https://discord.com/channels/1286023882515677236/1286051833005080576)
in the Discord server.

## `const` heuristics

While designing `let` declarations, we had to ask ourselves: _when should a
`let` declaration be compile to `const` instead of `final`?_

Dart allows you to explicitly instantiate a object with a `const` constructor
by using the `const` keyword.

Using `const` in Dart will allow the object to be included in the final compiled
code as a contant object, which can, supposedly, give us better performance.
There's even some recommended lint that suggests using `const` whenever
possible:

* `prefer_const_constructors`
* `prefer_const_literals_to_create_immutables`
* `prefer_const_declarations`

However, many Dart/Flutter users complain of this lint or even disable it. More
importantly, recently the Flutter team has been investigating the impact of
`const` in Flutter code, and the conclusion were that, although in [artificial
benchmarks they could lead
to some performance gains](https://github.com/flutter/flutter/pull/148261), when
benchmarks that emulate real-life conditions are conducted, ["the benchmarks are
not showing sufficient evidence to suggest that there is a statistically
significant difference in performance between const and nonconst."
](https://github.com/flutter/flutter/issues/149932#issuecomment-2332522293).

Because of this, and considering that Flutter is the primary target of pint°,
we decided to simply not support explicit consting.

Considering that making everything `const` can change the semantics of a
program, we decided instead to make everything non-const except when we are
sure that making it `const` won't change the semantics of the program. This
means, basically, simple const literals.

## What's next

Aside form the discussions about string and number literals, the nexts steps
are going to work on `let` declarations with parameters, i.e. top-level
functions.
