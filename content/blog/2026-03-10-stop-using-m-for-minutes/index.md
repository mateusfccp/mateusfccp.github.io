+++
title = 'Stop using “m” for minutes'
date = 2026-03-10T09:00:00-03:00
tags = ['Rant']
+++

I've noticed that people don't give a darn about ambiguity and consistency. This
is very evident when we look at how software in general deals with time unit
symbols. It's very well-known that in the
[International System of Units (SI)](https://en.wikipedia.org/wiki/International_System_of_Units),
we have non-ambiguous symbols for every unit. And the unit for “meter” is — who
would’ve guessed — “m”.

That's why, if I ever see “1 m”, I will immediately read it as “1 meter”. So how
would one read this: "1 m, 10 s"? I read it as "1 meter, 10 seconds", which is
EXACTLY what it means.

Obviously, I'm not a retard. I know what one means when they write "1 m, 10 s".
They are talking about time, so they _must_ mean "1 minute, 10 seconds",
except that this is not what they are saying. And, well, our brains work faster
when they can associate common patterns, so I can't avoid first reading it
as meters, and then, after a second, fixing it to the intended meaning.

This could be solved with a very simple fix: use the freaking correct unit
abbreviation. In the SI, the abbreviation for "minute(s)" is "min"[^1], and you
should just use it.

<table>
  <caption>Non-SI units accepted for use with the SI units.</caption>
  <thead>
    <tr>
      <th>Name of unit</th>
      <th>Symbol for unit</th>
      <th>Value in SI units</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>minute</td>
      <td>min</td>
      <td>1 min = 60 s</td>
    </tr>
    <tr>
      <td>hour</td>
      <td>h</td>
      <td>1 h = 60 min = 3600 s</td>
    </tr>
    <tr>
      <td>day</td>
      <td>d</td>
      <td>1 d = 24 h = 86 400 s</td>
    </tr>
  </tbody>
</table>

Will anyone enforce it? No. Will we ever get to a state where this consistency
is generally achieved in the world? No. But as someone reasonable, I argue that
we **should** strive for unambiguity and consistency. And, currently, using the
SI is probably the best standard we have at our disposal.

## Some examples

I have compiled some examples that I found in the wild of people doing this
abnormality. It is really not hard to do so.

### Claude Code

I use Claude Code a lot, and seeing this was what actually reminded me about
the desperate state we are at.

{{<figure
	src="example_claude.webp"
	title="Claude Code sautéed for 6 meters and 56 seconds."
	alt="Claude Code sautéed for 6 meters and 56 seconds."
	caption="Claude Code sautéed for 6 meters and 56 seconds.">}}

### The `ms` package

There's [this](https://github.com/vercel/ms) JS package that I never heard of,
and I found it when doing the research for this post. It has more than
300,000,000 downloads per week, so it looks like it is very popular.

It is a library for converting time formats to milliseconds. Surprisingly, it
is also able to convert METERS to milliseconds, considering 1 meter as equal to
60,000 milliseconds.

One confused user opened an [issue](https://github.com/vercel/ms/issues/134)
requiring this nonsensensicality to be fixed, but he was dismissed with the
argument that they should be "extra cautious with adding new features that
would increase the package size"[^2] (yes, this is literally what they said).

### Factorio

The same happened with the game Factorio[^3].

{{<figure
	src="example_factorio.webp"
	title="This factory produces 1.8k sheets of metal per meter."
	alt="This factory produces 1.8k sheets of metal per meter."
	caption="This factory produces 1.8k sheets of metal per meter.">}}

When a good citizen of Earth questioned why their factory produced a certain
amount of resources per meter, he was answered with
[this](https://forums.factorio.com/viewtopic.php?p=485825#p485825) amazing result of pure
neuron absence.

{{<figure
	src="example_factorio_2.webp"
	title="Thanks for the report, however we aren't changing this. \"m\" is minutes."
	alt="Thanks for the report, however we aren't changing this. \"m\" is minutes."
	caption="Thanks for the report, however we aren't changing this. \"m\" is minutes.">}}

No, my dear friend. You are objectively wrong. "m" is not minutes.


[^1]: One could argue that a minute is a non-SI unit, which is true. However,
the [SI Brochure](https://www.bipm.org/en/publications/si-brochure), which is
the canonical source for this matter, specifies the non-SI units to be used with
the SI units. For obvious reasons, one won't say that they are about to turn 945
Ms instead of 30 years (thinking now, we could even split the human life into
three sections of 1 Gs for youth, mid-life, and senior years). Thus, the more
common minute, hour, and day units are accepted and specified in the SI to be
worked with "pure" SI units.

[^2]: https://github.com/vercel/ms/issues/134#issuecomment-1180684792

[^3]: This is a very old forum thread, so maybe they changed their minds and
fixed this bug, but it nonetheless reveals the idiocracy that is the status quo
of humanity.
