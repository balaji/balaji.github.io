---
layout: post
title: "My Emacs Setup"
date: 2024-11-01 10:00:00 +0200
category: software
---

## Package Manager
- [Straight.el](https://github.com/radian-software/straight.el){:target="_blank"}: Emacs comes with a built-in package manager that uses the [`(use-package)`](https://www.gnu.org/software/emacs/manual/html_node/use-package/index.html){:target="_blank"} macro to install and manage packages. Straight is compatible and builds on it. It manages the package repositories, clones from Git repos for bleeding edge changes, and creates lock files if you want to freeze your setup.

<div class="callout callout-info">
The closest and newer alternative for Straight is <a href="https://github.com/progfolio/elpaca" target="_blank">Elpaca</a>. Interestingly both are maintianed by the same person.
</div>

## Editor Essentials
These are some editor configuration that I hand-rolled. Surely there are 

- If possible use a Emacs version that is built with [native compilation](https://www.gnu.org/software/emacs/manual/html_node/elisp/Native-Compilation.html){:target="blank"} support. It compiles lisp functions to native code that promises speed in editor functions. Not all packages need it, but some like LSP mode and JSON parsing benefit from native compilation.

- [better-defaults](https://github.com/emacsmirror/better-defaults){:target="blank"} is a must-have for a visually clutter free and less intrusive UX with emacs. 

- These are some of the options which are not included with better-defaults.

```lisp
(cua-mode t) ; regular keyboard fns for cut, copy, paste and delete
(global-linum-mode 1) ; in Emacs v30, this is (global-display-line-numbers-mode t)
(scroll-bar-mode 0) ; aesthetics.
```


## Editor Enhancements
## Programming Language Specific Enhancements
## Orgmode
 
