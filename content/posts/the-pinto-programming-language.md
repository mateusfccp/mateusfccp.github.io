+++
title = 'The pint° programming language'
date = 2024-08-07T21:25:00-03:00
lastmod = 2024-08-13T12:00:00-03:00
tags = ['Language', 'Flutter', 'Dart', 'pint°']
+++

I started making a toy language that's supposed to compile to Dart and have
interoperability with it.

The main objectives of the language are:

- Have seamless[^1] interoperability with stable Dart;
- Generate a legible Dart that can be used as is;
- Be terser and more expressive than Dart;
- Provide a powerful macro system with great ergonomy;
- Try to solve the greatest pain points when using Dart, especially with
  Flutter.[^2]

This post will be an introduction to the language specification and what we
have now.

[^1]: By _seamless_, I mean that you should be able to work on a 100% pint°
      project by importing Dart packages, or a mix between Dart and pint°
	  files without any problem. Also, there shouldn't be surprises when
	  dealing with language differences. For this, we have to make some
	  compromises, like the identifier grammar.

[^2]: This language is intended to support Flutter as first-class. This means
      that, if we have to choose between an approach that is better for Flutter
	  but worse for general programming, we are doing what is better for
	  Flutter. One of the key factors for this to work is to have a good and
	  ergonomic macro system.

# Identifiers

Identifiers are exactly as in Dart. As the language compiles to Dart, it's
easier if we do it this way[^3].

[^3]: Particularly, I would prefer to support full Unicode for identifiers.
Why can't I define my variable as `final String 🔑`? Doing so, however, would
harm interoperability.

{{< highlight ebnf "linenos=table" >}}
identifier ::= identifier_start identifier_part*

identifier_start ::= [a-z] | [A-Z] | "_" | "$"
identifier_part ::= identifier_start  | digit

digit ::= [0-9]
{{< /highlight >}}

Some examples of valid identifiers:

- `myVariable`
- `_privateVar`
- `totalAmount`
- `calculateSum`
- `UserName`
- `MAX_VALUE`
- `isValid123`

And some invalid identifiers:

