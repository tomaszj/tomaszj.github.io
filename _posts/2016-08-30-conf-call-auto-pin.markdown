---
layout: post
title:  "Calling into a phone conference quickly"
date:   2016-08-30
categories: phone protip
---

This is probably one of my shorter posts, but just a bit of trivial research goes a long way.

#### Special digits on your phone

Turns out, that apart from usual digits on your phone's keypad (0-9), there are some additional special keys. These are ```,``` and ```;``` (pause and wait, respectively).

Pause simply lets your phone wait a bit before keying in the rest of the sequence. You can put multiple pauses one after another.

Wait, on the other hand, lets the phone wait with keying in the sequence until an event. To be honest, I have yet to see this working as I'd expect.

#### Scenario

Say you're calling in as a chairperson to a phone conf-call and this requires you to dial the conf-call number ```0800 333 987```, then press the conference ID ending with a hash ```654321#``` and then press the password ending with a hash as well ```789456#```.

To go through this process automatically, you would simply call the following number:

    0800333987,654321#,789456#

This should get you into the call in no time! Just save the number as your contact.

#### Caveats

From time to time, a system might require a bit more pause before receiving the next sequence of digits. In such case, just add more commas where needed:

    0800333987,,,654321#,,789456#

