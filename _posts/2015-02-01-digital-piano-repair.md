---
title: 'Repairing a Roland Digital Piano'
author: admin
layout: post
description: "Diagnosing and repairing a Roland HP-2900G digital piano"
tags: [repair, piano, electronics]
category: Electronics 
comments: true
imagefeature: piano-cover.jpg
---


I've wanted a digital piano for some time now, and I still occasionally glance
over the 'spares and repairs' category on eBay in hopes of a cheap deal.
Finally, I found one:

{:.with-image}
[![picture of the keyboard][1]][2]

## The Problem

According to the previous owner, when she bought the piano, it was working
fine. Over time it developed a crackle and eventually stopped producing any
audio output whatsoever. It does however turn on and the digital stuff appears
fine, the screen and buttons were all working.

Opening up the keyboard, I found a problem pretty quickly. Leaky capacitors:

{:.with-image}
[![leaky capacitors][3]][4]

## Capacitors
Electrolytic capacitors are composed of a thin sheet of (usually) aluminium bathed in
electrolyte. At the factory a dielectric oxide layer is formed on the aluminium
by applying a voltage to it and thus creating a capacitor.

Over time the electrolyte in these capacitors dries out, usually causing the
capacitance to decrease and the impedance to increase. In some cases, gas build
up inside the capacitor and then bursts, leaking electrolyte onto the PCB,
   which is what has happened here.

The electrolyte is corrosive and as you can see from the photos below, can be
quite damaging to the copper traces around it. Nearly all of the capacitors on
the main board have leaked and corroded the copper around them, so they all had
to be replaced.

{:.with-image}
[![Corroded copper][5]][6]

{:.with-image}
[![More corrosion][7]][8]

The worst offenders were to be found in the audio output stage of the board,
    directly after the DACs and before the connector for the amplifier. There
    are two 5V regulators here with electrolytics around them, so I'd imagine
    the heat from the regulators accelerated the leaking process and caused the
    worst corrosion. 

## The repair

I started off removing the faulty capacitors with a soldering iron resulting in
a few lifted pads, and soon switched to a hot air station. The electrolyte
seems to 'stick' the caps to the board so this wasn't as easy as I had hoped
but in the end, I got them all off.

{:.with-image}
[![Capacitors desoldered][9]][10]

Reading around on electronics forums seems to show that deionised water is
the best option for cleaning off the old electrolyte so I brushed the board
down with water and alcohol. After removing the solder I ended up with a slightly
cleaner board.

{:.with-image}
[![Cleaned board][11]][12]

Unfortunately, the electrolyte had actually corroded away a number of traces
and vias completely, which is almost certainly what caused the original
problem. I fixed these traces with mod wires and solder and replaced the lifted
pads with solder braid where necessary. I had to remove the -5V regulator to
clean up the corrosion beneath it and in the process snapped off a leg. So that
has now been replaced with a TO-220 package.

In some places the electrolyte had migrated along the tracks underneath the
soldermask. In these cases I tried to scrape off as much soldermask as possible
and clean the copper underneath to prevent further corrosion.

{:.with-image}
[![Cleaned with tracesCleaned with traces and pads fixed and pads fixed][13]][14]

I ended up replacing the caps with a variety of caps, mainly because it's much
easier to solder them in (especially where broken traces and mods occurred) and
there weren't any particular space requirements.


{:.with-image}
[![new capacitors][15]][16]


## It Works!

Turning everything on, the keyboard works fairly well, there is still a slight
tone over the notes being played, this is barely noticeable unless the treble
is turned up but I will try to diagnose it eventually.

I have been thinking about designing a USB midi board to interface with the
keyboard matrix, allowing me to replace all the aging electronics with a single
board linux computer, but this is probably some time off.

[1]: {{site.url}}/images/keyboard/keyboard.jpg
[2]: {{site.url}}/images/keyboard/keyboard.jpg

[3]: {{site.url}}/images/keyboard/leaky.JPG
[4]: {{site.url}}/images/keyboard/leaky.JPG

[5]: {{site.url}}/images/keyboard/corrosion.JPG
[6]: {{site.url}}/images/keyboard/corrosion.JPG

[7]: {{site.url}}/images/keyboard/corrosion_close.JPG
[8]: {{site.url}}/images/keyboard/corrosion_close.JPG

[9]: {{site.url}}/images/keyboard/caps_removed.JPG
[10]: {{site.url}}/images/keyboard/caps_removed.JPG

[11]: {{site.url}}/images/keyboard/cleaned.JPG
[12]: {{site.url}}/images/keyboard/cleaned.JPG

[13]: {{site.url}}/images/keyboard/repairing_traces.JPG
[14]: {{site.url}}/images/keyboard/repairing_traces.JPG

[15]: {{site.url}}/images/keyboard/finished.JPG
[16]: {{site.url}}/images/keyboard/finished.JPG










