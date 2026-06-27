---
title: "Sage INI File Language Support for IntelliJ IDEA"
date: 2026-06-27
seo_title: Sage INI File Language Support for IntelliJ IDEA
seo_description: A writeup on how i built a plugin for IntelliJ IDEA that supports syntax highlighting, formatting and code navigation for Sage Engine INI files.
---

It’s been a while since I last posted here, and looking back, I’m surprised by how quickly the time has passed. Since then, I’ve been working on a few things
BfME related, but my most recent project felt worth writing about: so here we are. This blog post is about a plugin
for [IntelliJ IDEA](https://www.jetbrains.com/idea/) that adds syntax highlighting, code formatting, completion, and navigation features for Sage Engine INI
files.

The plugin is called [Sage Engine INI](https://github.com/DarkAtra/bfme2-idea-plugin), and while I mostly built it with
[The Lord of the Rings: The Battle for Middle-earth II](https://en.wikipedia.org/wiki/The_Lord_of_the_Rings:_The_Battle_for_Middle-earth_II) in mind, the
format is used by other Sage Engine games as well.

# But... Why?

First of all, I have to admit that I’m very comfortable in IntelliJ IDEA. It has been my main IDE for years, and I’ve spent a lot of time refining my
keybindings and making the whole setup feel like my own. What kept bothering me, though, was the lack of proper syntax highlighting, navigation actions, and -
most importantly - a formatter for Sage INI files. So, a few weeks ago, I decided it was time to finally do something about it.

# The features

The project is still in its early stages, and the code is far from perfect, but it already has a few features that make working with Sage INI files noticeably
easier.

## Syntax Highlighting

The plugin registers Sage Engine INI files as their own file type and provides syntax highlighting for the things I care about most.

[![A screenshot of syntax highlighting](/assets/bfme2-ini-syntax-highlighting.png)](/assets/bfme2-ini-syntax-highlighting.png)

## Formatting

This is probably my favorite feature. There’s something incredibly satisfying about pressing a single button and having the entire file formatted consistently.
It might not look like much at first glance, but the formatter already takes care of a bunch of small things that make files feel cleaner and more consistent.
For example, it:

* indents blocks
* aligns properties
* removes excessive whitespace almost everywhere, including both code and comments
* removes "empty" comments at the end of lines, such as `//`, `--`, or `;`
* replaces tabs with spaces, although there might be a configuration option for all the tab enjoyers in the future

Here’s a quick comparison at what the formatter changes in `aragorn.ini`:

<div style="display: flex; gap: 1rem;">
  <div>
    <h5 style="margin-top: 0; margin-bottom: .5rem;">Before</h5>
    <a href="/assets/bfme2-aragorn-before-formatting.png" target="_blank">
      <img alt="A screenshot of Aragorn code before formatting" src="/assets/bfme2-aragorn-before-formatting.png"/>
    </a>
  </div>
  <div>
    <h5 style="margin-top: 0; margin-bottom: .5rem;">After</h5>
    <a href="/assets/bfme2-aragorn-after-formatting.png" target="_blank">
      <img alt="A screenshot of Aragorn code after formatting" src="/assets/bfme2-aragorn-after-formatting.png"/>
    </a>
  </div>
</div>

## Include Navigation and Completion

Another small quality-of-life improvement is support for `#include` macros. If an INI file contains something like:

```ini
#include "data/ini/object/goodfaction/units/men/aragorn.inc"
```

the plugin can resolve the referenced file, jump to it via the usual "go to declaration" action, and offer path completion based on the directory of the
current file. This is one of those features that does not sound particularly exciting until you have to jump between included files over and over again.

# Use of AI

I should probably also mention that this project was intentionally built with a lot of AI assistance. Part of the goal was to better understand where AI works
well in this kind of project and where its limits become visible. Instead of trying to fully understand every IntelliJ Platform API up front, I used AI to
draft implementation plans, explore unfamiliar extension points, create tests, and refactor some of the repetitive code.

This also means that I treated the resulting code somewhat pragmatically: I did not review most of it line by line, but focused on whether it fulfilled the
intended behavior through automated tests. In other words, the review process was more about verifying the intended behavior rather than manually inspecting
every single line of code.

# What’s Next?

There are still plenty of things I would like to improve. Some ideas that come to mind are more inspections, smarter completion for known values, and eventually
maybe deeper understanding of the game data itself. I also want to keep improving the parser as I run into more real-world INI files with edge cases.

For now, though, I’m already happy with the result. Editing Sage Engine INI files in IntelliJ IDEA feels much nicer than before, and that was exactly the goal.

The source code is available on GitHub here: [https://github.com/DarkAtra/bfme2-idea-plugin](https://github.com/DarkAtra/bfme2-idea-plugin)
