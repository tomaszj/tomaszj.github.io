---
layout: post
title:  "ack or just better grep"
date:   2016-02-13
categories: command-line utilities
---

TL;DR: Use `ack 'search-phrase'` to quickly locate interesting bits in your source code.

Finding a certain text or phrase across a project can be done in multiple ways. One is to use search/replace facility in editor or your favourite IDE. Sooner or later, you will stumble upon the old, mighty `grep`:

    $ grep -r 'search-phrase' .

UNIX coreutils tools contain lots of useful utilities, and `grep` is certainly one of them. Slight change in `grep` parameters, makes it even more useful:

    $ grep -inr 'search-phrase' .

This time round, `grep` will do case-insensitive search and will also print the line numbers of the match. Finally, `grep` can provide more lines around the match:

    $ grep -inr -C 3 'search-phrase' .

And all is great, until your repository grows bigger and bigger, and someone checks in a few binary files. Before you realise, `grep` searches take a dozen-ish seconds to complete. Is there a clever way out of this madness, without giving million parameters to `grep`?

Yes, there's an alternative and it's ack! (install that on Mac with `brew install ack`). Just do:

    $ ack 'search-phrase'

`ack` returns results in nicely-formatted way. But it's biggest benefit isn't a cool way to display results. It's its sensible defaults - `ack` is designed to search through source code, so it ignores .git hidden folders, binary files and many more! Result is a snappy search, just like you would expect.

`ack` uses flags you already know from `grep`:

    $ ack -i -C 3 'search-phrase'

This will do exactly what you expect.

Enjoy!

