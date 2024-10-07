+++
title = 'The pint° programming language'

sharingLinks = false
showAuthor = false
showDate = false
showComments = false
showHeadingAnchors = false
showPagination = false
showReadingTime = false
showTableOfContents = false
showWordCount = false
+++

{{< img
    src="images/logo.png"
	width="300px"
	height="300px"
	alt="pint° logo" >}}

The pint° (/pĩntʊ/) programming language.

[{{< img style="display: inline" src="https://img.shields.io/github/license/mateusfccp/pinto" >}}](https://github.com/mateusfccp/pinto/blob/master/LICENSE)
[{{< img style="display: inline" src="https://img.shields.io/pub/v/pinto" >}}](https://pub.dev/packages/pinto)
[{{< img style="display: inline" src="https://img.shields.io/github/stars/mateusfccp/pinto" >}}](https://github.com/mateusfccp/pinto)

{{< alert >}}

The pint° programming language is still in very early development stage, and is
not recommended for use in production software. Use at your own risk.

{{< /alert >}}

The pint° programming language is a language that compiles to Dart.

It has the following objectives:

- Have seamless interoperability with Dart;
- Consider Flutter as first-class;
- Generate reasonably readable and efficient Dart code;
- Be terser and more expressive than Dart;
- Provide a powerful macro system with great ergonomy.

{{< highlight haskell "linenos=table" >}}
import @async

let name = "pint°"
let isTheBestLanguage = true

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

let main _ =
  print "Hello, world!"  

{{</ highlight >}}

You can get started with the language by reading
[the documentation]({{< ref "docs" >}}).

---

## Community

We now have a small community in Discord. You can join our Discord server to
learn more and contribute by clicking button below!

{{< button href="https://discord.gg/Y9cWqCUCCG" target="_blank" >}}
{{< icon "discord" >}}
Join our Discord server!
{{< /button >}}

<p>
<iframe src="https://discord.com/widget?id=1286023882515677236&theme=dark"
        width="350"
		height="500"
		allowtransparency="true"
		frameborder="0"
		sandbox="allow-popups allow-popups-to-escape-sandbox allow-same-origin allow-scripts">
</iframe>
</p>

---

# Latest news
