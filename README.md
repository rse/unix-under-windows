
<img src="https://assets.ubuntu.com/v1/29985a98-ubuntu-logo32.png" width="120" align="right" alt=""/>
<img src="https://upload.wikimedia.org/wikipedia/commons/5/5f/Windows_logo_-_2012.svg" width="120" align="right" alt=""/>

Author: [Dr. Ralf S. Engelschall](mailto:rse@engelschall.com)<br/>
Version: 2.0.1 (2019-04-28)

# Unix Environment under Windows

> This is a documentation on how to setup a reasonable but opinionated Unix development environment under
> [*Microsoft Windows 10*](https://windows.com) (Pro edition, 64-bit mode, version &ge; 1809) with the help of the
> native [*Windows Subsystem for Linux (WSL)*](blogs.msdn.microsoft.com/wsl/),
> the [*Ubuntu*](https://www.ubuntu.com/) GNU/Linux distribution,
> Ralf S. Engelschall's [*Unix dotfiles*](https://github.com/rse/dotfiles) shell environment,
> the terminal emulator [*MinTTY/WSLTTY*](https://github.com/mintty/wsltty) and
> the [*Docker Desktop for Windows*](https://www.docker.com/products/docker-desktop)
> container execution platform.

![screenshot](screenshot.png)

Notice:

> In short, the crux of this setup is: `sudo` `NOPASSWD` configuration,
> `wsl.conf` based WWL configuration, combined `/c/Users/$USER` home
> directory, essential Unix "dotfiles", additional WSL utilities,
> MinTTY/WSLTTY terminal emulator, `weasel-pageant` SSH agent, and
> `DOCKER_HOST` based Docker control, etc.

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
   Upgrade to the latest package versions of Ubuntu and allow APT to use HTTPS.<br/>
   Rationale: you always want the latest updates and Docker later needs HTTPS access.

   - `sudo apt-get update`
   - `sudo apt-get upgrade -y --with-new-pkgs`
   - `sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common`

4. **Mount Windows directories in WSL with Meta-Data enabled**:<br/>
   Configure the mounting of Windows directories in WSL (`/c` instead of `/mnt/c`) and with *Meta-Data* enabled.<br/>
   Rationale: allow POSIX file permissions on Windows drives from within WSL.
   
    - `sudo vi /etc/wsl.conf`<br/>
      &rarr; `[automount]`<br/>
      &rarr; `root    = /`
      &rarr; `options = "metadata"`

5. **Use Combined Home Directory**:<br/>
   Map the Unix home directory to the regular Windows home directory.<br/>
   Rationale: just convenience only -- feel free to ignore.

    - `exec sudo usermod -d /c/Users/$USER $USER`

   Notice:
   
   > Don't panic: We have to `exec` the command here to release the current user's processes
   > and this way let `usermod` proceeed. This will immediately close the Unix terminal, of course.

## Configure Unix Shell Environment

1. **Re-Enter Ubuntu under WSL**:<br/>
   Re-Enter Ubuntu GNU/Linux under Windows Subsystem for Linux again.<br/>
   Rationale: we have to configure the Unix environment from itself.
    
    - *START* &rarr; *Ubuntu 18.04*

2. **Install Essential Unix Tools**:<br/>
   Install all necessary essential and some more useful Unix tools.<br/>
   Rationale: the subsequent Unix Shell Configurations are based on them.

   First, the tools available via standard package manager:

   - `sudo apt-get install -y bash less vim vifm tmux`
   - `sudo apt-get install -y procps lsof dnsutils tcpdump nmap`
   - `sudo apt-get install -y openssh-client stunnel subversion git curl`
   - `sudo apt-get install -y mc tree file findutils`
   - `sudo apt-get install -y rsync rdup rclone w3m lftp`
   - `sudo apt-get install -y atool gzip bzip2 xz-utils zip unzip`
   - `sudo apt-get install -y diffutils diffstat patch patchutils par`
   - `sudo apt-get install -y openssl gnupg golang-cfssl apg uuid bc`
   - `sudo apt-get install -y imagemagick ghostscript poppler-utils`

   Second, the tool ([FZF](https://github.com/junegunn/fzf)) not available
   (at least not in latest version) via standard package manager:

   - `curl -skLO https://github.com/junegunn/fzf-bin/releases/download/0.18.0/fzf-0.18.0-linux_amd64.tgz`
   - `tar zxf fzf-0.18.0-linux_amd64.tgz`
   - `sudo install -c -m 755 fzf /usr/local/bin/`
   - `rm fzf-0.18.0-linux_amd64.tgz fzf`

   Third, the tool ([Git-Town](https://www.git-town.com)) not available via standard package manager:

   - `curl -skLO https://github.com/Originate/git-town/releases/download/v7.2.0/git-town-amd64.deb`
   - `sudo dpkg -i git-town-amd64.deb`
   - `rm git-town-amd64.deb`

3. **Install Unix Shell Configurations**:<br/>
   Install Ralf S. Engelschall's essential Unix dotfiles.<br/>
   Rationale: you really want a reasonable pre-configured Unix shell environment.

   - `sudo apt-get install -y make`
   - `curl -skLO https://github.com/rse/dotfiles/archive/master.zip`
   - `unzip -x master.zip`
   - `(cd dotfiles-master && sudo make install)`
   - `rm -rf dotfiles-master`
   - `rm master.zip`
   - `dotfiles -f ~`

4. **Install Unix Shell Addon Configurations**:<br/>
   Install Ralf S. Engelschall's Bash-FZF and Bash-ENVRC addons.<br/>
   Rationale: you really want a reasonable pre-configured Unix shell environment.

   - `curl -skL https://raw.githubusercontent.com/rse/bash-fzf/master/bash-fzf.rc -o ~/.bash-fzf.rc`
   - `curl -skL https://raw.githubusercontent.com/rse/bash-envrc/master/bash-envrc.rc -o ~/.bash-envrc.rc`

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

   - `sudo apt-get install -y ubuntu-wsl wslu`
   - `curl -skLO https://github.com/4U6U57/wsl-open/archive/master.zip`
   - `unzip -x master.zip`
   - `sudo install -c -m 755 wsl-open-master/wsl-open.sh /usr/local/bin/wsl-open`
   - `sudo mkdir -p /usr/local/share/man/man1`
   - `sudo install -c -m 644 wsl-open-master/wsl-open.1 /usr/local/share/man/man1/`
   - `rm -rf wsl-open-master`
   - `rm master.zip`
   - `vi ~/.dotfiles.d/bashrc`<br/>
      &rarr; `PATH=$PATH:/c/Windows/System32/WindowsPowerShell/v1.0/`<br/>
      &rarr; `alias open=wsl-open`

## Install MinTTY/WSLTTY Terminal Emulator

1. **Install MinTTY/WSLTTY**:<br/>
   Install the WSLTTY variant of MinTTY.<br/>
   Rationale: a reasonable terminal emulator has to be used and the default WSL console is not good enough.

   - [WSLTTY version &ge; 3.0.0](https://github.com/mintty/wsltty/releases) &rarr; `wsltty-*-install.exe`

2. **Install DejaVu Sans Mono font**:<br/>
   Install a perfect monospaced font for the terminal emulator.<br/>
   Rationale: MinTTY/WSLTTY configuration above references it.

   - download: [DejaVu Sans](https://dejavu-fonts.github.io/Download.html) &rarr; `dejavu-fonts-ttf-*.zip`
   - extract: `dejavu-fonts-ttf-*.zip` &rarr; <kbd>RIGHT-CLICK</kbd> *Extract all*
   - install: `dejavu-fonts-ttf-*\ttf\` &arr; select all `*.ttf` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Install*

3. **Install MinTTY/WSLTTY Configuration**:<br/>
   Install a reasonable MinTTY/WSLTTY configuration.<br/>
   Rationale: colors and fonts should be used.
   
   - download: [MinTTY-config](https://github.com/rse/mintty-config/archive/master.zip)
   - extract: `master.zip` &rarr; <kbd>RIGHT-CLICK</kbd> *Extract all*
   - copy: `mintty-config-master\config`
   - paste: `%APPDATA%\wsltty` (override `config` file)

## Install Secure-Shell (SSH) Environment

1. **Install PuTTY**:<br/>
   Install the PuTTY SSH client.<br/>
   Rationale: you want to run the PuTTY Agent (`pageant`).

   - [PuTTY Downloads](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) &rarr; `putty-64bit-*-installer.msi`

2. **Generate SSH Key**:<br/>
   Generate (or use existing) SSH key.<br/>
   Rationale: you don't want to use passwords.

   - *START* &rarr; `puttygen` <kbd>RETURN</kbd>
   
   Save the Private Key under `c:\Users\<username>\Documents\ssh-key-prv.ppk`.<br/>
   Save the Converted Private Key under `c:\Users\<username>\Documents\ssh-key-prv.pem`.<br/>
   Save the Public Key under `c:\Users\<username>\Documents\ssh-key-pub.pem`.

3. **Autostart PuTTY Agent**:<br/>
   Enable the PuTTY Agent to autostart on login and load the SSH private key.<br/>
   Rationale: you want it to be running all the time.

   - <kbd>WIN+e</kbd> &rarr; `%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *New* &rarr; *Shortcut*
   - `"C:\Program Files\PuTTY\pageant.exe" "C:\Users\<username>\Documents\ssh-key-prv.ppk"`

5. **Install PuTTY Agent Client (Weasel-Pageant)**<br/>
   Install Weasel-Pageant for accessing the PuTTY Agent from within WSL.<br/>
   Rationale: you want to directly access PuTTY Agent from within WSL.
 
   - [Weasel-Pageant Downloads](https://github.com/vuori/weasel-pageant/releases) &rarr; `weasel-pageant-1.3.zip`
   - `weasel-pageant-1.3.zip` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Extract all*
   - move `weasel-pageant-1.3\` to `%APPDATA%\weasel-pageant\`

4. **Enter Ubuntu under WSL via MinTTY/WSLTTY**:<br/>
   Enter Ubuntu GNU/Linux under Windows Subsystem for Linux again (this time via MinTTY/WSLTTY).<br/>
   Rationale: we have to configure also the Unix of SSH.
    
    - *START* &rarr; `wsl terminal` <kbd>RETURN</kbd>

5. **Configure Unix Environment for SSH Agent**:<br/>
   Configure the Unix environment to use the Weasel-Pageant as the SSH agent.<br/>
   Rationale: in every WSL terminal you want SSH agent access available automatically.

   - `vi ~/.dotfiles.d/bashrc`<br/>
     &rarr; `eval $(~/AppData/Roaming/weasel-pageant/weasel-pageant -r -s)`

## Install Docker/Kubernetes Container Runtimes

1. **Install Docker Desktop**:<br/>
   Install the Docker Desktop for Windows (Community Edition) distribution.<br/>
   Rationale: you want Docker container engine be available for development.

   - *START* &rarr; `control panel` &rarr; *Programs* &rarr; *Turn Windows features on or off* &rarr; *Hyper-V*
   - [Docker Desktop](https://www.docker.com/products/docker-desktop) &rarr; *Download for Windows*
   - *START* &rarr; `computer management` <kbd>RIGHT-CLICK</kbd> &rarr; *Run as administrator*
   - *Computer Management* &rarr; *System Tools* &rarr; *Local Users and Groups* &rarr; *Groups* &rarr; `docker-users` &rarr; <kbd>LEFT-DOUBLE-CLICK</kbd> &rarr; *Add...*

   Notice:

   > You need a Docker Hub account for downloading and using Docker Desktop.
   > [Sign up](https://hub.docker.com/signup) first if you still don't have a Docker Hub account.

   Notice:

   > Yes, Hyper-V is necessary as Docker Desktop for Windows (in
   > contrast to the regular Docker for Linux) runs Docker inside a
   > small Linux distribution which is executed in a virtual machine via
   > Hyper-V. You have to use this separate Docker Desktop for Windows,
   > as Docker cannot be run on the WSL-based Ubuntu directly.

2. **Start & Configure Docker Desktop**:<br/>
   Start and configure Docker Desktop.<br/>
   Rationale: for CLI access from within WSL, the Docker daemon API has to be exposed via TCP on localhost.

   - *START* &rarr; `docker desktop` <kbd>RETURN</kbd>
   - *System Tray* &rarr; *Docker Desktop* &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Settings* &rarr; *General* &rarr; *Expose daemon...*

3. **Re-Enter Ubuntu under WSL**:<br/>
   Re-Enter Ubuntu GNU/Linux under Windows Subsystem for Linux again.<br/>
   Rationale: we have to install also the Unix client side of Docker.
    
   - *START* &rarr; `wsl terminal` <kbd>RETURN</kbd>

4. **Install Docker CLI and Docker-Compose**:<br/>
   Install native Linux versions of the Docker CLI.<br/>
   Rationale: native Linux version works more flawless than executing the Windows version under WSL.

   - `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
   - `sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`
   - `sudo apt-get update -y`
   - `sudo apt-get install -y docker-ce-cli`
   - `vi ~/.dotfiles.d/bashrc`<br/>
     &rarr; `export DOCKER_HOST=tcp://localhost:2375`

5. **Install Docker-Compose and Kubectl**:<br/>
   Install native Linux versions of the Docker-Compose and Kubernetes Kubectl.<br/>
   Rationale: native Linux versions work more flawless than executing the Windows versions under WSL.

   - `curl -skL https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose`
   - `chmod +x /usr/local/bin/docker-compose`
   - `curl -skL https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl -o /usr/local/bin/kubectl`
   - `chmod +x /usr/local/bin/kubectl`

## Install C/C++/Go/JavaScript/Java Development Environments

1. **Install GCC**:<br/>
   Install the GCC C/C++ compilers.<br/>
   Rationale: you want reasonable C/C++ compilers available -- feel free to skip.

   - `sudo apt-get install -y gcc g++ bison flex`

2. **Install Go**:<br/>
   Install the Go compiler.<br/>
   Rationale: you want reasonable Go compiler available -- feel free to skip.

   - `sudo apt-get install -y golang`

3. **Install Perl**:<br/>
   Install the Perl runtime.<br/>
   Rationale: you want reasonable Perl runtime available -- feel free to skip.

   - `sudo apt-get install -y perl`

4. **Install Node.js**:<br/>
   Install the Node.js JavaScript runtime.<br/>
   Rationale: you want a reasonable JavaScript environment available -- feel free to skip.

   - `curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`
   - `sudo apt-get install -y nodejs`

5. **Install OpenJDK**:<br/>
   Install the OpenJDK Java runtime.<br/>
   Rationale: you want a reasonable Java environment available -- feel free to skip.

   - `sudo apt-get install -y default-jdk`

