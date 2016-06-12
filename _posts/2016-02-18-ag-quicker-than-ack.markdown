---
layout: post
title:  "ag - quicker ack"
date:   2016-02-18
categories: command-line utilities
---

TL;DR: Use `ag 'search-phrase'` to even more quickly locate interesting bits in your source code.

In the [previous post](/command-line/utilities/2016/02/13/ack-or-better-grep.html) I've explored how `ack` can help you to quickly and neatly find certain text in files. Now it's time to speed things up! Piotr (fellow dev @ Kainos) has pointed me to ag soon after my recent post and it quite impressed me with further speed improvement it makes in usual searches.

#### ag usage

As you would expect, `ag`'s syntax is identical to its siblings (btw: if you haven't installed it yet, do that on Mac with `brew install ag`). Just do:

    $ ag 'search-phrase'

![ag 1st example](/images/ag/ag1.png)

`ag` returns results in same, nicely-formatted way. If you looked closely, you've discovered how ag differs from ack - it inspects .gitignores (and other .*ignores) to speed up the search. Hence the difference to what `ack` has previously returned.

Now, some useful flags for `ag`. `-C` provides context around the search:

    $ ag -C 3 'search-phrase'

![ag 2nd example](/images/ag/ag2.png)

`-l` returns names of the matched files, ideal for further processing with, i.e. `xargs`:

    $ ag -l 'grep -r'

And my favourite to finish `--{FILE TYPE}` lists files specific to a language. E.g. `--ruby` or `--html`:

    $ ag 'grep -r' --html

![ag 3rd example](/images/ag/ag3.png)

Enjoy!

