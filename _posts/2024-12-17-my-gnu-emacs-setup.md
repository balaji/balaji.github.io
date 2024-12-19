---
layout: post
title: "My GNU Emacs Setup"
date: 2024-12-16 10:00:00 +0200
category: software
tags:
  - blog
gist: >-
  <a href="/my-gnu-emacs-setup">My GNU Emacs Setup</a>
---

#### Quicklinks
- [Package Manager](#package-manager)
- [Editor Enhancements](#editor-enhancements)
- [Minibuffer Enhancements](#minibuffer-enhancements)
- [Project Management](#project-management)
- [Programming related packages](#programming-related-packages)
- [Org Mode](#org-mode)
- [Full Config](#full-configuration)

I use GNU Emacs version 29 built with [native compilation](https://www.gnu.org/software/emacs/manual/html_node/elisp/Native-Compilation.html) support. Native Compilation compiles lisp functions to native code that promises speedy executions of editor functions. Not all packages need it, but some like LSP mode and JSON parsing benefit from it. Better to have it than not because you cannot add native compilation support after installing Emacs.

## Package Manager
### [Elpaca](https://github.com/progfolio/elpaca)
GNU Emacs comes with a built-in package manager that uses the [`(use-package)`](https://www.gnu.org/software/emacs/manual/html_node/use-package/index.html){:target="_blank"} macro to install and manage packages. Elpaca is compatible with that macro and it is probably the only reason I could migrate to Elpaca from Straight.el

<div class="callout callout-info">
<strong>Alternatives:</strong>
<ul>
<li><code>Package.el</code> - the default package manager</li>
<li><a href="https://github.com/radian-software/straight.el" target="_blank">straight.el</a></li>
</ul>
<h4>What I like</h4>
<ul>
<li>Async, fast and to not have to worry about which repository to pull packages from.</li>
<li>Read the comment in Reddit from the package maintainer themselves: <a href="https://www.reddit.com/r/emacs/comments/19bs8w9/comment/kiu27mo">link</a></li>
</ul>
</div>

## Editor Enhancements
Settings that enhance the editor experience through third-party packages or configurations.

### [better-defaults](https://github.com/emacsmirror/better-defaults)
better-defaults is a good package to start with for a visually clutter free and less intrusive user-experience with GNU Emacs. Here are some more options I add to my init file that are not provided by better-defaults.

```lisp
(cua-mode t) ; regular keyboard fns for cut, copy, paste and delete
(global-display-line-numbers-mode 1) ; in Emacs v28 and below this is (global-linum-mode t)
(scroll-bar-mode 0) ; aesthetics.
(vc-follow-symlinks t) ; avoids the question everytime we open a symlink of a file.
(add-to-list 'default-frame-alist '(fullscreen . maximized)) ; start with a maximized window
```

### Menu Bar mode on GUI editor
I prefer to have menu-bar on GUI mode. Menu Bar can be a very useful tool and disabling it for new users is considered a [bad advice](https://www.masteringemacs.org/article/bad-emacs-advice#disabling-the-menu-bar). `better-defaults` enables it (or rather, not disables it) for Mac only. This function re-enables menubar for all window systems but keeps it disabled in terminal.

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
Text jumping in _visible_ buffers. If you write longform text (like blogs or journals or notes), avy would be very useful. One can do apparently [anything](https://karthinks.com/software/avy-can-do-anything) with Avy, but for me I had to mentally pepare myself to use Avy before I start my text search. The cool thing is it doesn't take long for it to become a part of my core editing workflow. I specifically use `avy-goto-word-1` and `avy-goto-line` functions only.

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
VERTical Interactive COmpletion. Vertico provides a performant and minimalistic vertical completion UI based on the default [completion system](https://www.gnu.org/software/emacs/manual/html_node/elisp/Completion.html). This is similar in intent to [ivy](https://github.com/abo-abo/swiper) or [helm](https://github.com/emacs-helm/helm). Because this is UI only, vertico needs and works well with [complementary packages](https://github.com/minad/vertico?tab=readme-ov-file#complementary-packages). I use all of them (well except for [embark](#embark)). 

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

<p align="center"><img src="/assets/img/14.png" alt="Before Orderless" width="600" align="center"></p>
<p align="center"><i>Before Orderless</i></p>
<p align="center"><img src="/assets/img/15.png" alt="After Orderless" width="600" align="center"></p>
<p align="center"><i>After Orderless</i></p>

### [which-key](https://github.com/justbur/emacs-which-key)
The good news is that GNU Emacs 30 will include which-key-mode by default. which-key is a minor mode for Emacs that displays the key bindings following your currently entered incomplete command (a prefix) in a popup. 

<p align="center"><img src="/assets/img/17.png" alt="Before which-key" align="center"></p>
<p align="center"><i>Typing `C-h` without which-key-mode</i></p>
<p align="center"><img src="/assets/img/16.png" alt="After which-key" align="center"></p>
<p align="center"><i>Typing `C-h` with which-key-mode</i></p>

## Project Management
"Projects" are root folders/working directories. These are packages that makes working with projects better.

### [General.el](https://github.com/noctuid/general.el)
`general.el` provides a more convenient method for binding keys in GNU Emacs. In other words, one can create their own key bindings for most used functions. This is the bare minimum of what general can provide, checkout their [documentation](https://github.com/noctuid/general.el?tab=readme-ov-file#key-features) for the full feature list.

The reason why I kept `general.el` under project management is because all the keys I have mapped so far are for project management.

<div class="callout callout-info">
The feature set I use: which is to create a new keymap with custom bindngs, doesn't really require a separate package. They can be done with standard elisp functions.
</div>

```lisp
(use-package general
  :ensure t
  :config
  (general-auto-unbind-keys)
  (general-define-key
   :states '(emacs insert normal)
   :prefix-map 'custom/prefix
   :global-prefix "C-c"
   :non-normal-prefix "M-SPC"
   :prefix "SPC"
   "ff" 'find-file-in-project-by-selected
   "sf" 'consult-fd
   "sr" 'consult-ripgrep
   "so" 'consult-outline
   "sc" 'avy-goto-char
   "sw" 'avy-goto-word-1
   "sl" 'avy-goto-line
   "pp" 'whaler
   "wr" 'whaler-populate-projects-directories)

  (general-create-definer leader-global :keymaps 'custom/prefix))
```

### [Whaler](https://github.com/SalOrak/whaler.el)
Whaler is a lightweight project manager. It is a package to move between directories while maintaining a sense of working directory. It is an alternative to [`projectile`](https://github.com/bbatsov/projectile).

### [magit](https://magit.vc/)
Magit is the most intuitive git client ever across all editors. It is widely acknowledged so and I am not the last one to give accolades it. There are certain developer tools that make you _want_ to code, to write, to engage. Vim and Emacs are among them and Magit certainly earns its place in that list.

### [find-file-in-project](https://github.com/redguardtoo/find-file-in-project)
While [Consult](#consult) covers all search and navigation needs for the editor, the `consult-fd` command is eager, as in it will start searching the project as and when we start typing. While this is OK for most projects, the command slows down when dealing with really large mega projects with LSP mode enabled. In such cases we would either have to disable LSP mode which may work or use [`find-file-in-project-by-selected`](https://github.com/redguardtoo/find-file-in-project?tab=readme-ov-file#find-file-in-project-by-selected) command which first accepts a wildcard keyword to narrow the files list and then we can search the list like any `fd` command.

## Programming related packages
I am not covering the programming language specific packages here. Only the packages that make programming better.

### [eat](https://codeberg.org/akib/emacs-eat)
Eat stands for Emulate A Terminal. It is a terminal emulator. The UX and feature set is better than [`vterm`](https://github.com/akermu/emacs-libvterm). This also has a neat little function called `eat-project` which opens Eat in the root directory of the project. There's a lot going on with Eat:

![Eat Modes](https://abode.karthinks.com/share/eat-modes.png){:width="600"}
<p><i><a href="https://www.reddit.com/r/emacs/comments/17nl7cw/comment/k7upuab/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button">original post</a></i></p>

### [rainbow-delimiters](https://github.com/Fanael/rainbow-delimiters)
`rainbow-delimiters` highlights delimiters such as parentheses, brackets or braces according to their depth. Each successive level is highlighted in a different color. 

<p align="center"><img src="/assets/img/19.png" alt="Before which-key" width="400" align="center"></p>
<p align="center"><i>without rainbow-delimiters-mode</i></p>
<p align="center"><img src="/assets/img/18.png" alt="After which-key" width="400" align="center"></p>
<p align="center"><i>with rainbow-delimiters-mode</i></p>

### [smartparens](https://github.com/Fuco1/smartparens)
Smartparens is a minor mode for dealing with pairs in Emacs. Since I don't deal with lisp-like languages a lot, the only features of smartparens that I use often is their auto pair feature and `sp-forward-slurp-sexp` which forwards the closing parantheses by one token. This is better than `paredit` because by default it gets out of the way.

### [lsp-mode](https://github.com/emacs-lsp/lsp-mode)
There are several lsp modes in development. The most active one is the LSP mode and the other is `eglot` which is part of GNU Emacs from v29. There is more documentation and other packages support for `lsp-mode` than `eglot` at the time of writing.

### [corfu](https://github.com/minad/corfu)
COmpletion in Region FUnction. Corfu enhances in-buffer completion with a small completion popup. Corfu is a small package, which relies on the Emacs completion facilities and concentrates on providing a polished completion UI. This is so much easier to customize and works well with other packages such as `orderless` to provide a hassle free UX.

### [cape](https://github.com/minad/cape)
Corfu does not include its own completion backends. The Emacs built-in Capfs and the Capfs provided by third-party programming language packages are often sufficient. Additional Capfs and completion utilities are provided by the separate Cape package.

### [flycheck](https://github.com/flycheck/flycheck)
Modern on-the-fly syntax checking extension for GNU Emacs. This is one of the install and forget packages. To be honest, I haven't used GNU Emacs in anger for even a week in software development to have any opinion on `flycheck`, but it has worked well so far with my code katas.

## Org Mode
This requires its own blog post. I added orgmode here only to highlight that it is a very important reason for me to stick with GNU Emacs. Dare I say, like Rails is to Ruby, Gradle is to Groovy, Orgmode is to GNU Emacs?

## Packages that I want to learn / use:
These are other interesting and useful packages that I haven't accommodated in my workflow because I don't use GNU Emacs in anger.

### [Tab Bar](https://www.gnu.org/software/emacs/manual/html_node/emacs/Tab-Bars.html)
Tab bars are cool. They can even have the emacs menu bar in a single icon at the beginning of the tab bar so one doesn't have to do [this](#menu-bar-mode-on-gui-editor).

### [embark](https://github.com/oantolin/embark)
Embark stands for Emacs MiniBuffer Actions Rooted in Keymaps. To be honest, this one takes some getting used to. I am switching from visual IDEs such as JetBrains and VS Code and I am tuned to think in a certain way about code editing. Embark forces me to unlearn and relearn such ways of working.

### Others
Origami, Perspective, Hydra, Treemacs, ...

## Full Configuration
[https://github.com/balaji/config_files/blob/master/emacs/](https://github.com/balaji/config_files/blob/master/emacs/)
<br/>
<br/>
<br/>

