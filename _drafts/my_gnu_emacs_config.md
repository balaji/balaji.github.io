---
layout: post
title: "My GNU Emacs Setup"
date: 2024-11-01 10:00:00 +0200
category: software
---

#### Quicklinks
- [Package Manager](#package-manager)
- [Editor Enhancements](#editor-enhancements)
- [Minibuffer Enhancements](#minibuffer-enhancements)
- [Project Management](#project-management)
- [Language-specific Packages](#project-management)
- [Org Mode](#org-mode)

I use GNU Emacs version 29 built with [native compilation](https://www.gnu.org/software/emacs/manual/html_node/elisp/Native-Compilation.html){:target="blank"} support. Native Compilation compiles lisp functions to native code that promises speedy executions of editor functions. Not all packages need it, but some like LSP mode and JSON parsing benefit from it. Better to have it than not because you cannot add native compilation support after installing Emacs.

## Package Manager
### [Straight.el](https://github.com/radian-software/straight.el){:target="_blank"}
GNU Emacs comes with a built-in package manager that uses the [`(use-package)`](https://www.gnu.org/software/emacs/manual/html_node/use-package/index.html){:target="_blank"} macro to install and manage packages. Straight is compatible and builds on it. Some of its advantages over default package manager are:
- It manages the package repositories. No need to add extra code to add MELPA, Marmalade, etc.
- Clones from Git repos for bleeding edge changes. Default package manager installs from published versions by default.
- Creates lock files if you want to freeze your setup.

<div class="callout callout-info">
A newer alternative for Straight that is gaining more traction is <a href="https://github.com/progfolio/elpaca" target="_blank">Elpaca</a>. Interestingly both are maintianed by the same person.
</div>

## Editor Enhancements
Settings that enhance the editor experience through third-party packages or configurations.

### [better-defaults](https://github.com/emacsmirror/better-defaults){:target="blank"}
better-defaults is a good package to start with for a visually clutter free and less intrusive user-experience with emacs. Here are some more options I prefer to add to my init file that are not provided by better-defaults.

```lisp
(cua-mode t) ; regular keyboard fns for cut, copy, paste and delete
(global-linum-mode 1) ; in Emacs v30, this is (global-display-line-numbers-mode t)
(scroll-bar-mode 0) ; aesthetics.
(vc-follow-symlinks t) ; avoids the question everytime we open a symlink of a file.
```

### Menu Bar mode on GUI editor
I prefer to have menu-bar on GUI mode. better-defaults enables it (or rather, not disables it) for Mac only. This function re-enables menubar for all window systems but keeps it disabled in terminal.

```lisp
(defun my/on-window-display ()
  (if (display-graphic-p)
      (progn
        (menu-bar-mode 1))
    (progn
      (menu-bar-mode 0))))

(add-hook 'after-make-frame-functions
          (lambda (frame)
            (select-frame frame)
            (my/on-window-display)))
(add-hook 'after-init-hook #'my/on-window-display)
```

This is built on the answer from Stackoverflow: [https://stackoverflow.com/a/5801740](https://stackoverflow.com/a/5801740){:target="_blank"}.

### [evil](https://github.com/emacs-evil){:target="_blank"}
The Extensible VI Layer. The goodness of vim text editing features, but on GNU Emacs.

### [evil-collection](https://github.com/emacs-evil/evil-collection){:target="_blank"}
This is a collection of bindings that Evil does not cover by default. It is a must have if one is already using evil and writes code for a living.

### [expand-region](https://github.com/magnars/expand-region.el){:target="_blank"}
Expand Region selects text region like `Ctrl + A` but way better. Expand region increases the selected region by semantic units. Just keep pressing the key until it selects what you want.`C-=` to continuously select and `C--` to reduce the scope of selection.

### [doom-themes](https://github.com/doomemacs/themes){:target="_blank"}
A megapack of themes that comes part of the configuration framework called Doom, but has the option to be installed separately. 

### [doom-modeline](https://github.com/seagle0128/doom-modeline){:target="_blank"}
A fancy and fast [mode line](https://www.gnu.org/software/emacs/manual/html_node/emacs/Mode-Line.html){:target="_blank"}. One would have to install [Symbols Nerd Font](https://www.nerdfonts.com/font-downloads){:target="_blank"} to fully appreciate the usefulness of doom modeline. 

### [solaire-mode](https://github.com/hlissner/emacs-solaire-mode){:target="_blank"}
It is an aesthetic plugin designed to visually distinguish "real" buffers (i.e. file-visiting code buffers) from "unreal" buffers (like popups, sidebars, log buffers, terminals, etc) by giving the latter a slightly different background. This works particularly well with doom themes.

### [Avy](){:target="_blank"}

### [Ace Window](){:target="_blank"}

## Minibuffer Enhancements

### [vertico](){:target="_blank"}

### [consult](){:target="_blank"}

### [marginalia](){:target="_blank"}

### [orderless](){:target="_blank"}

### [which-key](https://github.com/justbur/emacs-which-key){:target="_blank"}

## Project Management

### [Whaler](https://github.com/SalOrak/whaler.el){:target="_blank"}

### [magit](https://magit.vc/){:target="_blank"}

### [General.el](https://github.com/noctuid/general.el){:target="_blank"}

### [find-file-in-project](https://github.com/redguardtoo/find-file-in-project){:target="_blank"}

### [vterm](){:target="__blank"}


## Language Specific Packages

### [rainbow-delimiters](){:target="_blank"}

### [smartparens](){:target="_blank"}

### [lsp-mode](){:target="_blank"}

### [company](){:target="_blank"}

### [flycheck](){:target="_blank"}

## Org Mode
 
