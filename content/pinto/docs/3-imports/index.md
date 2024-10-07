+++
title = 'Imports'
date = 2024-10-05
slug = 'imports'
description = 'Importing external packages to a pint° program.'
summary = 'Importing external packages to a pint° program.'
+++

The `import` keywords allows one to import an external package to a pint°
program. There are, currently, two types of imports in pinto°, regular imports
and Dart imports[^1].


Currently, there's no support for relative imports, neither modifiers (`as`,
`show`, `hide`, `if`).

[^1]: pint° can't import other pint° files. You have to compile them to Dart
first. This will change in the future.

## Regular imports

Regular imports depends on the packages available in your `package_config.json`.
You can either import the main package[^2] file or specific files exported from the
package by specifying (or not) what you want to import.

{{< highlight js "linenos=table" >}}
import flutter_bloc // Imports `package:flutter_bloc/flutter_bloc.dart`
import flutter/widgets // Imports `package:flutter/widgets.dart`
{{</ highlight >}}

## Dart imports

By prefixing a `@` before the identifier, you can import Dart's SDK packages.

{{< highlight js "linenos=table" >}}
import @async // Imports `dart:async`
{{</ highlight >}}

[^2]: The main package file is the one that has the same name as the package
itself. For instance, for a package named `foo`, it would be the same as
`foo/foo`.
