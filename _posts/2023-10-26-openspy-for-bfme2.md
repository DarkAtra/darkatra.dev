---
title: "OpenSpy Support for Battle for Middle-Earth 2"
date: 2023-10-26
---

# Motivation

Some of you may be familiar
with [The Lord of the Rings, The Battle for Middle Earth II](https://en.wikipedia.org/wiki/The_Lord_of_the_Rings:_The_Battle_for_Middle-earth_II).
It is one of many strategy games released in 2006, yet it holds a special place in my heart.
Maybe because it is set in the world of JRR Tolkien or perhaps just because it reminds me of my childhood.
Either way, it's one of few games that I can motivate myself to write software for.

# The 3rd Age Online

If you've ever tried to play the game in multiplayer, you've probably noticed that EA has shut down the online servers.
The community has found many ways to continue playing online, for example via Gameranger, but only [the 3rd age online](https://t3aonline.net) comes close to
feeling like a true multiplayer experience. They achieved this by building their own GameSpy servers and modifying the game to use these instead of the original
ones. As a software developer, I have always wondered how exactly stuff like this works.
To my surprise, the source code was not available. Neither for the t3a online servers, nor for the client side modifications to the game.
The reasoning seemed to be simple: they didn't want the community to fragment any further. The risk of introducing additional servers that could compete with
t3a:online seemed quite high. Obviously this was quite disappointing for me as I could not satisfy my thirst for knowledge.

# OpenSpy to the rescue

A few months ago I stumbled upon [an open source project](https://github.com/chc/openspy-core-v2) that aims to restore the online functionality for GameSpy
based games. Unfortunately, The Battle for Middle Earth II was not yet supported.
However, it greatly improved the chances of ever having an open source server for The Battle for Middle Earth II.
This is a big deal, as t3a:online could easily die if the maintainers lose interest.

# The client side

TBD
