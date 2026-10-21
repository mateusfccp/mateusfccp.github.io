+++
title = "Your app is offline last, and that's (probably) fine"
date = 2026-10-20T21:10:00-03:00
tags = ['Architecture', 'Software Engineering', 'Rant']
+++

This is something that I noticed a while ago and has since bothered me. When I
started to work with Flutter in 2019, a term caught my attention: *offline
first*. This is a movement that began at least as early as 2013, and that more
recently, was refined by Kleppmann et al.[^1] under the name _local-first
software_.[^2] They define it as software in which "we treat the copy of the
data on your local device — your laptop, tablet, or phone — as the primary copy"
(p. 155), contrasting it with cloud apps, which have the server data as the
primary source.

[^1]: Kleppmann, Martin; Wiggins, Adam; van Hardenberg, Peter; McGranaghan, Mark
(October 2019). **Local-first software:**  You own your data, in spite of the
cloud. 2019 ACM SIGPLAN International Symposium on New Ideas, New Paradigms, and
Reflections on Programming and Software (Onward!). Athens, Greece: ACM.
pp. 154–178.

[^2]: Kleppmann says that the reason why offline first software often fails is
"because tools and libraries designed for a server-centric model do not easily
adapt to situations in which users make edits while offline". They propose
local-first software as a proper replacement that achieves the original
intention of the offline first movement.

It is basically a middle ground between offline software, which was the standard
a bit more than a decade ago, and the now so popular cloud apps, in which all
the data and functionality depend on a server, and the client is probably a thin
client that only shows this data and sends requests to the server to change it.

While the latter has many advantages important to the way our world works
today,[^3] the former still has its value: it allows one to have "full agency
and ownership" (Ibid.) of the data that software produces and uses.

[^3]: In a world where everything is connected and changes quickly, cloud apps
allow us to have easy cross-device usage of the apps and real-time collaboration
features.

The value of _offline first_ (also known as _local first_) applications is that
we can (at least theoretically) have the best of both worlds. It accomplishes
that by implementing the main functionality completely independent of any
connection with the internet, but still provides a server that allows you to
back up the data or collaborate with other people. A notable example of software
that implements this concept properly is [Obsidian](https://obsidian.md).

## Offline last applications

Even though this idea is perfectly valid and useful, what I noticed is that it
became a buzzword, and, as such, it was emptied of most of its meaning. I've
known many people who claimed their apps were _offline first_. However, when you
try them, what you get is:

- The main features of the application don't work, or work only partially;
- You see obsolete data;
- You see loading indicators that run for an indeterminate time or even
  intentional network error messages or notifications.

Surely, the app still opens. Maybe it even "ignores" your login (because it
can't authenticate your account without an active connection)[^4] to allow you
to access it. You can read through your data, as it is cached locally. However,
the main functionality of your application is still blocked (or partially
blocked).

[^4]: A telling sign that an application isn't truly offline-first is blocking
remote authentication on startup. If an app prevents you from inspecting or
interacting with your existing local data simply because it can't reach an
identity server to validate a session token, the network is still treated as a
strict runtime requirement. In a true local-first architecture, authentication
either unlocks a local encrypted store or is deferred until the app actually
needs to contact the sync server.

This approach is not necessarily wrong, but **it is not _offline first_**. In
software engineering this approach may have different names, depending on the
context. You can call it simply **fault-tolerance**, in this case, more
specifically, **network fault-tolerance**. Or you may call it **graceful
degradation**. In a lower level, you can say you implemented a **cache-aside
pattern**, or a **stale-while-revalidate pattern**. The point is: call it what
it is.

For the cases where someone incorrectly calls something _offline first_ I like
to call it, ironically, _offline last_. It clearly conveys the idea: the app's
main features require connection, but, as a last resource when connection is not
available, it tries to do its best (or worst) to not break down completely.

## Why does this happen?

The probable reason for why this happens (and don't quote me on that, this is a
personal opinion) is simply because "offline first" became a buzzword. It sounds
nice. It is simple. It is popular. It's something that your product manager
heard somewhere and asked you to implement it without any idea of what that
means, and you proceeded to do the minimal effort to please your PM because it's
easier than correcting them with an "actually ☝️🤓".

But, from a software engineering perspective, this is completely wrong. And what
bothers me is not what your PM thinks: it is how other programmers, software
developers or engineers keep propagating this white lie and using the term
incorrectly.

Maybe I'm being picky (well, I **am** picky when it comes to terminology), but
this is something that bothers me a lot. Your app probably does not need to be
_offline first_[^5]. Most apps **CAN'T** be: they depend on intrinsic rules of
the domain that have to be validated by a proprietary server. Maybe your app
just needs to be "offline last". That's totally fine. Just don't try to deceive
yourself and others about what you are doing.

[^5]: Building real _offline first_ software is hard as hell. _Offline last_
apps don't require you to think about conflict resolution, synchronization
side-effects, state rejection and other problems that arise naturally when
designing proper _offline first_ software. And, for many cases, having to think
and implement all this will be more expensive than not supporting this use case
at all. The added complexity is often greater than the benefits.
