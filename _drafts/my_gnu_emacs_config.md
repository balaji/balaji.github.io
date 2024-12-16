---
layout: post
title: "My GNU Emacs Setup"
date: 2024-12-16 10:00:00 +0200
category: software
---

#### Quicklinks
- [Package Manager](#package-manager)
- [Editor Enhancements](#editor-enhancements)
- [Minibuffer Enhancements](#minibuffer-enhancements)
- [Project Management](#project-management)
- [Language-specific Packages](#project-management)
- [Org Mode](#org-mode)

I use GNU Emacs version 29 built with [native compilation](https://www.gnu.org/software/emacs/manual/html_node/elisp/Native-Compilation.html) support. Native Compilation compiles lisp functions to native code that promises speedy executions of editor functions. Not all packages need it, but some like LSP mode and JSON parsing benefit from it. Better to have it than not because you cannot add native compilation support after installing Emacs.

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

### [better-defaults](https://github.com/emacsmirror/better-defaults)
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

This is built on the answer from Stackoverflow: [https://stackoverflow.com/a/5801740](https://stackoverflow.com/a/5801740).

### [evil](https://github.com/emacs-evil)
The Extensible VI Layer. The goodness of vim on GNU Emacs.

### [evil-collection](https://github.com/emacs-evil/evil-collection)
This is a collection of bindings that Evil does not cover by default. It is a must have if one is already using evil and writes code for a living.

### [expand-region](https://github.com/magnars/expand-region.el)
Expand Region selects text region like `Ctrl + A` but way better. Expand region increases the selected region by semantic units. Just keep pressing the key until it selects what you want.`C-=` to continuously select and `C--` to reduce the scope of selection.

### [doom-themes](https://github.com/doomemacs/themes)
A megapack of themes that comes part of the configuration framework called Doom, but has the option to be installed separately. 

### [doom-modeline](https://github.com/seagle0128/doom-modeline)
A fancy and fast [mode line](https://www.gnu.org/software/emacs/manual/html_node/emacs/Mode-Line.html). One would have to install [Symbols Nerd Font](https://www.nerdfonts.com/font-downloads) to fully appreciate the usefulness of doom modeline. 

### [solaire-mode](https://github.com/hlissner/emacs-solaire-mode)
It is an aesthetic plugin designed to visually distinguish "real" buffers (i.e. file-visiting code buffers) from "unreal" buffers (like popups, sidebars, log buffers, terminals, etc) by giving the latter a slightly different background. This works particularly well with doom themes.

### [avy](https://github.com/abo-abo/avy)
Text jumping in _visible_ buffers. If you write long text (like blogs or journals or orgmode), avy would be very useful. One can do apparently [anything](https://karthinks.com/software/avy-can-do-anything) with Avy, but for me I had to mentally say "try this with Avy" before I start my text search. I specifically use `avy-goto-word-1` and `avy-goto-line` functions only.

### [ace-window](https://github.com/abo-abo/ace-window)
`ace-window` is the Avy of window management. It in fact installs Avy as a dependency. For a conclusive review of window managers check this [blog post](https://karthinks.com/software/emacs-window-management-almanac/#ace-window).
```lisp
(use-package ace-window
  :config
  (global-set-key (kbd "M-o") 'ace-window)
  (setq aw-dispatch-always t)
  (ace-window-display-mode))
```

## Minibuffer Enhancements
In GNU Emacs, minibuffer is where it reads complicated arguments. It is a special-purpose buffer with a small amount of screen space. There are scores of packages to make the minibuffer experience as smooth as possible.

### [vertico](https://github.com/minad/vertico)
VERTical Interactive COmpletion. Vertico provides a performant and minimalistic vertical completion UI based on the default [completion system](https://www.gnu.org/software/emacs/manual/html_node/elisp/Completion.html). This is similar in intent to [ivy](https://github.com/abo-abo/swiper) or [helm](https://github.com/emacs-helm/helm). Because this is UI only, vertico needs and works well with [complementary packages](https://github.com/minad/vertico?tab=readme-ov-file#complementary-packages). I use all of them. 

### [consult](https://github.com/minad/consult)
Consult provides search and navigation commands based on the Emacs completion function [completing-read](https://www.gnu.org/software/emacs/manual/html_node/elisp/Completion.html). I specifically use these functions
- `consult-fd` - searches for filenames using the `fd` command.
- `consult-ripgrep` - searches for a string using the `rg` command
- `consult-outline`- searches within outlines.
- `consult-line` - searches within a file.

### [marginalia](https://github.com/minad/marginalia)
Annotates completion candidates with richer information.

![Before Marginalia](/assets/img/12.png)
<p align="center"><i>Before Marginalia</i></p>
![After Marginalia](/assets/img/13.png)
<p align="center"><i>After Marginalia</i></p>

### [orderless](https://github.com/oantolin/orderless)
It provides an orderless _completion style_ that divides the pattern into space-separated components, and matches candidates that match all of the components in any order. 

<p align="center"><img src="/assets/img/14.png" alt="Before Orderless" width="500" align="center"></p>
<p align="center"><i>Before Orderless</i></p>
<p align="center"><img src="/assets/img/15.png" alt="After Orderless" width="500" align="center"></p>
<p align="center"><i>After Orderless</i></p>


### [which-key](https://github.com/justbur/emacs-which-key)

## Project Management

### [Whaler](https://github.com/SalOrak/whaler.el)

### [magit](https://magit.vc/)

### [General.el](https://github.com/noctuid/general.el)

### [find-file-in-project](https://github.com/redguardtoo/find-file-in-project)

### [vterm](){:target="__blank"}


## Language Specific Packages

### [rainbow-delimiters]()

### [smartparens]()

### [lsp-mode]()

### [company]()

### [flycheck]()

## Org Mode
 
