---
layout: post
title: "[docs] Jekyll and Octopress Tips"
author: Jake Zimmerman
tags: [docs, jekyll, octopress]
---

We're using Octopress 3.0 on top of Jekyll to help make the day-to-day usage of
Octopress simple. Here are some tips for getting the most out of Jekyll and
Octopress.

# Jekyll

Jekyll is a blogging platform that puts the emphasis on text files. It's the
Unix hacker's dream! Content is generated using HTML, Markdown, and YAML, a
data-serialization format similar to JSON but easier to write. It has some
excellent documentation over at <http://jekyllrb.com/>.

Jekyll is a __static site generator__. That means it generates a single set of
files HTML which don't change at runtime.

## Front-Matter

The driving force behind Jekyll is __front-matter__. This is simple a section of
text at the start of a file that looks like this:

```yaml
---
layout: post
title: "[docs] Jekyll and Octopress Tips"
author: Jake Zimmerman
---
```

As you can see, there's three hyphens, a lump of YAML data, and then more
hyphens.

Any file that Jekyll finds with something of this format at the top of the file
it will treat specially, according to where it is in the directory structure.

## Directory Structure

Jekyll takes advantage of where files are in the directory structure to know
what to do with them. For example, all files inside `_posts/` will be generated
and served as posts.

These are the folders you'll likely want to be familiar with:

| Directory     | Purpose                                                                                     |
| ---------     | -------                                                                                     |
| `_posts/`     | House published posts. Files are either HTML or Markdown files.                             |
| `_drafts/`    | Houses unpublished posts.                                                                   |
| `_layouts/`   | Tells Jekyll how specific files (pages and posts) should be generated.                      |
| `_site/`      | Houses the source of the site that Jekyll generates. Don't change files in here!            |
| `_sass/`      | Jekyll compiles sass files for us.                                                          |
| `_templates/` | This is actually an Octopress-specific directory. It holds draft, post, and page templates. |

There are more directories that are important to Jekyll. See the [full list here][dir-struct].


## Configuration

All Jekyll configuration is done in the `_config.yml` file. I'm not going to
talk about every option, but know that this file exists.


## Posts, Pages, and Drafts

Jekyll understands three types of content: Posts, Pages, and Drafts. A post is
basically a markdown file in `_posts/` (almost always with the key `layout:
post` in the front-matter) that's tied to a publishing date. Drafts are similar,
but they get placed in `_drafts/`. Pages are special. They can be placed
anywhere, but don't have a publishing date tied to them. The file `tags.html` is
an example of a Page.

All posts show up on the landing page of the blog. All pages show up in the
header on each webpage. Drafts show up in the same place as posts, but only if
you start the Jekyll server with `jekyll serve --drafts`.


# Octopress

If you're familiar with Octopress, you might not have the fondest memories of
it. It used to be pretty janky. It's come a long way, and the release candidates
versions of its 3.0 release are pretty stable. Here we'll describe some of the
features that you'll probably want to use.


## Getting Documentation

You can find the documentation for all the Octopress commands _and subcommands_
by using the `--help` flag at the terminal. You can alternatively run

```console
$ octopress docs
```

to launch the Octopress docs as a web server. Octopress is currently still
pre-release, so there isn't much documentation online other than the [source
repo][octopress].


## Creating Posts, Pages, and Drafts

The most common command you'll likely use with Octopress is

```console
$ octopress new draft "My Cool Title"
```

This will create a new markdown file, show you the path of the created file, and
initialize the contents of that file with everything that we've told it to (see
`_templates/draft`).

To publish this draft (taking if from a draft to a post), [see
below](#publishing-drafts). If you'd like to skip making a draft and go straight
ahead with a post, use

```console
$ octopress new post "My Cool Title"
```

You might also like to specify an exact date (as opposed to `Time#now`):

```console
$ octopress new post -d <date>
```


## Publishing Drafts

To convert a draft to a post, run

```console
$ octopress publish _draft/my-draft.markdown
```

You can undo this if you have to:

```console
$ octopress unpublish _post/2015-01-01-my-post.markdown
```


## Deployment

Octopress has tools for automating deployment (`octopress deploy`), but for now
we'll probably not need them. These tools come in handy if we ever start to use
custom plugins that aren't supported by the `GitHub Pages` runtime, or if we
ever decided to host our own static blog. For now, we can get by just fine
pushing the raw Jekyll site to GitHub and letting GitHub take care of the
compilation.

For a list of Jekyll plugins that are supported by the GitHub Pages runtime,
[see here][plugins].


[dir-struct]: http://jekyllrb.com/docs/structure/
[octopress]: https://github.com/octopress/octopress
[plugins]: https://pages.github.com/versions/


