---
layout: post
title: "My Emacs Setup"
date: 2024-11-01 10:00:00 +0200
category: software
---

#### Quicklinks
- [Package Manager](#package-manager)
- [Editor Essentials](#editor-essentials)
- [Editor Enhancements](#editor-enhancements)
- [Minibuffer Enhancements](#minibuffer-enhancements)
- [Project Management](#project-management)
- [Language-specific Packages](#project-management)
- [Org Mode](#org-mode)

I use Emacs version 29 built with [native compilation](https://www.gnu.org/software/emacs/manual/html_node/elisp/Native-Compilation.html){:target="blank"} support. It compiles lisp functions to native code that promises speedy executions of editor functions. Not all packages need it, but some like LSP mode and JSON parsing benefit from native compilation. Better to have it than that because you cannot just add native compilation support after installing Emacs.

## Package Manager
### [Straight.el](https://github.com/radian-software/straight.el){:target="_blank"}
Emacs comes with a built-in package manager that uses the [`(use-package)`](https://www.gnu.org/software/emacs/manual/html_node/use-package/index.html){:target="_blank"} macro to install and manage packages. Straight is compatible and builds on it. It manages the package repositories, clones from Git repos for bleeding edge changes, and creates lock files if you want to freeze your setup.

<div class="callout callout-info">
A newer alternative for Straight that is gaining more traction is <a href="https://github.com/progfolio/elpaca" target="_blank">Elpaca</a>. Interestingly both are maintianed by the same person.
</div>

## Editor Essentials
Editor configurations that are the bare essentials or as the Emacs world calls it - "better defaults".

### [better-defaults](https://github.com/emacsmirror/better-defaults){:target="blank"}
better-defaults is a must have for a visually clutter free and less intrusive user-experience with emacs. These are three options I add on top of what better-defaults provides.

```lisp
(cua-mode t) ; regular keyboard fns for cut, copy, paste and delete
(global-linum-mode 1) ; in Emacs v30, this is (global-display-line-numbers-mode t)
(scroll-bar-mode 0) ; aesthetics.
(savehist-mode t) ; cycle through M-x history with M-p and M-n
```

### Menu Bar mode on GUI editor
I prefer to have menu-bar on GUI mode. better-defaults enables it (or rather, not disables it) for Mac only. This command re-enables menubar for all window systems that Emacs can identify. I place this after better-defaults use-package macro. TBH, this doesn't work well when run in daemon mode.

```lisp
(if (memq window-system '(w32 x ns pc mac))
    (menu-bar-mode 1))
```

Emacs documentation for the variable `window-system` (`C-h v window-system`) copy pasted below. Its value will be `nil` when called from a Terminal, otherwise one of the below values depending on the OS.
```
window-system is a variable defined in ‘C source code’.

Its value is nil
It is a terminal-local variable; global value is the same.

Name of window system through which the selected frame is displayed.
The value is a symbol:
 nil for a termcap frame (a character-only terminal),
 ‘x’ for an Emacs frame that is really an X window,
 ‘w32’ for an Emacs frame that is a window on MS-Windows display,
 ‘ns’ for an Emacs frame on a GNUstep or Macintosh Cocoa display,
 ‘pc’ for a direct-write MS-DOS frame.
```

## Editor Enhancements
These are settings that enhance the editor experience through third-party packages.

### [evil](https://github.com/emacs-evil){:target="_blank"}
The Extensive VI Layer. Vim navigation and text manipulation is brilliant and I am a convert. My fingers know what to do before I even think about it.

### [evil-collection](https://github.com/emacs-evil/evil-collection){:target="_blank"}
This package is a must have if one is already using evil and writes code for a living.

### [expand-region](https://github.com/magnars/expand-region.el){:target="_blank"}
Expand Region selects text region like `Ctrl-A` but not same. The beauty of this is it starts with the token on where the cursor is and progressively increases the selected region through semantic scope. `C-=` to continuously select and `C--` to reduce the scope of selection.

### [solaire-mode](https://github.com/hlissner/emacs-solaire-mode){:target="_blank"}

### [doom-themes](https://github.com/doomemacs/themes){:target="_blank"}

### [doom-modeline](https://github.com/seagle0128/doom-modeline){:target="_blank"}

### Avy

### Ace Window

## Minibuffer Enhancements

### [ivy](){:target="_blank"}

### [marginalia](){:target="_blank"}

### [orderless](){:target="_blank"}

### [which-key](https://github.com/justbur/emacs-which-key){:target="_blank"}


## Project Management

### [Whaler](https://github.com/SalOrak/whaler.el){:target="_blank"}

### [magit](https://magit.vc/){:target="_blank"}

### [General.el](https://github.com/noctuid/general.el){:target="_blank"}

### [fzf](https://github.com/bling/fzf.el){:target="_blank"}

### [find-file-in-project](https://github.com/redguardtoo/find-file-in-project){:target="_blank"}


## Language Specific Packages

### rainbow-delimiters

### smartparens

### lsp-mode

### company

### flycheck

### origami

## Org Mode
 
