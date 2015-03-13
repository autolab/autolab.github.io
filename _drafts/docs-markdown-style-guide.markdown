---
layout: post
title: "[docs] Markdown Style Guide"
author: Jake Zimmerman
tags: [docs]
---

This is a short style guide for writing Markdown for this blog.

# Links

- Prefer the reference link style (unless you're using `#internal-links`):

```
This is [an example][id] reference-style link.
[id]: http://example.com/
```
- Note: it appears that RedCarpet recognizes the above ID as an actual ID. See
  the source if you are confused about what a reference-style link looks like.
- Put reference link IDs and URLs at bottom of page.
- At the bottom of the page, order reference links in the same order as they
  appear in the page, unless there is a more obvious ordering


# Headings

- Use `#`, `##`, `###`, etc. for headings (ATX-style headings)
- Use `#` for the first heading
  - This should be different from the front-matter title (the one in the Jekyll
    post). They will be styled at different sizes.
- Put two newlines before a heading, and one newline after


# Miscellaneous

- Wrap long lines to 80 characters when possible (sometimes this isn't possible,
  like inside tables.)
- Prefer spaces to tabs


