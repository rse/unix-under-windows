
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

   If you want to perform a fresh Windows 10 installation, start over with Microsoft's
   [Download Windows 10 Disc Image (ISO File)](https://www.microsoft.com/en-us/software-download/windows10ISO/).

2. **Enable Windows Subsystem for Linux**:<br/>
   Enable the Windows feature *Windows Subsystem for Linux*.<br/>
   Rationale: Windows Subsystem for Linux is the core feature we want to use.

   - <kbd>WIN+r</kbd> &rarr; `powershell` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Run as administrator*
   - `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`

   Alternatively:

   - *START* &rarr; `control panel` &rarr; *Programs* &rarr; *Turn Windows features on or off* &rarr; *Windows-Subsystem for Linux*

   (Let the system reboot)

## Install Ubuntu GNU/Linux

1. **Install Ubuntu 18.09 LTS**:<br/>
   Install Ubuntu GNU/Linux 18.09 LTS from the Windows Store.<br/>
   Rationale: you need a reasonable GNU/Linux distribution.

   - *START* &rarr; `microsoft store` &rarr; *Search* &rarr; `ubuntu`

   In case Microsoft Store is not available on your system, the reason can be that
   you still have User Account Control (UAC) disabled and/or you are still not logged in with
   a Microsoft Account. Anyway, fix the problems the following way first:

   - *START* &rarr; *Settings* &rarr; *Updates & Security* &rarr; *Troubleshoot* &rarr; *Windows Store Apps* &rarr; *Run the troubleshooter*

## Install MinTTY/WSLTTY

1. **Install DejaVu Sans Mono font**:<br/>
   Install a perfect monospaced font for the terminal emulator.<br/>
   Rationale: MinTTY/WSLTTY configuration requires it.

   - download: [DejaVu Sans](https://dejavu-fonts.github.io/Download.html) &rarr; `dejavu-fonts-ttf-*.zip`
   - extract: `dejavu-fonts-ttf-*.zip`
   - install: `ttf/` &arr; `*.ttf` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Install*

2. **Install MinTTY/WSLTTY**:<br/>
   Install the WSLTTY variant of MinTTY.<br/>
   Rationale: a reasonable terminal emulator has to be used and the default WSL console is not good enough.

   - [WSLTTY version &ge; 3.0.0](https://github.com/mintty/wsltty/releases) &rarr; `wsltty-*-install.exe`

3. **Install MinTTY/WSLTTY Configuration**:<br/>
   Install a reasonable MinTTY/WSLTTY configuration.<br/>
   Rationale: colors and fonts should be used.
   
   - download: [config](https://raw.githubusercontent.com/rse/mintty-config/master/config)
   - <kbd>WIN+e</kbd> &rarr; `%APPDATA%\wsltty` <kbd>RETURN</kbd>
   - override: `config`

## Configure Unix Shell Environment

1. **Enter Ubuntu under WSL**:<br/>
   Enter Ubuntu GNU/Linux under Windows Subsystem for Linux.<br/>
   Rationale: we have to configure the Unix environment from itself.
    
    - *START* &rarr; `ubuntu` <kbd>RETURN</kbd>

   When asked for your password, enter an existing or new one, but remember it (at least once until next step)!

2. **Enable Convenient Root Access**:<br/>
   Ensure no password is needed for subsequent root access.<br/>
   Rationale: just convenience only -- feel free to ignore.

    - `sudo vi /etc/sudoers`<br/>
      &larr; `%sudo ALL=(ALL:ALL) ALL`<br/>
      &rarr; `%sudo ALL=(ALL:ALL) NOPASSWD: ALL`

3. **Use Combined Home Directory**:<br/>
   Map the Unix home directory to the regular Windows home directory.<br/>
   Rationale: just convenience only -- feel free to ignore.

    - `sudo usermod -d /mnt/c/Users $LOGNAME`

4. **Mount Windows directories in WSL with Meta-Data enabled**:<br/>
   Configure the mounting of Windows directories in WSL (`/mnt/c`) with *Meta-Data* enabled.<br/>
   Rationale: allow POSIX file permissions on Windows drives from within WSL.
   
    - `sudo vi /etc/wsl.conf`<br/>
      &rarr; `[automount]`<br/>
      &rarr; `options = "metadata"`

5. **Upgrade Ubuntu Operating System**:<br/>
   Upgrade to the latest package versions of Ubunu.<br/>
   Rationale: you always want the latest updates.

   - `sudo apt-get update`
   - `sudo apt-get upgrade`

5. **Install Essential Unix Tools**:<br/>
   Install all necessary essential and some more useful Unix tools.<br/>
   Rationale: the subsequent Unix Shell Configurations are based on them.

   First, the tools available via standard package manager:

   - `sudo apt-get install bash less vim vifm vifm tmux git`
   - `sudo apt-get install subversion curl socat`

   Second, the tool ([FZF](https://github.com/junegunn/fzf)) not available
   (at least not in latest version) via standard package manager:

   - `curl -skLO https://github.com/junegunn/fzf-bin/releases/download/0.18.0/fzf-0.18.0-linux_amd64.tgz`
   - `tar zxf fzf-0.18.0-linux_amd64.tgz`
   - `sudo mv fzf /usr/local/bin/`
   - `rm fzf-0.18.0-linux_amd64.tgz`

6. **Install Unix Shell Configurations**:<br/>
   Install Ralf S. Engelschall's essential Unix dotfiles.<br/>
   Rationale: you really want a reasonable pre-configured Unix shell environment.

   - `curl -skLO https://github.com/rse/dotfiles/archive/master.zip`
   - `tar zxf master.zip`
   - `(cd dotfiles-master && sudo make install)`
   - `rm -f master.zip`
   - `dotfiles -f ~`

7. **Extend Unix Shell Configurations**:<br/>
   Extend the Unix shell configuration with your personal information.<br/>
   Rationale: these informations are individual.

   - `vi ~/.dotfiles.d/gitconfig`<br/>
     &rarr; `[user]`<br/>
     &rarr; &nbsp;&nbsp;`user  = <username>`<br/>
     &rarr; &nbsp;&nbsp;`name  = <firstname> <lastname>`<br/>
     &rarr; &nbsp;&nbsp;`email = <firstname>.<lastname>@<domain>`<br/>

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

