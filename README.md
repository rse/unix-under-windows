
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

1. **Ensure Windows 10 Professional/Enterprise**:<br/>
   Ensure you are running Windows 10 Professional or Windows 10 Enterprise.<br/>
   Rationale: Windows Subsystem for Linux (WSL) is available only under those Windows editions only.

    - <kbd>WIN+r</kbd>
    - `winver`

   If you have a different edition, you are out of luck with WSL. Sorry, then
   you have to went with [MSYS2](https://www.msys2.org/) or [Cygwin](https://www.cygwin.com/).

2. **Ensure Windows 10 Version 1809**<br/>
   Ensure you are running at least Windows 10 Version 1809 (October 2018) or higher.<br/>
   Rationale: Windows Subsystem for Linux (WSL) is available in a reasonable way since this version only.

    - <kbd>WIN+r</kbd>
    - `winver`

   If you have an older version, upgrade with one of the following two options:

   - *START* > "Windows Update Settings" <kbd>RETURN<kbd>
   - [Windows 10 Update Assistant](https://www.microsoft.com/software-download/windows10) for feature upgrades

## Install Ubuntu GNU/Linux

## Install MinTTY/WSLTTY

## Install Unix Dotfiles Shell Environment

## Install Docker Desktop for Windows

