---

layout: post
title: "Why I Moved From WordPress to Jekyll"
date: 2026-07-29 15:18:00 +0800
categories:
- blogging
- jekyll
- wordpress
- ai

---

So, I moved my blog from WordPress to Jekyll.

This was not because WordPress suddenly became bad, evil, cringe, or personally broke into my house and rearranged my furniture. WordPress is still perfectly good software. It just stopped being particularly good **for me**.

As my blog got bigger, my posts also started becoming longer and more ambitious. At that point, managing everything through WordPress became increasingly tedious.

## WordPress and AI do not speak the same language

In practice, WordPress expects me to work with either HTML or Gutenberg blocks.

That is fine when you are editing everything visually inside WordPress itself. It becomes considerably less fine when you want to move the post somewhere else, edit it using another tool, or feed it into an AI model for proofreading.

Yes, I use AI.

Mostly for proofreading, with the occasional rewrite when a paragraph has somehow mutated into an unreadable linguistic traffic accident.

The problem is that most AI models are extremely comfortable with Markdown. Give them a Markdown document and they generally understand the title, headings, lists, links, quotations, code blocks, and overall structure immediately.

Give them a pile of Gutenberg block markup, however, and they respond like you have handed them fragments of an ancient alien transmission.

What does this mean?

```html
<!-- wp:paragraph -->
<p>Hello!</p>
<!-- /wp:paragraph -->
```

To WordPress, that is a paragraph.

To an AI model, it is technically still a paragraph, but now it has to work around WordPress-specific comments and formatting structures that add absolutely nothing useful to the actual writing.

Longer posts only make this worse. The more headings, quotations, lists, links, and other formatting a post contains, the more Gutenberg debris there is for another tool to misunderstand, accidentally remove, or somehow duplicate seventeen times.

## My increasingly ridiculous workflow

For a while, my workflow looked something like this:

1. Write or edit the post in WordPress.
2. Copy the WordPress version.
3. Give it to one AI and ask it to translate everything into Markdown.
4. Give that Markdown version to the main AI for proofreading.
5. Convert the edited version back into something WordPress accepts.
6. Hope nothing exploded during translation.

This is objectively silly.

I cannot keep forcing one AI to translate WordPress into Markdown purely so I can feed it into another AI.

And if I am already converting everything into Markdown anyway, why not just write the damn thing in Markdown in the first place?

Guess what language Jekyll speaks.

**Markdown.**

## Markdown is basically everywhere

Markdown is already the default writing format for a huge amount of technical stuff.

README files use Markdown. GitHub uses Markdown. Documentation platforms use Markdown. Issue trackers use Markdown. Static-site generators use Markdown. A frankly unreasonable percentage of the modern developer world runs on Markdown files sitting inside Git repositories.

There are exceptions, naturally.

Research papers often speak LaTeX.

Some older forum engines speak BBCode for some godforsaken reason.

*Ahem.*

**Scratch Forums.**

And websites themselves can technically speak almost anything, provided it can eventually be translated into HTML.

But for ordinary writing - headings, paragraphs, links, images, lists, quotations, and code - Markdown is simple, readable, portable, and widely supported.

Even without Jekyll, this:

```markdown
## Cool Heading

This is a paragraph containing a [link](https://example.com).

- One thing
- Another thing
- A third, increasingly concerning thing
```

is much nicer to work with than a massive blob of HTML or Gutenberg metadata.

## Why Jekyll works better for me

Jekyll lets me write each post as an ordinary Markdown file.

A post looks something like this:

```markdown
---
layout: post
title: "My Extremely Normal Blog Post"
date: 2026-07-29 15:18:00 +0800
categories:
  - personal
---

# Hello!

Here are some words.
```

That file can be edited in basically any text editor. It can be stored in Git. I can view its history, compare revisions, create branches, back it up, and move it somewhere else without having to export a database or fight a visual editor.

It also means the version I write, the version I proofread, and the version Jekyll publishes are all essentially the same document.

No translation layer. No Gutenberg archaeology. No begging an AI model to preserve fourteen mysterious HTML comments without eating a paragraph.

I write Markdown.

The AI reads Markdown.

Jekyll turns the Markdown into HTML.

The browser displays the HTML.

Beautiful. A functioning food chain.

## WordPress still has advantages

None of this means WordPress is useless.

WordPress is still much better if you want things like:

* A visual editor
* User accounts
* Built-in comments
* A management dashboard
* E-commerce
* Plugins for basically anything imaginable
* Non-technical people editing the same website

Those are real advantages.

I simply do not care about most of them enough to justify keeping WordPress around.

Not having a visual editor is, at worst, a minor thirty-second inconvenience. I am already comfortable writing Markdown, so typing `## Heading` instead of clicking a heading button is not exactly an unbearable hardship.

Accounts? Do not need them.

E-commerce? I am not running an online shop.

A giant dashboard? GitHub already shows me the files, commits, deployments, and build logs.

Comments are the one feature I might actually miss - but even then, there are alternatives such as [Utterances](https://utteranc.es/), which stores comments as GitHub issues.

So even that problem is not particularly fatal.

## In conclusion

WordPress is built around managing a dynamic website through a browser-based interface.

Jekyll is built around turning plain text files into a website.

For some people, the WordPress approach is much easier.

For me, plain text won.

My posts are getting longer, my workflow is becoming more technical, and Markdown is already the format used by nearly every tool involved in writing them.

Moving to Jekyll means fewer conversions, fewer formatting problems, easier version control, and considerably less time spent asking:

> What the hell does this Gutenberg block even mean?

So yeah.

I moved from WordPress to Jekyll because I was already doing everything in Markdown with several unnecessary steps in between.

I have now removed the unnecessary steps.

Revolutionary technology.
