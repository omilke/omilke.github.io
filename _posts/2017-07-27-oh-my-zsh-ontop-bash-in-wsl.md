---
layout: post
title:  "Effectively using oh-my-zsh in Windows Subsystem for Linux (WSL)"
date:   2017-07-27 10:00:00
categories: practical, tools
tags: [Maven, IntelliJ, Windows, Docker]
description: This article is a describes setting up oh-my-zsh in WSL and re-use existing windows configurations.
author: olli
excerpt_separator: <!--more-->
comments: true
disqus_id: 18
---

Lately I have been working a team, in which my fellow devs mostly use Linux while I decided to stay with Windows. However, whenever I can take a look on their shells, during pair programming for instance, I get a little envious of them, since they use oh-my-zsh in different flavors. 

## Oh my zsh

Oh my zsh is an extension on top of Linux' zsh, which provides some really neat features, including  docker auto-completion for parameters, containers and images, mvn auto completion, integrated git status, shell theming and so much more,  that you should definitely check it out. 

By the way, [Jekyll](https://jekyllrb.com/) will also work out of the box under WSL ;-)

<!--more-->

### Oh my zsh on Windows

I have long used cygwin under Windows, but updating and installing new applications were  uncomfortable.  But nevertheless  it integrated pretty well into my workflow with "cygwin here" in Windows Explorer.

Recently I discovered [babun](http://babun.github.io/), which in theory sounded awesome to me. Built-in package manager and a preconfigured oh-my-zsh as default shell. However, the oh-my-zsh plugins never really worked for me and being somewhat disappointed, I uninstalled it again.

But then I learned about Windows Subsystem for Linux (WSL) and actually wanted to try it *Bash on Ubuntu on Windows*

### Installation Guide

In order to fully re-use my setup, you'll need Windows 10 Creators Update, which among others enables you to execute regular Windows binaries from within bash.

Here's what I found to be most helpful:

* [Updating to Windows 10 Creators Update](https://www.microsoft.com/en-us/windows/features)
* [Installing WSL](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide)
* install zsh, git and curl (if not installed)

        sudo apt install git zsh curl
       
 * [Install oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)

        sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

* Use nicer fonts, like [Hack](http://sourcefoundry.org/hack/) or [FiraCode](https://github.com/tonsky/FiraCode)
   
    The former comes with an installer that enables you to choose *Hack* even in the default Windows terminal, the latter unfortunately not.

* Use [wsl-terminal](https://github.com/goreliu/wsl-terminal)

The last step actually is optional with respect to functionality. However, many oh-my-zsh themes use characters that cannot be displayed in the default Windows terminal application. Even though you can change the font of the default terminal in the settings of the opened terminal, you can still only select fonts that are properly recognized as monospace fonts by Windows. Unfortunately, [all fonts I tried](https://github.com/powerline/fonts) were not, except for *Hack* as mentioned above. Besides wsl-terminal with *solarized-dark* theme looks awesome.

### Now what?

Having followed all the steps above, you'll have a working oh-my-zsh in your Bash on Ubuntu on Windows and a clean terminal application that is capable of rendering properly.

When navigating to a git repository in a WSL shell you might notice *git status* behaves differently from what your git-in-windows might tell you. And rightly so, because WSL has its own git alongside its own global / system settings. 

Frankly, I absolutely disliked the idea of re-installing and re-configuring every tool I wanted to use under WSL.

## Re-using the Windows configuration

My dev systems were setup the way I wanted them and I was obsessed with the thought that there has to be a way to re-use it. Starting with Windows 10 Creator's Update, you can naturally execute Windows programs from within WSL... 

The Windows *%PATH%* is inherited to WSL, which is quite practical, and you can even use aliases in *~/.zshrc*. Sadly, these will not work for oh-my-zsh auto completion.

### Backup your current git

At this point we already have a git installed in WSL and unfortunately the oh-my-zsh setup and update does not work with the faked executable. I decided to make a backup of the original Linux git and preserve it for later zsh updates.

    git --version
    sudo mvn /usr/bin/git /usr/bin/git.original

### Faking executables in WSL

You can simply fake an executable with a symbolic link placed in */usr/bin/* and pointing to the appropriate Windows executable.

    sudo -i
    cd /usr/bin/
    ln -s /mnt/c/path/to/your/git.exe
    git --version

This worked for git, docker and docker-compose but not for Maven, since mvn under Windows actually is *mvn.cmd*. In order to get Maven up and running I needed to run *cmd.exe* with the path to *mvn.cmd*

    ### mvn-wrapper.sh
    #!/bin/bash

    #re-use specific mvn 
    #mvn_cmd="C:\Program Files\JetBrains\IntelliJ IDEA Community Edition 2017.1.1\plugins\maven\lib\maven3\bin\mvn.cmd"

    #...or just the one from the %PATH%
    mvn_cmd="mvn.cmd"

    params=""
    for var in "$@"
        do
            params="$params $var"
    done

    #simply delegate all own parameters to mvn.cmd
    /mnt/c/Windows/System32/cmd.exe /C "$mvn_cmd" $params


 Here is a list of my "faked" programs:

    ll /usr/bin/ | grep mnt
    lrwxrwxrwx 1 root   root          53 Jul 31 17:08 code -> /mnt/c/Program Files (x86)/Microsoft VS Code/Code.exe*
    lrwxrwxrwx 1 root   root          59 Jul 11 16:25 docker -> /mnt/c/Program Files/Docker/Docker/resources/bin/docker.exe*
    lrwxrwxrwx 1 root   root          36 Jul 11 16:04 git -> /mnt/c/Program Files/Git/cmd/git.exe*
    lrwxrwxrwx 1 root   root          45 Jul 24 08:50 mvn -> /mnt/c/Users/omilke/[data]/app/mvn-wrapper.sh*
    lrwxrwxrwx 1 root   root          52 Jul 18 11:14 mvnexit -> /mnt/c/Program Files/apache-maven-3.5.0/bin/mvn-exec
   
 
Now you can enable the plugins in oh-my-zsh you need: 

    nano ~/.zshrc

    plugins=(git mvn docker)

### oh-my-zsh in IntelliJ IDEA

IntelliJ has a neat configuration option to specify which terminal application to use
   
    #in IntelliJ
    [STRG] + [ALT] + S
   
     #Tools | Terminal | Shell Path
    "C:\Windows\System32\bash.exe"

IntelliJ by default uses the theme font for displaying characters in the terminal, hence you might want to change that font, as well

    #in IntelliJ
    [STRG] + [ALT] + S
   
     #Editor | Color Scheme | Console Font
    Sets only the font for the console
   
     #Editor | Font
    Globally sets the font, including the console (except when already exclicitly set)         

   
## Caveat

Docker-Compose behaved a little strange. I had a Dockerfile using npm that worked out of the box with docker, but didn't work when using the same Dockerfile via *docker-compose.yml* because there was an issue with a file path. I could solve this problem with [explicitly activating long file paths](http://mspoweruser.com/ntfs-260-character-windows-10/). However, this only worked with docker-compose invoked from *Windows Power Shell* or *cmd*. It didn't work when *faked* in WSL and neither did the oh-my-zsh plugin for docker-compose.

As mentioned earlier, updating oh-my-zsh does not work with the faked git from Windows. Updating now requires restoring the original executable, performing the update and *re-faking* git.

    ###update-omz.sh
    #!/bin/bash
   
    git_executable=/usr/bin/git
    git_faked=/usr/bin/git.faked
    git_original=/usr/bin/git.original
   
    #request root, staying $user
   
    #revert git
    sudo mv ${git_executable} ${git_faked}
    sudo mv ${git_original} ${git_executable}
   
    #perform the update, as given by "which upgrade_oh_my_zsh" 
    env ZSH=$ZSH sh $ZSH/tools/upgrade.sh
   
    #re-fake
    sudo mv ${git_executable} ${git_original}
    sudo mv ${git_faked} ${git_executable}
   
 Additionally I use an alias in ~/.zshrc

    alias update-omz="/mnt/c/Users/omilke/\[data\]/app/update-omz.sh"
   
 WSL currently does not properly support networking tools like ngrep or tcpdump. You can however track progress on these at [WSL github issue](https://github.com/Microsoft/BashOnWindows/issues/1628) and [vote for it](https://wpdev.uservoice.com/forums/266908-command-prompt-console-bash-on-ubuntu-on-windo/suggestions/17817685-support-af-packet-address-family-tcpdump-wireshar). 
 
 While you can generally use TCP Ports, binding them to *0.0.0.0* in order to make them reachable not only via loopback does not work. It won't fail, it will just silently be bound to 127.0.0.1.

The wrapper I wrote for invoking mvn from bash might fail if quoting is required. Currently quotation marks are swallowed by bash.

Executing shell scripts requires these scripts to only contain Linux line breaks.

## Conclusion

I like that I can ditch my VM I used for reviewing my blog articles in Jekyll and *mvn* CLI works in IntelliJ's terminal.

While I originally thought WSL might be nice toy around with, it has replaced cygwin on my setup now and is a good addition to IntelliJ. SSH works out of the box and it almost feels like a real Linux.

I have prepared a [github-repo](https://github.com/omilke/wsl-utility) with the helpers I created.