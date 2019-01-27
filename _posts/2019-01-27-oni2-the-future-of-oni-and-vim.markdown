---
layout: post
title:  "Oni 2, the future of Oni and Vim?"
date:   2018-01-2 14:20:00 +0100
categories: blog vim
---

My editor of choice is [Vim](https://en.wikipedia.org/wiki/Vim_(text_editor)), but having worked with the .Net Framework in a Windows workspace for two and a half years now I still don't find it as comfortable or natural to use the console in Windows as I do in my Debian laptop. After switching around between many shells ([PowerShell](https://docs.microsoft.com/en-us/PowerShell/scripting/overview?view=PowerShell-6), [git-bash](https://gitforwindows.org/), [cygwin](http://www.cygwin.com/), [babun](http://babun.github.io/), etc.) I've settled with PowerShell and the bash shell contained in git, but neither feels right when I have to fire up Vim to edit a file.

One editor that I've been experimenting with for 3 months now is Oni, which is an electron app on top of neovim. It feels much better than using VS Code or Atom with a vim emulator. On my Debian laptop I was still using vim until today I thought that I could give Oni a try on Linux.

Heading to the GitHub page I saw an interesting disclaimer:
> NOTE: We're now shifting our focus to building Oni 2

Wait, what? A new version?! Looking at the README.md the project goals look good, a VS Code or Atom like application that has a modal editor (neovim) at it's core. Never mind that it also says it's `Pre-alpha - not yet usable!`, let's take a look at it :)

## Fetching the project

First thing to notice is that there are no releases or binaries listed on the github page. However, the installation seems simple enough: 

```bash
    git clone https://github.com/onivim/oni2
    esy install
    esy build
    esy x Oni2
```

What is [esy](https://esy.sh/)? Had no idea, their webpage states that it's a package manager, more specifically a project that provides an `Easy package management for native Reason, OCaml and more`. Fair enough, the installation is done with the Node Package Manager, and that is something that I can understand :)

You also need [git](https://git-scm.com/) installed, but that will be soon come pre-installed on every computer operating system, smart phone, tablet and toaster, right?

The final prerequisites listed for Linux are [cmake](https://cmake.org/) and [ragel](https://en.wikipedia.org/wiki/Ragel), although I didn't know ragel ([Ragel State Machine Compiler](http://www.colm.net/open-source/ragel/)) it was available in the debian package repositories.


## Building Oni 2, not as easy as It looks

Installing the dependencies with `esy install` was fine. However, `esy build` wasn't as easy as the command seems to imply.

After a while compiling stuff, one of the dependency packages threw an error coming straight from `ld` saying that I had no bz2 and png libraries available.

Fair enough, further inspection with `ld -lbz2 --verbose` and `ld -lpng --verbose` confirms I don't have those around. A quick trip to [https://packages.debian.org/](https://packages.debian.org/) tells me that packages `libbz2-dev` and `libpng-dev` will fix that.

Another round of `esy build` to find out that cmake can't find X11. Then `xrandr`, `gl.h`, `glu.h`... This was quickly degenerating to the `./configure; make; make install` of old.

In the end here's the full list of dependencies that were installed on Debian stretch:

```bash
sudo apt install cmake ragel libbz2-dev libpng-dev libx11-dev libxrandr-dev libxinerama-dev libxcursor-dev libxi-dev mesa-common-dev libgl1-mesa-dev libglu1-mesa-dev
```

I finally saw `esy build` completing successfully.

## Giving Oni 2 a go

After building, running Oni 2 was as simple as executing `esy x Oni2`.

The load time was phenomenal, right into a mostly empty editor with the famous `Hello World` content on it.
Nothing was editable or usable at all, and actually resizing the window results in a division by zero exception.

Here's the screenshot of the application:

![Oni2](/assets/img/oni2.png)

## Wrapping up

Well, they weren't joking when they said `Pre-alpha - not yet usable!`
<pre>¯\_(ツ)_/¯</pre>

At the moment, the two processes (~esy~ and ~Oni2~) use 20MB of RAM, not much until you consider that there is no functionality yet. VS Code, on the other hand, uses around 450MB or RAM to handle one tab, but it is doing a lot more (useful) stuff. At the moment there isn't much to compare, I can't even really understand if neovim is running within Oni 2 yet. So I guess we'll have to wait until the application starts providing the basics.

Ended up writing this post in VS Code with vim emulation. Go figure!

![XKCD](https://imgs.xkcd.com/comics/real_programmers.png)

[XKCD - Real Programmers](https://xkcd.com/378/)