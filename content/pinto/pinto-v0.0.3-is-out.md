+++
title = 'pint° v0.0.3 is out with a basic LSP implementation'
date = 2024-08-18T21:30:00-03:00
tags = ['Language', 'Flutter', 'Dart', 'pint°', 'Announcement', 'Release']
+++

pint° v0.0.3 is a small release of the pint° language.

The repository can be seen [here](https://github.com/mateusfccp/pinto), and the
`pub.dev` package [here](https://pub.dev/packages/pinto).

## Changes on this release

I skipped v0.0.2 announcement, as it was a simple bug fix. You can see the
release in the GitHub repository.

For v0.0.3, the main difference is that now a basic LSP implementation is
available through the executable `pinto_server`. It is still pretty barebones,
but it allows you to use the new
[VSCode extension](https://marketplace.visualstudio.com/items?itemName=mateusfccp.pinto)
to have basic static analysis integrated in the editor.

![VSCode extension example](https://github.com/mateusfccp/pinto_vscode_extension/raw/HEAD/static/example.gif)

### Another small improvement

Aside from this, now, when you use an import that is not available in the
environment[^1] a resolving error will be emitted.

[^1]: pint° will look both in the Dart SDK installed in the machine and in the
packages declared in `pubspec_config.json`.

## New homepage for pint°

There's now a [new homepage](https://mateusfccp.me/pinto) for pint° with a
summary of the language. As the language grows, we'll probably need to do
something different, but for now it serves well.

Also, the language has now an official logo. It's a pinto horse, the bluish
colors reminiscing that of Dart and Flutter.

{{< img
    src="../images/logo.png"
	width="300px"
	height="300px"
	alt="pint° logo" >}}

## What's next?

As I implemented a basic LSP server and client, I think I'm now going to focus
on the next feature of the language, although I'm still not sure what will it
be.

Possibly, I'm going to implement something like top-level functions, which will
required me to implement many other things, like expressions, variables, and
basic blocks.

pint° is intended to be an expression-only language. That means that there's no
statements. I don't want to model pint° against IO Monads or another kind of
effects system for now, but I want the language to be very explicit about
side-effects and mutability.

I'm leaving a snippet of a tentative syntax of what a function implementation in
Flutter may be. Note that this probably won't be the final syntax, but it may
give an idea of where the language wants to go.

{{< highlight alloy "linenos=table" >}}
Widget build(ButtonState, BuildContext context) =
  let color = Color(0xAA22FF).withOpacity(0.5) // withOpacity is evaluated at compile time
  let style = TextStyle(:color) // `:color` stands for `:color color`
  
  GestureDetector(
    :onTap widget.onTap,
	:child Text(
	  :style,
	  :data @count, // The `@count` token inlines the first argument. It's equivalent to `state.count`
    )
  )
  
{{</ highlight >}}
