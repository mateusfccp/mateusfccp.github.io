+++
title = 'Getting Started'
date = 2024-10-07T17:00:00-03:00
slug = 'getting-started'
description = 'Learn how to set up pint° on your local machine.'
summary = 'Learn how to set up pint° on your local machine.'
+++

## Setting up

### Requirements

pint° is made in Dart and compile to Dart. You need the latest stable version of
Dart installed on your machine.

{{<button href="https://dart.dev/get-dart" target="_blank">}}
Get the Dart SDK
{{</button>}}

### Installation

Install the `pinto` executable with `pub`.

```sh
dart pub global activate pinto
```

## Compiling with the `pinto` executable

With the `pinto` executable you can compile a pint° file.

```sh
pinto your_file.pinto
```

The compiled Dart file will me printed in you stdout. This means that, if you
want to redirect it to a file, you may use `>`, like the following example.

```sh
pinto your_file.pinto > your_file.dart
```

## Editors support

pint° provides a basic implementation of a LSP that emits static
errors. It can be started by using the `pinto --server` command.

Currently, however, the only editor with official support is VSCode. There's a
official pint° extension for VS Code which consumes the LSP server and provides
syntax highlighting.

{{<button href="https://marketplace.visualstudio.com/items?itemName=mateusfccp.pinto">}}
Download the VSCode extension
{{</button>}}
