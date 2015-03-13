---
layout: post
title: "[docs] Kitchen Sink"
author: 'Jake Zimmerman'
tags: [docs]
---

You can use Markdown to create new posts. The format is similar to the Markdown
you use on GitHub README files and Wiki pages. This page is meant to demonstrate
all the features that the Markdown rendering engine we're using can handle.

If you're familiar with Markdown rendering engines, you can check the
`_config.yml` file to find that we're using redcarpet. Here are all the
extensions that we have enabled (this list might have changed, check the
`_config.yml` file as the Absolute Source of Truth™):

```yaml
redcarpet:
  extensions:
    - no_intra_emphasis
    - tables
    - fenced_code_blocks
    - autolink
    - disable_indented_code_blocks
    - strikethrough
    - footnotes
    - with_toc_data
```

As you can see, the syntax is basically [GitHub-Flavored Markdown][gfm], with a few key
differences:

- Linebreaks between words __do not__ start a new paragraph. This makes it
  easier to keep lines under 80 characters, so please try to do this. Vim makes
  this easy by providing `gq`. Simple select some text, run `gq`, and your text
  will be automatically (and intelligently) formatted to 80 characters.
- You __can't__ indent code with four spaces to make a code block. Just use
  fenced code blocks.
- You can create footnotes! Check the source for how to create these.[^1]


# Example Markdown

Notice how all headings will be given a slug id. That means you can link to specific pieces of a post. For example, [click this link](#example-markdown).

This text will be made into a paragraph.

Look at the awesome table!

| Heading 1 | Heading 2 |
| ---       | ---       |
| Data 1    | Data 2    |

Tables are pretty robust (meaning you can format them pretty much anyway you
want and still end up with a table), but you can't have newlines within a row.
Workarounds:

- Use a `<br>` tag instead of a physical `\n` character.
- Write the table in HTML.

There's also probably a plugin for your editor to make pretty creating ASCII
tables easy.

We'll probably be using fenced code blocks a lot:

```console
$ echo 'echo "Hello, world\!"'
echo "Hello, world\!"
```

With fenced code blocks, you can specify a language for syntax highlighting.
There's a complete list of [recognized syntax keywords][linguist] on GitHub. The
keywords are the root-level identifiers, plus the root-level identifiers but
lowercase, plus everything under an `alias:` declaration. Confusing, I know, but
it'll do.

One syntax I didn't realize was available until recently was the one above: it's
handy for highlighting console input and output: all lines starting with `$` are
taken to be lines that you entered into a shell!




[gfm]: https://help.github.com/articles/github-flavored-markdown/
[linguist]: https://github.com/github/linguist/blob/master/lib/linguist/languages.yml

[^1]: I'm a footnote!
