---
layout: post
date: 2026-07-02 00:01:00
title: "Building a network-native ham station (part 1): the hamtools story"
tags:
    - ham-radio
    - cw
    - logging
    - linux
    - cpp
excerpt: "How a pile of annoyances — flaky contest tooling, an unmaintained logger, wanting to operate from the couch — grew into hamtools: cwsd, xlog2 and usb-paddles, a small suite that runs a whole ham station over the network. Part 1: the story."
---

**hamtools** is a small suite of Linux ham-radio tools I wrote to run my station over the network — **cwsd** (a rig daemon), **xlog2** (a logging program) and **usb-paddles** (Morse-paddle firmware). Each is useful on its own; wired together they turn one radio into a fully network- and internet-operable station. This post is the story of how they came to be; a follow-up — [part 2: components and setup]({% post_url 2026-07-03-hamtools-components-and-setup %}) — tours each piece and how to run it.

## A bit of history

Everything started when Matei, YO3GEK, tried to get me into CW contesting. Being a Linux user, I tried the ubiquitous [tlf](https://tlf.github.io/) + [cwdaemon](https://github.com/acerion/cwdaemon/) + [rigctld](https://hamlib.sourceforge.net/html/rigctld.1.html) mix, but it quickly became annoying to have so many moving parts that randomly crashed. Long story short, that was the birth of **cwsd**. In its first incarnation it was just a simple binary interfacing the rig with hamlib and exposing a cwdaemon and a rigctld facade.

Matei and I tested it throughout contests, pairing it up with tlf for net keying and rig control. This brought to light bugs that were ironed out and further ideas that I slowly implemented.

And for a while, that was good. Right until I got annoyed by logging.

I was using [xlog](https://www.nongnu.org/xlog/), which is sadly no longer maintained and is stuck on GTK2 — which looked awful on a modern distro. After a short attempt at porting it to GTK3, I had an idea: what if, instead of wasting effort porting xlog to GTK3, I just wrote my own logger that could import xlog data? I would then be in control, able to quickly fix bugs or add any feature my heart desired. And thus **xlog2** was born (quite an original name, I know).

Being a GNOME user, I was inclined to use GTK4 for a modern, slick UI, but after a small flamewar with Matei I decided to also go for a Qt frontend. Being the decent software engineer that I am, I went for an MVP approach, building a UI-toolkit-agnostic core library and two separate frontends on top of it — an architectural decision that paid off many times over in the long run. Claude Code graciously helped with all the dirty, boring boilerplate.

After I had a first version running on both frontends that could import my QSO history from xlog, I decided I would use **xlog2** exclusively from then on. That way I would feel firsthand what frustrated me most and fix or implement the missing features — the proverbial eating your own dog food.

The IC-7300 sat on my work desk, connected to the desktop PC I was developing on, until I decided to keep the rig always on and connect it over USB to a mini server running **cwsd**. That let me work on the logger from the laptop, in various spots around the house. This posed another problem: I wanted to add band switching and frequency control to the logger, but it was cumbersome to develop in the living room and then run upstairs to the station to check that the frequency/band switching worked as intended.

So I decided to add audio streaming to **xlog2** — which of course required building support in **cwsd** too. Once that was working, I could operate from the couch using macros defined in the logger. That wasn't entirely to my taste, though; I wanted true remote keying — being able to use an actual paddle to key the rig remotely. More than that, it had to feel seamless: no perceived lag whatsoever!

After a couple of failed ideas and iterations, I ended up writing a small microcontroller firmware acting as a **USB paddle interface**. The idea was simple: you plug the paddles into the module, connect that to the PC running **xlog2**, and you can work as if it were plugged in at the station. The implementation, however, was more challenging than I'd assumed — but it all worked out in the end (more on that in the next post).

So now I had a logger that could control my rig, query rig data, listen to its audio, and key it as if I were there. A couple of other features followed: a map showing the location of the DX, LoTW and qrz.com integration, a Morse decoder à la CW Skimmer, and a waterfall that was nice to look at.

The next thing I got annoyed by was switching from laptop to desktop: the logger ran just fine on both at the same time, but the logbook wasn't synchronized, and I did not want to deal with that manually. It took a couple of attempts, but I ended up with something I enjoy a lot: a distributed, decentralized, self-discovering mesh — log a contact on any **xlog2** instance and it turns up on all the others. (More on how it holds together in the next post.)

So, with things close to perfect for my needs, a question started bugging me: what if I used the desktop and the laptop alternately, and **xlog2** was never online on both at the same time to sync the logbook? And that's how **xlog2-syncd** was born — an exceptionally low-hanging fruit, thanks to the architectural decisions taken at the start. **xlog2-syncd** is a service running on the same server as **cwsd**, always up and quietly making sure all QSOs and qrz.com queries are cached and served to any mesh member that needs them.

I could finally take a breath and enjoy the fruits of my labour. But wait — what about field logging? I have an Elecraft KX3 and a QRP Labs QMX+, and I sometimes like to operate from remote places. I used [VLS](https://www.qrz.com/db/SP7VLS) before — an exquisite logging app — but I had to jump through many steps to get the field log into xlog. But now that I have a logger …

And that's where the very same architectural decisions paid off. With the help of Claude, I put together an Android frontend in Kotlin on top of the same **xlog2** core library. Soon I had logging synchronized across all my devices, which was very satisfying. But wait — why not attempt something crazy, like remote operation from the phone itself? Would that even work? Only one way to find out: first **cwsd**'s audio streaming was integrated into the Android app, followed by the remote keyer. It was amazing to plug the keyer interface into my phone and actually have a QSO using the **xlog2** frontend running on the phone!

At this point things felt like they were in a state where I could share the whole thing with fellow hams, who might make use of one component or the whole stack. It is all free, and always will be.

That's the story. The [next post]({% post_url 2026-07-03-hamtools-components-and-setup %}) is the practical half — each component in turn (what it does, how to install and configure it), plus how the internet CW keying and the logbook sync mesh actually work.

73 · YO6SSW
