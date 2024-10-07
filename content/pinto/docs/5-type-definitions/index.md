+++
title = 'Type definitions'
date = 2024-10-07T19:00:00-03:00
slug = 'type-definitions'
description = 'Defining product types and sum types.'
summary = 'Defining product types and sum types.'
+++

pint° still have no types with complex behavior, but you can introduce product
types and sum types with the `type` keyword. The types defined by the `type`
keyword are opaque and have structural identity.

All types are made of a type name with optional type parameters and one or more
type variants.

## Product types

Product types are described by providing parameters delimited by commas.

{{< highlight io "linenos=table" >}}
type User = User(
  int id,
  String name,
  int age,
)
{{< /highlight >}}

## Sum types

You can declare multiple variants of a type by separating each variant with the
pseudo-operator `+`.

{{< highlight io >}}
type LogLevel = Debug + Info + Warn + Error + Fatal
{{< /highlight >}}

## Combining both types

You can combine both product types and sum types to make more complex types.

{{< highlight io "linenos=table" >}}
import @async

type Complex(T) = Complex(
  [⊤] listOfAny,
  [T] listOfT,
  {T} set,
  {T: T} map,
  T? maybeT,
  Future(T) futureOfT,
  int aSimpleInt,
  {{T?} : [Future(T)]} aMonster
)
{{</ highlight >}}
