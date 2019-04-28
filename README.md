
<img src="https://assets.ubuntu.com/v1/29985a98-ubuntu-logo32.png" width="120" align="right" alt=""/>
<img src="https://upload.wikimedia.org/wikipedia/commons/5/5f/Windows_logo_-_2012.svg" width="140" align="right" alt=""/>

Author: [Dr. Ralf S. Engelschall](mailto:rse@engelschall.com)<br/>
Version: 2.0.0 (2019-04-27)

# Unix Environment under Windows

> This is a documentation on how to setup a reasonable but opinionated Unix development environment under
> [*Microsoft Windows 10*](https://windows.com) (Pro edition, 64-bit mode, version &ge; 1809) with the help of the
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

   Notice:

   > If you have a different edition or you are running in 32-bit mode,
   > you are out of luck with WSL. Sorry, then you have to went with
   > [MSYS2](https://www.msys2.org/) or [Cygwin](https://www.cygwin.com/).
   > If you want to perform a fresh Windows 10 installation, start over with Microsoft's
   > [Download Windows 10 Disc Image (ISO File)](https://www.microsoft.com/en-us/software-download/windows10ISO/).
   > If you just still have an older Windows version, upgrade with one of the following
   > two options:
   >
   > - Windows Updates: *START* &rarr; `windows update settings` <kbd>RETURN</kbd>
   > - Windows Upgrades: [Windows 10 Update Assistant](https://www.microsoft.com/software-download/windows10)

2. **Enable Windows Subsystem for Linux**:<br/>
   Enable the Windows feature *Windows Subsystem for Linux*.<br/>
   Rationale: Windows Subsystem for Linux is the core feature we want to use.

   - <kbd>WIN+r</kbd> &rarr; `powershell` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Run as administrator*
   - `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`

   Alternatively:

   - *START* &rarr; `control panel` &rarr; *Programs* &rarr; *Turn Windows features on or off* &rarr; *Windows-Subsystem for Linux*

   Notice:

   > In case you have trouble to enable *Windows Subsystem for Linux*, this can have many reasons.
   > In most cases, the reason is that point (1) above is not exactly fulfilled. Start over there again.

## Install Ubuntu GNU/Linux Operating System

1. **Install Ubuntu 18.04 LTS**:<br/>
   Install Ubuntu GNU/Linux 18.04 Long Term Support (LTS) from the Microsoft Store.<br/>
   Rationale: you need a reasonable GNU/Linux distribution.

   - *START* &rarr; `microsoft store` &rarr; *Search* &rarr; `ubuntu 18.04 lts` &rarr; *Get* &rarr; *Launch*

   Just be patient on first launch, it really takes time.
   Then, when asked for your username, enter the same as your Windows username.
   When asked for your password, enter either your Windows password or another one, but
   remember it (at least once until next step)!

   Notice:
   
   > In case the Microsoft Store is not available on your system, the reason
   > can be that you still have User Account Control (UAC) disabled, or
   > you are still not signed in with a Microsoft Account (although it
   > should be not required), or your Windows is still not activated or
   > you are still running an evaluation version of Windows. Anyway, try
   > to fix the problems the following way:
   > 
   > - *START* &rarr; *Settings* &rarr; *Updates & Security* &rarr; *Troubleshoot* &rarr; *Windows Store Apps* &rarr; *Run the troubleshooter*

## Setup Ubuntu GNU/Linux Operating System

1. **Enter Ubuntu under WSL**:<br/>
   Enter Ubuntu GNU/Linux under Windows Subsystem for Linux.<br/>
   Rationale: we have to setup Ubuntu from itself.
    
    - *START* &rarr; *Ubuntu 18.04*

2. **Enable Convenient Root Access**:<br/>
   Ensure no password is needed for subsequent root access.<br/>
   Rationale: just convenience only -- feel free to ignore.

    - `sudo vi /etc/sudoers`<br/>
      &larr; `%sudo ALL=(ALL:ALL) ALL`<br/>
      &rarr; `%sudo ALL=(ALL:ALL) NOPASSWD: ALL`

3. **Upgrade Ubuntu Operating System**:<br/>
   Upgrade to the latest package versions of Ubuntu.<br/>
   Rationale: you always want the latest updates.

   - `sudo apt-get update`
   - `sudo apt-get upgrade`

4. **Mount Windows directories in WSL with Meta-Data enabled**:<br/>
   Configure the mounting of Windows directories in WSL (`/mnt/c`) with *Meta-Data* enabled.<br/>
   Rationale: allow POSIX file permissions on Windows drives from within WSL.
   
    - `sudo vi /etc/wsl.conf`<br/>
      &rarr; `[automount]`<br/>
      &rarr; `options = "metadata"`

5. **Use Combined Home Directory**:<br/>
   Map the Unix home directory to the regular Windows home directory.<br/>
   Rationale: just convenience only -- feel free to ignore.

    - `exec sudo usermod -d /mnt/c/Users/$USER $USER`

   Notice:
   
   > Don't panic: We have to `exec` the command here to release the current user's processes
   > and this way let `usermod` proceeed. This will immediately close the Unix terminal.

## Configure Unix Shell Environment

1. **Re-Enter Ubuntu under WSL**:<br/>
   Re-Enter Ubuntu GNU/Linux under Windows Subsystem for Linux again.<br/>
   Rationale: we have to configure the Unix environment from itself.
    
    - *START* &rarr; *Ubuntu 18.04*

2. **Install Essential Unix Tools**:<br/>
   Install all necessary essential and some more useful Unix tools.<br/>
   Rationale: the subsequent Unix Shell Configurations are based on them.

   First, the tools available via standard package manager:

   - `sudo apt-get install bash less vim vifm vifm tmux git`
   - `sudo apt-get install subversion curl socat`
   - `sudo apt-get install build-tools`

   Second, the tool ([FZF](https://github.com/junegunn/fzf)) not available
   (at least not in latest version) via standard package manager:

   - `curl -skLO https://github.com/junegunn/fzf-bin/releases/download/0.18.0/fzf-0.18.0-linux_amd64.tgz`
   - `tar zxf fzf-0.18.0-linux_amd64.tgz`
   - `sudo mv fzf /usr/local/bin/`
   - `rm fzf-0.18.0-linux_amd64.tgz`

3. **Install Unix Shell Configurations**:<br/>
   Install Ralf S. Engelschall's essential Unix dotfiles.<br/>
   Rationale: you really want a reasonable pre-configured Unix shell environment.

   - `curl -skLO https://github.com/rse/dotfiles/archive/master.zip`
   - `tar zxf master.zip`
   - `(cd dotfiles-master && sudo make install)`
   - `rm -f master.zip`
   - `dotfiles -f ~`

4. **Extend Unix Shell Configurations**:<br/>
   Extend the Unix shell configuration with your personal information.<br/>
   Rationale: these informations are individual.

   - `vi ~/.dotfiles.d/gitconfig`<br/>
     &rarr; `[user]`<br/>
     &rarr; `user  = <username>`<br/>
     &rarr; `name  = <firstname> <lastname>`<br/>
     &rarr; `email = <firstname>.<lastname>@<domain>`<br/>

6. **Install WSL Utilities**:<br/>
   Install additional WSL utilities.<br/>
   Rationale: you want additional features inside WSL.

   - `sudo apt-get install ubuntu-wsl wslu`
   - `curl -skLO https://github.com/4U6U57/wsl-open/archive/master.zip`
   - `tar zxf master.zip`
   - `sudo install -c -m 755 wsl-open/wsl-open.sh /usr/local/bin/wsl-open`
   - `sudo install -c -m 644 wsl-open/wsl-open.1 /usr/local/man/man1/`
   - `rm master.zip`
   - `vi ~/.dotfiles.d/bashrc`<br/>
      &rarr; `PATH=$PATH:/mnt/c/Windows/System32/WindowsPowerShell/v1.0/`<br/>
      &rarr; `alias open=wsl-open`

## Install MinTTY/WSLTTY Terminal Emulator

1. **Install MinTTY/WSLTTY**:<br/>
   Install the WSLTTY variant of MinTTY.<br/>
   Rationale: a reasonable terminal emulator has to be used and the default WSL console is not good enough.

   - [WSLTTY version &ge; 3.0.0](https://github.com/mintty/wsltty/releases) &rarr; `wsltty-*-install.exe`

2. **Install MinTTY/WSLTTY Configuration**:<br/>
   Install a reasonable MinTTY/WSLTTY configuration.<br/>
   Rationale: colors and fonts should be used.
   
   - download: [config](https://raw.githubusercontent.com/rse/mintty-config/master/config)
   - <kbd>WIN+e</kbd> &rarr; `%APPDATA%\wsltty` <kbd>RETURN</kbd>
   - override: `config`

3. **Install DejaVu Sans Mono font**:<br/>
   Install a perfect monospaced font for the terminal emulator.<br/>
   Rationale: MinTTY/WSLTTY configuration above references it.

   - download: [DejaVu Sans](https://dejavu-fonts.github.io/Download.html) &rarr; `dejavu-fonts-ttf-*.zip`
   - extract: `dejavu-fonts-ttf-*.zip`
   - install: `ttf/` &arr; `*.ttf` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Install*

## Install Secure-Shell (SSH) Environment

1. **Install PuTTY**:<br/>
   Install the PuTTY SSH client.<br/>
   Rationale: you want to run the PuTTY Agent (`pageant`).

   - [PuTTY Downloads](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) &rarr; `putty-*-installer.msi`

2. **Generate SSH Key**:<br/>
   Generate (or use existing) SSH key.<br/>
   Rationale: you don't want to use passwords.

   - *START* &rarr; `puttygen` <kbd>RETURN</kbd>

   - Convert to standard format.

3. **Autostart PuTTY Agent**:<br/>
   Enable the PuTTY Agent to autostart on login.<br/>
   Rationale: you want it to be running all the time.

   - FIXME
   - `pageant.exe <ppk-file>`

5. **Install PuTTY Agent Client (Weasel-Pageant)**<br/>
   Install Weasel-Pageant for accessing the PuTTY Agent from within WSL.<br/>
   Rationale: you want to directly access PuTTY Agent from within WSL.
 
   - [Weasel-Pageant Downloads](https://github.com/vuori/weasel-pageant/releases) &rarr; `weasel-pageant-1.3.zip`
   - `weasel-pageant-1.3.zip` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; Extract
   - move `weasel-pageant-1.3` to `%APPDATA%\weasel-pageant`

4. **Enter Ubuntu under WSL via MinTTY/WSLTTY**:<br/>
   Enter Ubuntu GNU/Linux under Windows Subsystem for Linux again (this time via MinTTY/WSLTTY).<br/>
   Rationale: we have to configure also the Unix of SSH.
    
    - *START* &rarr; `wsltty` <kbd>RETURN</kbd>

5. **Configure Unix Environment for SSH Agent**:<br/>
   Configure the Unix environment to use the Weasel-Pageant as the SSH agent.<br/>
   Rationale: in every WSL terminal you want SSH agent access available automatically.

   - `vi ~/.dotfiles.d/bashrc`<br/>
     &rarr; `eval $(~/AppData/Roaming/weasel-pageant/weasel-pageant -r -s)`

## Install Docker Desktop for Windows

- Ensure PULSE Client &ge; 5.3.5
- Install Docker for Windows
   - *START* &rarr; `control panel` &rarr; *Programs* &rarr; *Turn Windows features on or off* &rarr; *Hyper-V*
- Install docker and docker-compose for Ubuntu and kubectl and helm

`echo "export DOCKER_HOST=tcp://localhost:2375" >>~/.dotfiles.d/bashrc`

## Install Node.js

## Install Java

## Install zScaler Certificates

update-ca-certificates

