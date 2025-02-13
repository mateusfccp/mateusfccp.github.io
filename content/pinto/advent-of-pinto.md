+++
title = 'Advent of pint°'
date = 2025-02-13T19:00:00-03:00
tags = ['Language', 'Flutter', 'Dart', 'pint°', 'Announcement']
+++

For the last months of the last year I've been focused on finishing my graduate
studies, so I couldn't work much on pint°. Unfortunately, in the first week of
this year my Macbook died and I had to wait about 1 month to get it fixed in the
authorized support provider. I'm finally back.

For the year of 2025 pint° has a kind of [sic] subjective objecive. That is:  by
december, when we get to [Advent of Code](https://adventofcode.com), I want pint°
to be a viable alternative to participate. If everything goes well, we will have
a pint° leaderboard so we can compete between ourselves!

This is a general goal, but we can't have a general goal and neglect the
details. This is why I want to share the general roadmap for pint° in 2025.
Considering that I work on this mostly alone, I am being very generous with the
"deadlines", but if we get to make the community grow and more people to
contribute, we can achieve the roadmap early and be greedier later.

However, before sharing the roadmap itself, lets talk briefly about some minor
subjects.

## Making development easier

Although I couldn't release anything in the last months, I've been working,
slowly, on two things. The first of them is improving the internal tooling used
to develop pint°. We had some scripts to generate code that helped us generating
the AST and semantic tree classes. Although it worked reasonably, it required us
to have a custom tree data structure implementation and to describe the trees in
terms of these nodes. I can't say it didn't work, but the complexity was
increasing constantly each time we needed to support something slightly
different.

I wanted to tackle this issue with code generation. My obvious first choice was
to [experiment with the (then) upcoming Dart feature,
macros](https://github.com/mateusfccp/pinto/issues/14).
I quickly got blocked by the fact that the feature was too unstable and even if
I had a initial code, it crashed and didn't support anything I needed. I decided
to wait more to see where it would get. Unfortunately (or not), [the Dart team
decided to stop in their pursuit of
macros](https://medium.com/dartlang/an-update-on-dart-macros-data-serialization-06d3037d4f12).

Considering that they are now instead focusing on improving the performance and
tooling around `build` and `build_runner`, I decided to change my approach from
macros to this already existing tool. Today
[I merged a PR with the changes](https://github.com/mateusfccp/pinto/pull/19).
The PR has 1,221 added lines and 1,691 removed lines, which gives us a delta of
exactly 470 lines removed. This is mostly because we ditched the tree data
structure, as most of the code generation functions were kept and some new code
related to the `build` package was added. But more important than cleaning the
source was the reduced complexity. Now, creating a new node in the tree is as
simple as creating a class that extends another node in the tree.

Here is an example of how we implemented `ImportedSymbolSyntheticElement` before:

{{< highlight dart >}}
TreeNode(
  name: 'ImportedSymbolSyntheticElement',
  implements: ['TypedElement'],
  properties: [
    StringProperty('name'),
    Property('TypedElement', 'syntheticElement'),
  ],
  methods: [
    Method((builder) {
      builder.annotations.add(refer('override'));
      builder.returns = refer('Type');
      builder.type = MethodType.getter;
      builder.name = 'type';
      builder.lambda = true;
      builder.body = refer('syntheticElement').property('type').nullChecked.code;
    })
  ],
),
{{</ highlight >}}

And now:

{{< highlight dart >}}
final class ImportedSymbolSyntheticElement extends DeclarationElement with _ImportedSymbolSyntheticElement implements TypedElement {
  ImportedSymbolSyntheticElement({
    required this.name,
    required this.syntheticElement,
  });

  final String name;

  final TypedElement syntheticElement;

  @override
  Type get type => syntheticElement.type!;
}
{{</ highlight >}}

This is shorter, clearer, easier (because uses "standard" Dart as description)
and potentially safer, as we are dealing with concrete types instead of strings
that may generate incorrect code.

The additional methods, like `accept`, `visitChildren` and `toString` are now in
the generated part file. This is a very interesting improvement in my opinion,
and I want to improve it further once we have augmentations, which would reduce
our code to:

{{< highlight dart >}}
final class ImportedSymbolSyntheticElement extends DeclarationElement implements TypedElement {
  final String name;

  final TypedElement syntheticElement;

  @override
  Type get type => syntheticElement.type!;
}
{{</ highlight >}}

Not only this, but in the future we could even use code check that the
implementation follows the grammar and generate documentation on top of it. The
future is exciting!

## Structs

The second thing I've been working on is structs. I talked about them a little
in the [v0.0.4 release post]({{< ref "pinto-v0.0.4-hello-world" >}}), and I have
a branch (that now I have to rebase with the lastest `build_runner` changes)
with some work-in-progress.

Structs are akin to Dart records, but more flexible, and will enable us to
proceed to the next objective of pint°. I've been struggling with some ideas
related to syntax, but I decided to let it out for now and keep going. If we
later decided that it is not enough, we can always change and break everything.
This is the advantage of still being in v0.x, after all. 

## Macros

Dart abandoned macros, but pint° don't intend to. It is being designed from
ground up with it in mind. Even if we still don't have macros, and it is a
feature that requires a lot of thinking to get things right[^1], this is one
of the core goals of pint°. The approach intended, however, is different from
what Dart was pursuing, and is purely syntactic.

[^1]: If not even the Dart team with experienced language designers could get
it to a state they were satisfied with after years of research, who am I?
Fortunately, considering that pint° is in a much earlier state, that we can
break at any time and that we are designing it in a way that macros will be
released with the first stable version, we have a lot of advantage, but we still
have to be careful to not make a mess.

## Community and contribution

We intend to grow up our community more and attract contributors to pint° this
year. We intend to do it by sharing more about the project in social media,
something that I've been avoiding so far. I intend to make it more public after
v0.5, and I hope that by sharing it we will have more people contributing.

Aside from this, I am thinking about organizing some kind of hackathon focused
on specific objectives of the language with some kind of (low-cost, remember,
this project is not monetized) prize to incentivize. If we do it, it will be
probably in Q3 or Q4, though.

## The (tentative) roadmap for 2025

* January -- March: v0.0.5
  * [Implement subtyping](https://github.com/mateusfccp/pinto/issues/17)
  * [Implement parameters to functions](https://github.com/mateusfccp/pinto/issues/16)
  * [Implement structs](https://github.com/mateusfccp/pinto/issues/15)
* April -- June: v0.0.6
  * Implement basic language functionality
	* Control flow, conditions
	* Decide on how to deal with static typing for functions
	* Decide about more complex literals
* July -- August: v0.0.7
  * Decide on pint° projects structure
  * Improvements on the CLI (like [#3](https://github.com/mateusfccp/pinto/issues/3))
  * [Allow pint° code to import pint° files](https://github.com/mateusfccp/pinto/issues/4)
  * General tooling and language consistency improvements (like [#8](https://github.com/mateusfccp/pinto/issues/8))
* September -- December: v0.0.8
  * Test how Dart stdlib works within pint°
  * Focus on improving interoperability so Dart libraries can be used in Advent of Code

This is only the more technical part of the plans I have por pint°, and it
becomes less precise the further it is from now, but it is good to at least
have something in mind.

## Conclusion

The year is only starting and I hope this year will be one of progress to pint°.
I want to invite anyone interested to contribute to the project and learn
together with me.
