<img src="https://assets.ubuntu.com/v1/29985a98-ubuntu-logo32.png" width="120" align="right" alt=""/>
<img src="https://upload.wikimedia.org/wikipedia/commons/5/5f/Windows_logo_-_2012.svg" width="120" align="right" alt=""/>

Author: [Dr. Ralf S. Engelschall](mailto:rse@engelschall.com)<br/>
Version: 2.3.1 (2021-12-01)

# Unix Environment under Windows

This is a documentation on how to setup a reasonable but opinionated Unix development environment under
[*Microsoft Windows 10*](https://windows.com) (Pro edition, 64-bit mode, version &ge; 1809) with the help of the
native [*Windows Subsystem for Linux (WSL)*](https://devblogs.microsoft.com/commandline/),
the [*Ubuntu*](https://www.ubuntu.com/) GNU/Linux distribution,
Dr. Ralf S. Engelschall's [*Unix dotfiles*](https://github.com/rse/dotfiles) shell environment,
the terminal emulator [*MinTTY/WSLTTY*](https://github.com/mintty/wsltty) and
the [*Docker Desktop for Windows*](https://www.docker.com/products/docker-desktop)
container execution platform.

![screenshot](screenshot.png)

> Notice: In short, the crux of this setup in contrast to the usual standard WSL setups is:
>
> - convenient root access (password-less `sudo`)
> - reasonably mapped home directory (`/c/Users/$USER`)
> - essential Unix shell configurations for Bash, Vim, Vifm, TMux and Git ("dot-files")
> - essential Unix shell tools (OpenSSH, RSYNC, etc)
> - additional WSL shell tools (`wsl-open`)
> - improved host terminal emulator (MinTTY/WSLTTY)
> - host SSH agent with transparent access from within WSL (`weasel-pageant`)
> - Docker and Kubernetes client CLIs
> - optionally, Docker runtimes inside WSL (DockerD, Podman) or on host (Docker for Windows)
> - optionally, essential programming language runtime (JavaScript/Java)

## Install Windows Subsystem for Linux (WSL)

1. **Ensure Windows 10 Professional/Enterprise, 64 Bit, Version &ge; 18.09**:<br/>
   Ensure you are running Windows 10 Professional or Windows 10
   Enterprise in 64-bit mode and in Version 1809 (October 2018), 1903
   (April 2019), 1909 (November 2019) or newer.

   > Rationale: Windows Subsystem for Linux (WSL) is available only
   > under those Windows editions, only under 64-bit and in a reasonable
   > fashion only under at least this version or newer.
   > Windows 10 in Version 1809 is the bare minimum. The recommended
   > system is Windows 10 in Version 1909.

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
   Enable the Windows feature *Windows Subsystem for Linux*.

   > Rationale: Windows Subsystem for Linux is the core feature we want to use.

   - *START* &rarr; `powershell` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Run as administrator*
   - `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`

   Alternatively:

   - *START* &rarr; `control panel` &rarr; *Programs* &rarr; *Programs and Features*
     &rarr; *Turn Windows features on or off* &rarr; *Windows-Subsystem for Linux*

   > Notice: In case you have trouble to enable *Windows Subsystem for Linux*, this can have many reasons.
   > In most cases, the reason is that point (1) above is not exactly fulfilled. Start over there again.

## Install Ubuntu GNU/Linux Operating System

1. **Install Ubuntu 20.04 LTS**:<br/>
   Install Ubuntu GNU/Linux 20.04 Long Term Support (LTS) from the Microsoft Store.

   > Rationale: you need a reasonable GNU/Linux distribution and it should receive updates for a longer time.

   - *START* &rarr; `microsoft store` &rarr; *Search* &rarr; `ubuntu 20.04 lts` &rarr; *Install*

   > Notice: In case the Microsoft Store is not available on your system, the reason
   > can be that you still have User Account Control (UAC) disabled, or
   > you are still not signed in with a Microsoft Account (although it
   > should be not required), or your Windows is still not activated or
   > you are still running an evaluation version of Windows. Anyway, try
   > to fix the problems the following way:
   > 
   > - *START* &rarr; *Settings* &rarr; *Updates & Security* &rarr; *Troubleshoot* &rarr; *Windows Store Apps* &rarr; *Run the troubleshooter*

## Setup Ubuntu GNU/Linux Operating System

1. **Enter Ubuntu under WSL**:<br/>
   Enter Ubuntu GNU/Linux under Windows Subsystem for Linux.

   > Rationale: we have to setup Ubuntu from itself.

    - *START* &rarr; *Ubuntu 20.04*

   Just be patient on first launch, it really takes time.
   Then, when asked for your username, enter the same as your Windows username.
   When asked for your password, enter either your Windows password or another one, but
   remember it (at least once until the step where we configure sudo(8) below)!

2. **Enable Convenient Root Access**:<br/>
   Ensure no password is needed for subsequent root access.

   > Rationale: just convenience only -- feel free to ignore if you want to enter your password over and over again.

    - `sudo vi /etc/sudoers`<br/>
      &larr; `%sudo ALL=(ALL:ALL) ALL`<br/>
      &rarr; `%sudo ALL=(ALL:ALL) NOPASSWD: ALL`

3. **Upgrade Ubuntu Operating System**:<br/>
   Upgrade to the latest package versions of Ubuntu and allow APT to use HTTPS.

   > Rationale: you always want the latest updates and Docker later needs HTTPS access.

   - `sudo apt update -y &&`<br/>
     `sudo apt upgrade -y --with-new-pkgs &&`<br/>
     `sudo apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common`

4. **Mount Windows directories in WSL with Meta-Data enabled**:<br/>
   Configure the mounting of Windows directories in WSL (`/c` instead of `/mnt/c`) and with *Meta-Data* enabled.

   > Rationale: allow POSIX file permissions on Windows drives from within WSL.

    - `sudo vi /etc/wsl.conf`<br/>
      &rarr; `[automount]`<br/>
      &rarr; `enabled = true`<br/>
      &rarr; `root    = /`<br/>
      &rarr; `options = "metadata,umask=022"`

5. **Use Combined Home Directory**:<br/>
   Map the Unix home directory to the regular Windows home directory.

   > Rationale: just convenience only -- feel free to ignore.

    - `exec sudo usermod -d /c/Users/$USER $USER`

   > Notice: Don't panic, we have to `exec` the command here to release the current user's processes
   > and this way let `usermod` proceed. This will immediately close the Unix terminal, of course.
   > This step actually is a tricky one as the running WSL sub-process might not allow the changing of
   > the underlying user home directory as long as it is running itself. One safe alternative is to
   > execute the following command from within a CMD or PowerShell terminal:<br/>
   > `wsl sudo usermod -d /c/Users/%USERNAME% %USERNAME%`

6. **Activate Filesystem Layout**:<br/>
   Perform a logout/login cycle on the host system to
   activate the new filesystem layout (mapping of /c and home directory)

   > Rationale: WSL does not re-mount the filesystem without a logout/login or reboot

## Configure Unix Shell Environment

1. **Re-Enter Ubuntu under WSL**:<br/>
   Re-Enter Ubuntu GNU/Linux under Windows Subsystem for Linux again.

   > Rationale: we have to configure the Unix environment from itself.

    - *START* &rarr; *Ubuntu 20.04*

2. **Install Essential Unix Tools**:<br/>
   Install all necessary essential and some more useful Unix tools.

   > Rationale: the subsequent Unix Shell Configurations are partially based on them and you really want a decent Unix environment.

   First, install the tools which available via standard package manager:

   - `sudo apt install -y bash less vim vifm tmux && `<br/>
     `sudo apt install -y procps lsof dnsutils tcpdump && `<br/>
     `sudo apt install -y openssh-client stunnel subversion git curl && `<br/>
     `sudo apt install -y mc tree file findutils && `<br/>
     `sudo apt install -y rsync rdup rclone restic w3m lftp && `<br/>
     `sudo apt install -y atool gzip bzip2 xz-utils zip unzip && `<br/>
     `sudo apt install -y diffutils diffstat patch patchutils par && `<br/>
     `sudo apt install -y openssl gnupg golang-cfssl apg uuid bc && `<br/>
     `sudo apt install -y imagemagick ghostscript poppler-utils && `<br/>
     `sudo apt install -y gcc g++ bison flex`

   Second, install the tool [FZF](https://github.com/junegunn/fzf) which is not available
   (at least not in latest version) via standard package manager:

   - `curl -skLO https://github.com/junegunn/fzf/releases/download/0.27.2/fzf-0.27.2-linux_amd64.tar.gz;`<br/>
     `tar zxf fzf-*-linux_amd64.tar.gz;`<br/>
     `sudo install -c -m 755 fzf /usr/local/bin/;`<br/>
     `rm fzf fzf-*-linux_amd64.tar.gz`

3. **Optionally Install Additional Unix Tools**:<br/>
   Install additionally useful Unix tools.

   > Rationale: Git-Town and TTY2Web are often useful.

   - `curl -skLO https://github.com/Originate/git-town/releases/download/v7.6.0/git-town_7.6.0_linux_intel_64.deb;`<br/>
     `sudo dpkg -i git-town_*.deb;`<br/>
     `rm git-town_*.deb;`<br/>
     `curl -skLO https://github.com/kost/tty2web/releases/download/v2.6.0/tty2web_linux_amd64;`<br/>
     `sudo install -c -m 755 tty2web* /usr/local/bin/tty2web;`<br/>
     `rm tty2web*`

4. **Install Unix Shell Configurations**:<br/>
   Install Dr. Ralf S. Engelschall's essential Unix dotfiles.

   > Rationale: you really want a reasonable pre-configured Unix shell environment.

   - `sudo apt install -y make;`<br/>
     `curl -skLO https://github.com/rse/dotfiles/archive/master.zip;`<br/>
     `unzip -x master.zip;`<br/>
     `(cd dotfiles-master && sudo make install);`<br/>
     `rm -rf dotfiles-master;`<br/>
     `rm master.zip;`<br/>
     `dotfiles -f ~`

5. **Install Unix Shell Addon Configurations**:<br/>
   Install Dr. Ralf S. Engelschall's Bash-FZF and Bash-ENVRC addons.

   > Rationale: you really want a reasonable pre-configured Unix shell environment.
   > This especially includes an improved <kbd>CTRL+r</kbd> functionality in Bash, which then opens
   > an FZF-based search functionality (which you can see in the screenshot above, too).

   - `curl -skL https://raw.githubusercontent.com/rse/bash-fzf/master/bash-fzf.rc -o ~/.bash-fzf.rc;`<br/>
     `curl -skL https://raw.githubusercontent.com/rse/bash-envrc/master/bash-envrc.rc -o ~/.bash-envrc.rc;`<br/>
     `exec bash;`<br/>
     `cdpaths -g`

6. **Optionally Extend Unix Shell Configurations**:<br/>
   Extend the Unix shell configuration with your personal information.

   > Rationale: these informations individualize your environment (replace the `<xxx>` placeholders, please).

   - `vi ~/.dotfiles/gitconfig`<br/>
     &rarr; `[user]`<br/>
     &rarr; `user  = <username>`<br/>
     &rarr; `name  = <firstname> <lastname>`<br/>
     &rarr; `email = <firstname>.<lastname>@<domain>`<br/>

7. **Install WSL Utilities**:<br/>
   Install additional WSL utilities.

   > Rationale: you want additional features inside WSL.

   - `sudo apt install -y ubuntu-wsl wslu;`<br/>
     `curl -skLO https://github.com/4U6U57/wsl-open/archive/master.zip;`<br/>
     `unzip -x master.zip;`<br/>
     `sudo install -c -m 755 wsl-open-master/wsl-open.sh /usr/local/bin/wsl-open;`<br/>
     `sudo mkdir -p /usr/local/share/man/man1;`<br/>
     `sudo install -c -m 644 wsl-open-master/wsl-open.1 /usr/local/share/man/man1/;`<br/>
     `rm -rf wsl-open-master;`<br/>
     `rm master.zip`

   - `curl -skLO https://github.com/CzBiX/WSLHostPatcher/releases/download/v0.1.0/release.zip;`<br/>
     `unzip -x release.zip;`<br/>
     `mkdir -p ~/AppData/Local/WSLHostPatcher;`<br/>
     `chmod 755 WSLHostPatcher.exe;`<br/>
     `mv WSLHostPatch* ~/AppData/Local/WSLHostPatcher;`<br/>
     `rm -f release.zip WSLHostPatch*.*`

   - `vi ~/.dotfiles/bashrc`<br/>
      &rarr; `PATH=$PATH:/c/Windows/System32/WindowsPowerShell/v1.0/`<br/>
      &rarr; `alias wsl="/c/Windows/System32/wsl.exe"`<br/>
      &rarr; `alias cmd="/c/Windows/System32/cmd.exe"`<br/>
      &rarr; `alias wsl-host-patcher="$HOME/AppData/Local/WSLHostPatcher/WSLHostPatcher.exe"`<br/>
      &rarr; `alias open=wsl-open`

8. **Optionally avoid system messages**:<br/>
   Force the system to not display messages.

   > Rationale: you don't want to be nerved with those messages

   - `touch ~/.hushlogin`

## Install MinTTY/WSLTTY Terminal Emulator

1. **Install MinTTY/WSLTTY**:<br/>
   Install the WSLTTY variant of MinTTY.

   > Rationale: a reasonable terminal emulator has to be used and the default WSL console is not good enough.

   - [WSLTTY version &ge; 3.5.1](https://github.com/mintty/wsltty/releases) &rarr; `wsltty-*-install.exe`
   - <kbd>WIN+r</kbd> &rarr; `%LOCALAPPDATA%\wsltty` &rarr; `add to context menu.lnk`

2. **Install DejaVu Sans Mono font**:<br/>
   Install a perfect monospaced font for the terminal emulator.

   > Rationale: MinTTY/WSLTTY configuration below references it.

   - download: [DejaVu Sans](https://dejavu-fonts.github.io/Download.html) &rarr; `dejavu-fonts-ttf-*.zip`
   - extract: `dejavu-fonts-ttf-*.zip` &rarr; <kbd>RIGHT-CLICK</kbd> *Extract all*
   - install: `dejavu-fonts-ttf-*\ttf\` &rarr; select all `*.ttf` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Install*

3. **Install MinTTY/WSLTTY Configuration**:<br/>
   Install Dr. Ralf S. Engelschall's MinTTY/WSLTTY configuration.

   > Rationale: reasonable colors and fonts should be used in the terminal emulator.

   - download: [MinTTY-config](https://github.com/rse/mintty-config/archive/master.zip)
   - extract: `master.zip` &rarr; <kbd>RIGHT-CLICK</kbd> *Extract all*
   - copy: `mintty-config-master\config`
   - paste: `%APPDATA%\wsltty` (override `config` file)

4. **Optionally Make MinTTY/WSLTTY easily accessible**:<br/>
   Pin MinTTY/WSLTTY to the Windows taskbar and additionally assign it to the global Windows hotkey <kbd>CTRL+ALT+c</kbd>.

   > Rationale: easy and quick access to the Unix shell

   - *START* &rarr; `wsl terminal` &rarr; <kbd>RIGHT-CLICK</kbd> *Pin to taskbar*.
   - Taskbar &rarr; *WSL Terminal* &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *WSL Terminal* &rarr;<br/>
   - ...<kbd>RIGHT-CLICK</kbd> &rarr; *Properties* &rarr; *Shortcut key* &rarr; <kbd>CTRL+ALT+c</kbd>.

<hr/>

## Optionally Install Secure-Shell (SSH) Environment (feel free to skip)

1. **Install PuTTY**:<br/>
   Install the PuTTY SSH client.

   > Rationale: you want to run the PuTTY Agent (`pageant`) for reasonable SSH agent support.

   - [PuTTY Downloads](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) &rarr; `putty-64bit-*-installer.msi`

2. **Generate SSH Key**:<br/>
   Generate (or use existing) SSH key.

   > Rationale: you don't want to use passwords, of course.

   - *START* &rarr; `puttygen` <kbd>RETURN</kbd>
   - &rarr; *Generate*
   - &rarr; *Key passphrase* &amp; *Confirm passphrase*
   - &rarr; *Save public key* &rarr; `c:\Users\<username>\Documents\ssh-key-pub.pem`
   - &rarr; *Save private key* &rarr; `c:\Users\<username>\Documents\ssh-key-prv.ppk`
   - &rarr; *Conversions* &rarr; *Export OpenSSH key* &rarr; `c:\Users\<username>\Documents\ssh-key-prv.pem`.<br/>

3. **Autostart PuTTY Agent**:<br/>
   Enable the PuTTY Agent to autostart on login and load the SSH private key.

   > Rationale: you want it to be running all the time.

   - <kbd>WIN+e</kbd> &rarr; `%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *New* &rarr; *Shortcut*
   - `"C:\Program Files\PuTTY\pageant.exe" "C:\Users\<username>\Documents\ssh-key-prv.ppk"`

5. **Install PuTTY Agent Client (Weasel-Pageant)**<br/>
   Install Weasel-Pageant for accessing the PuTTY Agent from within WSL.

   > Rationale: you want to directly access PuTTY Agent from within WSL.

   - [Weasel-Pageant Downloads](https://github.com/vuori/weasel-pageant/releases) &rarr; `weasel-pageant-1.4.zip`
   - `weasel-pageant-1.4.zip` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Extract all*
   - move `weasel-pageant-1.4\` to `%APPDATA%\weasel-pageant\`

4. **Enter Ubuntu under WSL via MinTTY/WSLTTY**:<br/>
   Re-Enter Ubuntu GNU/Linux under Windows Subsystem for Linux again (this time via MinTTY/WSLTTY).

   > Rationale: we have to configure also the Unix version of SSH.

    - *START* &rarr; `wsl terminal` <kbd>RETURN</kbd>

5. **Configure Unix Environment for SSH Agent**:<br/>
   Configure the Unix environment to use the Weasel-Pageant as the SSH agent.

   > Rationale: in every WSL terminal you want SSH agent access available automatically.

   - `vi ~/.dotfiles/bashrc`<br/>
     &rarr; `eval $(~/AppData/Roaming/weasel-pageant/weasel-pageant -r -s)`

## Optionally Enable Windows Subsystem for Linux (WSL) Version 2 (Windows 10 Version >= 19.03 only) (feel free to skip)

1. **Enable "Virtual Machine Platform" Feature**

   - *START* &rarr; `cmd` &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Run as administrator*
   - `dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all`

2. **Install WSL 2 Linux Kernel**

   - Visit the Microsoft [Updating the WSL 2 Linux kernel](https://docs.microsoft.com/en-us/windows/wsl/wsl2-kernel) page
     and [download and run the MSI installer](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)
     for the WSL 2 Linux Kernel.

3. **Switch to use WSL 2**

   - *START* &rarr; `cmd`
   - `wsl --set-default-version 2`
   - `wsl --set-version Ubuntu-20.04 2`

## Optionally Install Podman/Docker/Docker-Compose/Kubectl/Minikube/Helm Client CLIs (feel free to skip)

1. **Re-Enter Ubuntu under WSL**:<br/>
   Re-Enter Ubuntu GNU/Linux under Windows Subsystem for Linux again.

   > Rationale: we have to operate inside Ubuntu here again.

   - *START* &rarr; `wsl terminal` <kbd>RETURN</kbd>

2. **Install Podman/Skopeo/Buildah**:<br/>
   Install Podman, the daemon-less Docker alternative, and its companion tools
   Skopeo (registry access) and Buildah (container build).

   > Rationale: Podman is a Docker-compatible daemon-less way to run containers.
   > Notice that In WSL there are no systemd(8) and journald(8) daemons running,
   > so the environment has to be slightly adjusted to allow podman to work correctly.

   - `(. /etc/os-release;
     echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/testing/xUbuntu_${VERSION_ID}/ /" | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:testing.list;
     curl -skL "https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/testing/xUbuntu_${VERSION_ID}/Release.key" | sudo apt-key add - ) &&
     sudo apt update -y;
     sudo apt install -y podman skopeo buildah`

   - `sed -e 's;^# cgroup_manager = .*;cgroup_manager = "cgroupfs";'
      -e 's;^# events_logger = .*;events_logger = "file";'
      </usr/share/containers/containers.conf >/tmp/container.conf;
      sudo install -c -m 644 /tmp/containers.conf /etc/containers/containers.conf; rm /tmp/containers.conf`

3. **Install Docker/Docker-Compose**:<br/>
   Install original native Linux version of the Docker and Docker-Compose CLIs.

   > Rationale: the original native Linux versions works more flawless than executing the Windows versions under WSL.

   - `curl -skL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - &&`
     `sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" &&`
     `sudo apt update -y &&`
     `sudo apt install -y docker-ce docker-ce-cli`

   - `V=$(curl -skL https://github.com/docker/compose/releases | egrep 'releases/tag/v[0-9.]*"' | sed -e 's;^.*releases/tag/v;;' -e 's;".*$;;' | head -1);`
     `sudo curl -skL https://github.com/docker/compose/releases/download/v${V}/docker-compose-linux-x86_64 -o /usr/libexec/docker/cli-plugins/docker-compose; sudo chmod 755 /usr/libexec/docker/cli-plugins/docker-compose`

4. **Install Kubectl/Minikube/Helm**:<br/>
   Install the Kubernetes client kubectl(1), the Kubernetes all-in-one server minikube(8) and the Kubernetes package manager helm(1).

   > Rationale: when dealing with the Kubernetes world of containers, those two CLIs are essential.

   - `sudo curl -skL https://storage.googleapis.com/kubernetes-release/release/$(curl -skL https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl -o /usr/local/bin/kubectl &&`
     `sudo chmod 755 /usr/local/bin/kubectl`

   - `sudo curl -skL https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 -o /usr/local/bin/minikube;`
     `sudo chmod 755 /usr/local/bin/minikube`

   - `V=$(curl -skL https://github.com/kubernetes/helm/releases | egrep 'releases/tag/v3\.[0-9.]*"' | sed -e 's;^.*releases/tag/v;;' -e 's;".*$;;' | head -1);`
     `curl -skL $(printf "%s%s" https://get.helm.sh/helm-v${V}-linux-amd64.tar.gz) | sudo tar -z -x -f - --strip-components=1 -C /usr/local/bin linux-amd64/helm; sudo chmod 755 /usr/local/bin/helm`

## Optionally Establish DockerD/ContainerD as Container Runtime (Alternative 1, WSL2 only) (feel free to skip)

Pro: the original and no root permissions required for docker(1) and docker-compose(1) calls<br/>
Con: intransparent and slowed down development

1. **Allow Access to Daemon**<br/>
   Allow the current user access to the Docker daemon.

   - `sudo usermod -aG docker $USER`

2. **Configure User Environment**:<br/>
   Configure the user environment to auto-start the Docker daemon.

   > Rationale: WSL has no init scripts, so start the Docker daemon manually when the shell is opened.

   - `vi ~/.dotfiles/bashrc`<br/>
     &rarr; `(sudo service docker start || true) >/dev/null 2>&1`

## Optionally Establish Podman as Container Runtime (Alternative 2, WSL2 only) (feel free to skip)

Pro: fully transparent and fast Open Source development<br/>
Con: the clone and root permissions required for docker(1) and docker-compose(1) calls

1. **Provide Docker REST API Service**:<br/>
   Let Podman provide the Docker REST API as a Unix domain socket under the usual `/var/run/docker.sock` path.

   > Rationale: Docker-Compose requires this access method.

   - `curl -L "https://fdit-gitlab.dit.htwk-leipzig.de/martin.meszaros/wsl2-podman-compose/-/raw/master/podman-service?inline=false" >podman-service;`
     `install -c -m 755 podman-service /etc/init.d/; rm podman-service`

2. **Configure User Environment**:<br/>
   Configure the user environment to auto-start the Docker REST API of Podman.

   > Rationale: WSL has no init scripts, so start the Podman socket service manually when the shell is opened.

   - `vi ~/.dotfiles/bashrc`<br/>
     &rarr; `(sudo service podman-service start || true) >/dev/null 2>&1`

3. **Substitute Docker CLI**<br/>
   Substitute the call-compatible podman(1) for docker(1).

   - `sudo apt remove docker-ce docker-ce-cli`
   - `sudo sh -c '(echo "#!/bin/sh"; echo "exec /usr/bin/podman \"\$@\"") >/usr/local/bin/docker && chmod 755 /usr/local/bin/docker'`

## Optionally Establish Docker for Windows as Container Runtime (Alternative 3, Host only) (feel free to skip)

Pro: Docker available also on the host, Kubernetes included<br/>
Con: intransparent and slowed down development, no longer free for large Enterprise use

1. **Install Docker Desktop**:<br/>
   Install the Docker Desktop for Windows (Community Edition) distribution.

   > Rationale: you want Docker container engine be available on the host.

   - *START* &rarr; `control panel` &rarr; *Programs* &rarr; *Programs and Features*
     &rarr; *Turn Windows features on or off* &rarr; *Hyper-V*
     (a reboot is required)

   - [Docker Desktop](https://www.docker.com/products/docker-desktop) &rarr; *Download for Windows*
     (a reboot is required)

   - *START* &rarr; `computer management` <kbd>RIGHT-CLICK</kbd> &rarr; *Run as administrator*
   - *Computer Management* &rarr; *System Tools* &rarr; *Local Users and Groups* &rarr; *Groups* &rarr; `docker-users` &rarr; <kbd>LEFT-DOUBLE-CLICK</kbd> &rarr; *Add...*
     (ensure that your user is really in this group -- usually it is the case by default)

   > Notice: You need a Docker Hub account for downloading and using Docker Desktop.
   > [Sign up](https://hub.docker.com/signup) first if you still don't have a Docker Hub account.

   > Notice: Yes, Hyper-V is necessary as Docker Desktop for Windows (in
   > contrast to the regular Docker for Linux) runs Docker inside a
   > small Linux distribution which is executed in a virtual machine via
   > Hyper-V. 

2. **Start & Configure Docker Desktop**:<br/>
   Start and configure Docker Desktop.

   > Rationale: for CLI access from within WSL 1, the Docker daemon API has to be exposed via TCP on localhost.
   > For WSL 2 Docker for Windows has a special type of integration which does not need this any longer.

   - *START* &rarr; `docker desktop` <kbd>RETURN</kbd>
   - *System Tray* &rarr; *Docker Desktop* &rarr; <kbd>RIGHT-CLICK</kbd> &rarr; *Settings* &rarr; *General* &rarr; *Expose daemon...*

3. **Re-Enter Ubuntu under WSL**:<br/>
   Re-Enter Ubuntu GNU/Linux under Windows Subsystem for Linux again.

   > Rationale: we have to install also the Unix client side of Docker.

   - *START* &rarr; `wsl terminal` <kbd>RETURN</kbd>

4. **Configure User Environment**:<br/>
   Configure the user environment to access Docker for Windows on the host.

   > Rationale: the access works through the TCP socket instead of the usual Unix domain socket.

   - `vi ~/.dotfiles/bashrc`<br/>
     &rarr; `export DOCKER_HOST=tcp://localhost:2375`

## Optionally install Language Runtimes (feel free to skip)

1. **Install Node.js**:<br/>
   Install the Node.js JavaScript language runtime.

   > Rationale: you want a reasonable JavaScript environment available.

   - `curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -`
   - `sudo apt install -y nodejs`

2. **Install OpenJDK**:<br/>
   Install the OpenJDK Java language runtime.

   > Rationale: you want a reasonable Java environment available.

   - `sudo apt install -y default-jdk`

