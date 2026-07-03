---
title: "Sage Engine INI File Language Support for IntelliJ IDEA"
date: 2026-06-27
seo_title: Sage Engine INI File Language Support for IntelliJ IDEA
seo_description: A write-up about how I built a plugin for IntelliJ IDEA that adds syntax highlighting, formatting, and code navigation for Sage Engine INI files.
---

It's been a while since I last posted here, and looking back, I'm surprised by how quickly the time has passed. Since then, I've been working on a few
BfME-related projects, but my most recent one felt worth writing about. So here we are: this post is about a plugin for
[IntelliJ IDEA](https://www.jetbrains.com/idea/) that adds syntax highlighting, code formatting, completion, and navigation features for Sage Engine INI files.

The plugin is called [Sage Engine INI](https://github.com/DarkAtra/bfme2-idea-plugin), and while I mostly built it with
[The Lord of the Rings: The Battle for Middle-earth II](https://en.wikipedia.org/wiki/The_Lord_of_the_Rings:_The_Battle_for_Middle-earth_II) in mind, it
probably works for other Sage Engine games as well.

## Motivation

IntelliJ IDEA has been my primary IDE for years, and I've spent countless hours refining my keybindings and customizing it so that the whole setup feels like my
own. Whenever I worked on BFME-related projects, though, I found myself switching to a plain text editor instead. Without proper language support for Sage
Engine INI files, most of IntelliJ's advanced features simply weren't useful, and the IDE felt unnecessarily heavy compared to lightweight editors like VS Code
or Notepad++. Yet those editors weren't the ideal solution either. What I really wanted was an IntelliJ plugin that brought first-class support for Sage Engine
INI files, complete with syntax highlighting, code navigation, and - most importantly - a formatter. A few weeks ago, I finally decided it was time to build
exactly that.

## Use of AI

Before looking at the features, it's worth mentioning that this project was intentionally built with the assistance of AI. One of the goals was to better
understand where AI is genuinely useful and where its limitations become apparent. Rather than attempting to fully understand IntelliJ's Platform API upfront, I
used AI to build the initial version of the plugin, explore unfamiliar extension points, generate tests, and refactor repetitive code.

This also influenced how I approached code quality. Instead of reviewing every change line by line, I took a more pragmatic approach and focused on whether the
implementation behaved correctly, primarily through automated tests. As a result, the current codebase is not polished and does not meet my usual standards for
production-quality code. At this point, it's better viewed as a proof-of-concept.

## The features

The project is still in its early stages, and there's plenty of room for improvement, but it already includes a number of features that make working with Sage
Engine INI files much more convenient. These include syntax highlighting, code formatting for files such as `armor.ini`, `upgrade.ini`, `weapon.ini`, and object
definition files, code folding, as well as navigation and autocompletion for `#include` directives.

Let's take a closer look at some of the most notable features.

### Syntax Highlighting

[![A screenshot of syntax highlighting](/assets/bfme2-ini-syntax-highlighting.png)](/assets/bfme2-ini-syntax-highlighting.png)

### Formatting

This is probably my favorite feature. There's something incredibly satisfying about pressing a single button and watching the entire file snap into a clean,
consistent format. It might not seem particularly exciting at first glance, but the formatter already handles a surprising number of small details that make
Sage Engine INI files much easier to read and maintain. Among other things, it:

* indents most blocks consistently
* aligns property values for improved readability
* removes unnecessary whitespace throughout the file, including inside comments
* strips redundant empty comments at the end of a line
* replaces tabs with spaces (although support for tabs might be added in the future for all the tab enjoyers)

Here's a side-by-side comparison showing the changes for a section of `aragorn.ini`:

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

### Include Navigation and Completion

Another useful quality-of-life improvement is support for `#include` macros. When an INI file contains an `#include` macro such as:

```ini
#include "data/ini/object/goodfaction/units/men/aragorn.inc"
```

the plugin can resolve the referenced file, jump to it via the usual "go to declaration" action, and offer path completion based on the current file's
directory.

## What's Next?

There's still a lot of room for improvement. Some of the next ideas on the roadmap include richer inspections, smarter completion for known property values, and
a deeper semantic understanding of the underlying game data.

For example, navigation support could be extended further to support jumping to related game definitions for armors, weapons, and special powers. On top
of that, additional inspection rules could catch invalid or misspelled values. The parser itself also still has room to evolve, especially as more real-world
INI files reveal edge cases and inconsistencies that need to be handled more robustly.

For now, though, I'm already happy with the result. Editing Sage Engine INI files in IntelliJ IDEA feels much nicer than before, and that was exactly the goal.

If this blog post sparked your interest, feel free to try it out - the [source code is available on GitHub](https://github.com/DarkAtra/bfme2-idea-plugin).

<iframe width="245px" height="48px" src="https://plugins.jetbrains.com/embeddable/install/32514"></iframe>
