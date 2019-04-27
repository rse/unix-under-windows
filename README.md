
Author: [Dr. Ralf S. Engelschall](mailto:rse@engelschall.com)<br/>
Version 2.0.0 (2019-04-27)

# Unix Environment under Windows

> This is a summary on how to setup a reasonable Unix development environment under
> [*Microsoft Windows 10*](https://windows.com) with the help of the
> native [*Windows Subsystem for Linux (WSL)*](blogs.msdn.microsoft.com/wsl/),
> the [*Ubuntu*](https://www.ubuntu.com/) GNU/Linux distribution,
> Ralf S. Engelschall's [*Unix dotfiles*](https://github.com/rse/dotfiles) shell environment,
> the terminal emulator [*MinTTY/WSLTTY*](https://github.com/mintty/wsltty) and
> the [*Docker Desktop for Windows*](https://www.docker.com/products/docker-desktop)
> container execution platform.

## Install Windows Subsystem for Linux (WSL)

1. **Ensure Windows 10 Professional/Enterprise, 64 Bit, Version &ge; 18.09**:<br/>
   Ensure you are running Windows 10 Professional or Windows 10 Enterprise
   in 64-bit mode and in Version 1809 (October 2018) or newer.<br/>
   *Rationale*: Windows Subsystem for Linux (WSL) is available only
   under those Windows editions, only under 64-bit and in a reasonable
   fashion only under at least this version or newer.

   - *START* &rarr; *Settings* &rarr; *System* &rarr; *About*/*Info*:
   - ... *Device Specifications* &rarr; *System type*
   - ... *Windows Specifications* &rarr; *Edition*
   - ... *Windows Specifications* &rarr; *Version*

   If you have a different edition or you are running in 32-bit mode,
   you are out of luck with WSL. Sorry, then you have to went with
   [MSYS2](https://www.msys2.org/) or [Cygwin](https://www.cygwin.com/).
   If you just still have an older Windows version, upgrade with one of the following
   two options:

   - Windows Updates: *START* &rarr; `windows update settings` <kbd>RETURN</kbd>
   - Windows Upgrades: [Windows 10 Update Assistant](https://www.microsoft.com/software-download/windows10)

2. **Enable Windows Subsystem for Linux**:<br/>
   Enable the Windows feature *Windows Subsystem for Linux*.

   - *START* &rarr; *Settings* &rarr; *Updates & Security* &rarr; *For developers* &rarr; *Developer mode*
   - <kbd>WIN+r</kbd> &rarr; `powershell` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Run as administrator*
   - `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`

## Install Ubuntu GNU/Linux

1. **Install Ubuntu 18.09 LTS**:<br/>
   Install Ubuntu GNU/Linix 18.09 LTS from the Windows Store.

## Install MinTTY/WSLTTY

1. **Install DejaVu Sans Mono font**:<br/>
   Install
   Rationale: a perfect monospaced font should be used in the terminal emulator.

   - [DejaVu Sans](https://dejavu-fonts.github.io/Download.html) &rarr; `dejavu-fonts-ttf-*.zip`
   - `ttf/` &arr; `*.ttf` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Install*

2. **Install MinTTY/WSLTTY**:<br/>
   Install the WSLTTY variant of MinTTY.
   Rationale: a reasonable terminal emulator has to be used and the default WSL console is not good enough.

   - [WSLTTY &ge; 3.0.0](https://github.com/mintty/wsltty/releases) &rarr; `wsltty-*-install.exe`

3. **Install MinTTY/WSLTTY Configuration**:<br/>
   Install a reasonable MinTTY/WSLTTY configuration.
   Rationale: colors and fonts should be used.
   
   - Download: [config](https://raw.githubusercontent.com/rse/mintty-config/master/config)
   - Move to: `%APPDATA%/wsltty/config`

## Configure Unix Shell Environment

1. Homedir

1. Dotfiles Shell Environment

## Install Docker Desktop for Windows

-	Install bash vim tmux vifm
-	Install FZF
-	Install Docker for Windows
-	Install docker and docker-compose for Ubuntu and kubectl and helm
-	Install wsl-open
-	Install weasel-pageant
-	Install PuTTY pageant
-	apt-get install netcat socat subversion git less

