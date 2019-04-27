
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

   - *START* &rarr; *Settings* &rarr; *System* &rarr; *About*:
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
   Enable the Windows feature *Windows Subsystem for Linux*.<br/>
   Rationale: Windows Subsystem for Linux is the core feature we want to use.

   - *START* &rarr; *Settings* &rarr; *Updates & Security* &rarr; *For developers* &rarr; *Developer mode*
   - <kbd>WIN+r</kbd> &rarr; `powershell` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Run as administrator*
   - `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`

## Install Ubuntu GNU/Linux

1. **Install Ubuntu 18.09 LTS**:<br/>
   Install Ubuntu GNU/Linix 18.09 LTS from the Windows Store.<br/>
   Rationale

## Install MinTTY/WSLTTY

1. **Install DejaVu Sans Mono font**:<br/>
   Install...<br/>
   Rationale: a perfect monospaced font should be used in the terminal emulator.

   - [DejaVu Sans](https://dejavu-fonts.github.io/Download.html) &rarr; `dejavu-fonts-ttf-*.zip`
   - `ttf/` &arr; `*.ttf` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Install*

2. **Install MinTTY/WSLTTY**:<br/>
   Install the WSLTTY variant of MinTTY.<br/>
   Rationale: a reasonable terminal emulator has to be used and the default WSL console is not good enough.

   - [WSLTTY version &ge; 3.0.0](https://github.com/mintty/wsltty/releases) &rarr; `wsltty-*-install.exe`

3. **Install MinTTY/WSLTTY Configuration**:<br/>
   Install a reasonable MinTTY/WSLTTY configuration.<br/>
   Rationale: colors and fonts should be used.
   
   - Download: [config](https://raw.githubusercontent.com/rse/mintty-config/master/config)
   - Move to: `%APPDATA%/wsltty/config`

## Configure Unix Shell Environment

1. **Enable Convenient Root Access**:<br/>
   Ensure no password is needed for root access.<br/>
   Rationale: just convenience only -- feel free to ignore.

    - `sudo vi /etc/sudoers`<br/>
      &larr; `%sudo ALL=(ALL:ALL) ALL`<br/>
      &rarr; `%sudo ALL=(ALL:ALL) NOPASSWD: ALL`

2. **Use Combined Home Directory**:<br/>
   Map the Unix home directory to the regular Windows home directory.<br/>
   Rationale: just convenience only -- feel free to ignore.

    - `sudo usermod -d /mnt/c/Users $LOGNAME`

3. **Mount Windows directories in WSL with Meta-Data enabled**:<br/>
   Configure the mounting of Windows directories in WSL (`/mnt/c`) with *Meta-Data* enabled.<br/>
   Rationale: allow POSIX file permissions on Windows drives from within WSL.
   
    - `sudo vi /etc/wsl.conf`<br/>
      &rarr; ```[automount]```<br/>
      rarr; ```options = "metadata"```

3. **Install Essential Unix Tools**:<br/>

   - `apt-get install bash less vim vifm vifm tmux git subversion curl socat`

   - Install FZF

4. **Install Unix Shell Configurations**:<br/>

   - `curl -skLO https://github.com/rse/dotfiles/archive/master.zip`
   - `tar zxf master.zip`
   - `cd dotfiles-master`
   - `make install`
   - `dotfiles -f ~`

~/.dotfiles.d/gitconfig

[user]
    user  = <username>
    name  = <firstname> <lastname>
    email = <firstname>.<lastname>@<domain>

## Configure SSH Usage

1. **Install PuTTY Agent**<br/>

-	Install PuTTY pageant

1. **Install PuTTY Agent**<br/>

-	Install weasel-pageant

`echo "eval \$(~/AppData/Roaming/weasel-pageant/weasel-pageant -r -s)" >>~/.dotfiles.d/bashrc`

1. **Install WSL Utilities**:<br/>

    - Install wsl-open

        `echo \"PATH=\$PATH:/mnt/c/Windows/System32/WindowsPowerShell/v1.0/" >>~/.dotfiles.d/bashrc`
        `echo "alias open=wsl-open" >>~/.dotfiles.d/bashrc`

## Install Docker Desktop for Windows

- Ensure PULSE Client &ge; 5.3.5
- Install Docker for Windows
- Install docker and docker-compose for Ubuntu and kubectl and helm

`echo "export DOCKER_HOST=tcp://localhost:2375" >>~/.dotfiles.d/bashrc`

## Install Node.js

## Install Java

## Install zScaler Certificates

update-ca-certificates

