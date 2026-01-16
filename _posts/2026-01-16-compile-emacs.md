---
layout: post
date: 2026-01-16T18:28:16+01:00
title: Installing Emacs with Tree Sitter Support in WSL2
tags:
  - blog
gist: >-
  <a href="/compile-emacs">Compiling Emacs in WSL2 with Tree Sitter Support</a> 
---
[tree-sitter](https://tree-sitter.github.io/tree-sitter/) is an incremental parsing library that helps text editors (emacs, vim) understand the structure of the code better so that they can provide better syntax highlighting and structural selection.

For some reasons none of the prebuilt Emacs distributions neither from GNU software nor from package managers come with `tree-sitter` support. This article suggests how to go about compiling one's own Emacs with Tree Sitter support.

## Setup

The commands were tested in a WSL2 + Ubuntu OS setup.

For MacOS, the configure steps will slightly differ and there will be a codesign problem with custom-built Emacs.app that should be addressed separately.

For Windows, one should use `mingw64` or `msys2` platform to setup their linux build environment. Or just use a WSL2 distro, the Emacs running via WSL2 is faster than "native" Emacs in Windows.


## Steps

1. Download the source code from [GNU FTP Server](https://ftp.gnu.org/gnu/emacs/). I find this easier than cloning from a git source.
2. Extract the contents of the zip/tar file
   ```sh
   tar xvf ~/Downloads/emacs-30.2.tar.xz
   ```
   The source code is now in a folder named `emacs-30.2`

3. Install required libraries
    ```sh
   sudo apt install \
       build-essential \ # build tools
       libtree-sitter-dev \ # tree-sitter
       libxml2 \ # XML support
       libgtk-3-dev \ # GUI support in WSL2
       librsvg2-dev \ # SVG support
       libncurses-dev \
       libgnutls28-dev \ # for secure communication
       libgccjit-13-dev \ # the 13 comes from `gcc --version`
       libsqlite3-dev # for org-roam support
   ```
4. From within the emacs-30.2 folder, run the following:
   ```sh
   ./autogen.sh
   ```

5. Now run the following
   ```sh
   ./configure \
       --prefix=/usr/local/emacs # the install location
       --with-pgtk \ # personally this creates the best borderless GUI in WSL2
       --with-native-compilation \
       --with-xml2 \
       --with-gnutls \
       --with-tree-sitter \
       --with-rsvg \
       --with-sqlite3
   ```
   Note: There are a lot more options to choose in the `configure` stage and depending on what options you choose you might have to install the necessary developer libraries. For example: You can add `--with-imagemagick` for images support. You should install developer libraries for ImageMagick first.
   
6. Build Emacs
   ```sh
   make -j16 # 16 is the number of parallel jobs, optional and customizable.
   ```

7. Install Emacs
   ```sh
   make install # depending on the location, you might have to prefix the command with `sudo`
   ```

8. And that is all! We now have a system-built Emacs with tree-sitter support (amongst others). Emacs should've gotten installed in the `--prefix` location provided during the `configure` stage.

## Desktop Shortcuts

It is nicer to have desktop shortcuts. These were taken directly from the desktop shortcuts WSL2 creates when one installs Emacs through a package manager.

### Emacs Shortcut:

  Create a "new shortcut" by rightclicking on the Windows Explorer GUI and paste the following in the "location" section:

  ```
  "C:\Program Files\WSL\wslg.exe" -d Ubuntu --cd "~" -- /usr/local/emacs/bin/emacs
  ```
  adjust paths accordingly.
  
### Emacs Client Shortcut:

The best part about this command is that it doesn't wait for an emacs daemon to be running.

```
"C:\Program Files\WSL\wslg.exe" -d Ubuntu --cd "~" -- sh -c "if [ -n \"\$*\" ]; then exec /usr/local/emacs/bin/emacsclient --alternate-editor= --reuse-frame \"\$@\"; else exec /usr/local/emacs/bin/emacsclient --alternate-editor= --create-frame; fi" sh
```
