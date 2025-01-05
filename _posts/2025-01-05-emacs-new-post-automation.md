---
layout: post
date: 2025-01-05T18:56:32+01:00
title: Creating a new Jekyll blog post in Emacs
categories:
  - software
  - emacs
tags:
  - blog
gist: >-
  New blog: <a href="/emacs-new-post-automation">Creating a new Jekyll blog post in Emacs</a> 
---

I use [Jekyll](https://jekyllrb.com/) static site generator for this blog and it is served through [GitHub Pages](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll). One needs to create a new file under `_posts` folder to write a new blog. It should be of the [format](https://jekyllrb.com/docs/posts/#creating-posts) `yyyy-mm-dd-title.md`. 

It gets tedious to create the file manually. We can do better. I automated the following on GNU Emacs:
1. Creating the file
2. snippet for adding the the [front matter](https://jekyllrb.com/docs/front-matter/)

### Creating the file through a command on GNU Emacs:

This function would wait for a file name (`name`) and then create the file in the path: `~/path/{yyyy-mm-dd}-name.md`, where `{yyyy-mm-dd}` would be the current date.


```elisp
(defun new-post(blog-name)
  "Creates a new microblog entry for my blog in balaji.dev"
  (interactive "sBlog-name:")
  (let ((prefix (format-time-string "%Y-%m-%d-" (current-time))))
    (with-temp-buffer (write-file (concat "~/path/" prefix blog-name ".md")))))
```
I invoke this function as `M-x new-post`. I did not bind this to a key, I don't think I need it and I won't remember it anyway.

### Front matter snippets

#### package setup
I use [yasnippet](https://github.com/joaotavora/yasnippet) for code snippets. This is my package config:

```elisp
(use-package yasnippet
  :ensure t
  :init
  (yas-global-mode 1)
  :hook
  ((markdown-mode) . (lambda () (set (make-local-variable 'yas-indent-line) 'fixed)))
  :config
  (setq yas-snippet-dirs
        (append yas-snippet-dirs '("~/projects/dotfiles/emacs/custom-snippets")))
  (yas-reload-all))
```

- The `:init` section ensures that the package is loaded globally.
- The `:config` section is there to add our `custom-snippets` folder to the path and reload it.
- The `:hook` section is to ensure that we get the indentation style "as is" from the snippet file. By default yasnippets tries to auto-indent based on the major mode and it doesn't work for the snippet I use in markdown-mode. It might probably be a good idea to set the variable `yas-indent-line` globally to `fixed`. I might try that later if the snippet is an issue for another major mode.

#### snippets
Here's a snippet for the front matter of a blog post:

```
# -*- mode: snippet -*-
# name: blogpost
# key: blogpost
# --
---
layout: post
date: `(format-time-string "%Y-%m-%dT%H:%M:%S%:z")`
title: ${1:title}
categories:
  - ${2:category}
tags:
  - blog
gist: >-
  New blog: <a href="/${4:href}">${3:gist}</a> 
img:
  src: 
  width: 
---
$0
```

This text should be stored as a file under the `markdown-mode` sub-directory. There are two snippets here, one for a blog post and another for a [microblog](/hello-world) post.

```bash
~/projects/dotfiles/emacs/custom-snippets$ tree
.
└── markdown-mode
    ├── blogpost
    └── microblog
```

...and that is it. My new workflow is `M-x new-post` and use a snippet to load the front matter. Emacs makes it easy. 