- `123start` (cannot start with a digit)
- `my-var` (hyphens are not allowed)
- `my/function` (operators can't be used)

# Type definitions

The first construct I'm working on for the language is type definition.

{{< highlight ebnf "linenos=table" >}}
<type_declaration> ::= "type" <identifier> ( "(" <identifier> ( "," <identifier> )* ")" )? "=" <type_definition> ( "+" <type_definition> )*
<type_definition> ::= <identifier> ( "(" <identifier> <identifier> ( "," <identifier> <identifier )* ")" )?
{{< /highlight >}}

The `type` keyword defines an opaque type with structural identity.

{{< highlight io "linenos=table" >}}
type User = User(
  int id,
  String name,
  int age,
)
{{< /highlight >}}

<details>
<summary>Generated Dart code</summary>

{{< highlight dart "linenos=table" >}}
final class User {
  const User({
    required this.id,
    required this.name,
    required this.age,
  });

  final int id;
  final String name;
  final int age;

  @override
  bool operator ==(Object other) {
    if (identical(this, other)) return true;

    return other is User &&
        other.id == id &&
        other.name == name &&
        other.age == age;
  }

  @override
  int get hashCode => Object.hash(id, name, age);
}
{{< /highlight >}}
</details>

You can also define sum types with the operator `+`:

{{< highlight io >}}
type LogLevel = Debug + Info + Warn + Error + Fatal
{{< /highlight >}}

<details>
<summary>Generated Dart code</summary>

{{< highlight dart "linenos=table" >}}
sealed class LogLevel {}

final class Debug implements LogLevel {
  const Debug();

  @override
  bool operator ==(Object other) => other is Debug;

  @override
  int get hashCode => runtimeType.hashCode;
}

final class Info implements LogLevel {
  const Info();

  @override
  bool operator ==(Object other) => other is Info;

  @override
  int get hashCode => runtimeType.hashCode;
}

final class Warn implements LogLevel {
  const Warn();

  @override
  bool operator ==(Object other) => other is Warn;

  @override
  int get hashCode => runtimeType.hashCode;
}

final class Error implements LogLevel {
  const Error();

  @override
  bool operator ==(Object other) => other is Error;

  @override
  int get hashCode => runtimeType.hashCode;
}

final class Fatal implements LogLevel {
  const Fatal();

  @override
  bool operator ==(Object other) => other is Fatal;

  @override
  int get hashCode => runtimeType.hashCode;
}
{{< /highlight >}}
</details>

Types can also be parameterized:

{{< highlight io >}}
type Option(T) = Some(T value) + None
{{< /highlight >}}

<details>
<summary>Generated Dart code</summary>

{{< highlight dart "linenos=table" >}}
sealed class Option<T> {}

final class Some<T> implements Option<T> {
  const Some({
    required this.value,
  });

  final T value;

  @override
  bool operator ==(Object other) {
    if (identical(this, other)) return true;

    return other is Some &&
        other.value == value;
  }

  @override
  int get hashCode => value.hashCode;
}

final class None implements Option<Never> {
  const None();

  @override
  bool operator ==(Object other) => other is None;

  @override
  int get hashCode => runtimeType.hashCode;
}
{{< /highlight >}}
</details>

# Why compile to Dart instead of the Dart Kernel?

When [ClojureDart](https://github.com/Tensegritics/ClojureDart) was announced, 3
years ago, I asked the same thing.

{{< twitter user="mateusfccp" id="1402712007393042438" >}}

I never got a response from Cristophe (or anyone), but it's actually not viable
to compile to Dart Kernel.

The truth is: even though Dart is an open-source project, som things weren't
made to be consumed by others besides the Dart team itself. The Dart VM, for
instance. The Dart VM interprets Dart Kernel, an IR that is generated by the
language's CFE.

{{< mermaid >}}
flowchart LR
    A[Dart\nSource] --> B[[CFE]]
    B --> C["Kernel AST\n(binary)"]
    C --> D[[Dart VM]]
{{< /mermaid >}}

It's only logical that a language that compiles to Dart should compile to run
in the VM directly. However, as said before, this is not what the Dart teams
intend.

The [Dart Kernel "documentation"](https://github.com/dart-lang/sdk/blob/f83c6d5e999eed7318ab4e39c6e58b6062ba7ddd/pkg/kernel/README.md)
states the following:

> The APIs in this package are in an early state; developers should be careful
> about depending on this package. In particular, there is no semver contract
> for release versions of this package. Please depend directly on individual
> versions.

The thing is, the Dart Kernel never stabilized, since it was born, almost a
decade ago. The last update to the README was 6 years ago. So I decided to ask
directly in Dart's discord channel, where many (if not all) members of the Dart
team are present.

Egorov gave a direct answer:

> No. It is not going to be stable. It changes all the time and is not intended
> for external use.

So, the situation with Dart Kernel is:

- It will never be stable;
- It changes all the time;
- There are no guarantees;
- There's barely any documentation (we have to look the source code to
  understand how it works);
- It's not supposed to be used by anyone except the Dart team itself.

Considering all this, I see why ClojureDart didn't want to target the Dart VM,
and I decided that I also shouldn't try messing with this.

# What's next?

For now, I have a parse that can parse the aforementioned grammar and a
transpiler that generates the exact code that was shown. Nothing more,
nothing less.

There are two approaches I could take:

1. Design the language from the ground up, build a parser for it, then a
   resolver, and then a transpiler;
2. Design a single feature/aspect, implement it in the parser, then the
   resolver, and then the transpiler. Repeat.

I'm taking the 2nd approach, for two reasons:

1. By doing so, it's easier to test and get feedback from anyone (if someone)
   using it;
2. I want to tackle what I think will be the hardest part of this task, at least
   in the initial versions: interoperability.

Currently, as I have the parser, the compiler does not check for semantic
issues. For instance:

{{< highlight io >}}
type Option(T) = Some(U value) + None
{{< /highlight >}}


This code is invalid, because the declared type parameter `T` was not used,
and `U`, which does not exist in this context, is used instead. This code will
generate a Dart program that is syntatically correct, but will fail to compile.

This problem is not too hard when you think in the language isolated, but as my
language is supposed to interoperate with Dart, I have to consider which
identifiers were defined or not.

{{< highlight io >}}
type Id = Id(int idNumber)
{{< /highlight >}}

This should work, even if we didn't define `int` before, as `dart:core` should
be available in the default environment, just like in a Dart program.

This task also requires the ability to import other Dart or pint° files.

By doing so, I will have a minimally usable language that may be used alongside
Dart, and then I can work on adding more relevant features until it's good
enough to replace Dart completely if one decides to.
