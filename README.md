# How to install a Linux development environment on Windows using Ubuntu and WSL2

## Why this guide

This a guide for people familiar with Linux and Unix environment, to create a development environment on Windows using WSL2 and Ubuntu.

## The author

Hi it's Mike MacCana here. I [develop web apps and before that was a sysadmin](https://mikemaccana.com). I've been using Linux for a little over 20 years, including at places like Red Hat and IBMs dedicated Linux group. I'm the example `sudo` user in `/etc/sudoers` in a couple of distros.

For most of this time, my main machines have been either Linux or macOS.
My fingers know bash's defaults, they type `/`, pronounced 'slash', to separate directories, `ctrl R` to search history, and edit lines with `ctrl ◀` and `ctrl ▶` without thinking. I select things in a terminal to automatically copy them and I click a mouse button to paste. In short, my developer profile is very Linux and Unix oriented. Much like you, I **don't want to throw away that knowledge, or muscle memory to use something different**.

## Why WSL2?

- Windows has excellent hardware support. You can get high end Surface hardware, or build a dual purpose development/gaming rig from parts and know you'll get working drivers from the manufacturer.

- `apt-get` on Ubuntu has more packages than `brew` on macOS.

- Design tools common in web development are usually available for Windows.

- There are some good Windows-based developer GUI tools. While I'm comfortable using `git rebase -i` (so don't @ me), the visual interactive rebase in Fork, a Windows git app, is a useful tool to help clean up my commit history before pushing it.

## Why not WSL2?

- Like setting up any new OS, there's work to do to make it your own. Some things _will_ be different if you;re used to another OS.

- While storing your code on Linux makes things nice and fast, accessing fils on Linux from Windows tools is still not as fast as accessing Windows files from Windows tools. In practice (as a node developer working on large TypeScript project) I find the current speed to not be an issue in my day to day work.

## Why this guide

The aims for this guide as follows:

- To be as complete as possible, have this guide be the go-to place for people used to developing on a Unix environment.

- To include developer focused settings of interest to Linux/Unix developers that make working on WSL2 easier, like `.bashrc`, `.inputrc` and `git` settings.

- To save time when setting up a new development environment. Eg, setting WSL to version 2 before installing Ubuntu (so Ubuntu is already WSL version 2), and installing Ubuntu before Docker (so Docker and VScode detect Ubuntu and add their commands.)

- Best practices, like keeping your code on the Linux side to make them faster.

## Right now it's a great tme to try WSL

There's a new Ubuntu release (20.04) and a new Windows release (20.04), both with a bunch of new features.

## Enable WSL2

Click Start Type "Powershell". Click "Run as Administrator"

Run these two commands to enable WSL:

    dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

    dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

## Get Ubuntu and Windows Terminal from the Windows store

Install Windows Terminal.

Install Ubuntu from windows store - do this first, as docker and vscode will detect.

## Set WSL to WSL2

Open Ubuntu from the start menu and and let it install.

Have a message about Virtusliation needing to be enabled? See the next step.

Currently, the older WSL1 is

    wsl --set-default-version 2

This ensures distros you install subsequently will run on WSL 2.

wsl -l -v
wsl --set-version Ubuntu 2
wsl --set-default-version 2

## If you need to: enable Virtualisation in UEFI/BIOS

You may have to go into your UEFI/BIOS and enable Virtualisation. You can reboot to your UEFI/BIOS from inside Windows. Open the Settings app and click "Update and Security" > "Recovery", and "Restart Now"/

When your PC reboots, it will go into Advanced startup. Go to “Troubleshoot -> Advanced options" and click on the "UEFI Firmware Settings" option.

## Get VScode and Edge

If you haven't got them already, you can also get [VSCode]() and [Edge]()

## Configure Terminal for Unix settings

Open terminal settings

"copyOnSelect": true,

and in change the default profile UUID to the Ubuntu one.

Also, because transparent terminals are cool:

"useAcrylic": true,
"acrylicOpacity": 0.9

## Install Docker Edge for Windows

https://download.docker.com/win/edge/Docker%20Desktop%20Installer.exe

This adds `docker-compose` and other useful things to Linux.

## Add Ubuntu to Windows Explorer

Pin `\\wsl$\Ubuntu` to Quick Access

## Set up Ubuntu for development in WSL2

Open Ubuntu from Windows Terminal and set up your deevlopment envrionment:

### Developer Tools

sudo apt install build-essential

### Quiet login

touch /home/mike/.hushlogin

### Sudo without a password

Typing your own password can get boring.

export EDITOR=code

visudo

And edit the line below to as follows:

# Allow members of group sudo to execute any command

# %sudo ALL=(ALL:ALL) ALL

%sudo ALL=(ALL) NOPASSWD:ALL

### Make tab completion work with any case

Open .inputrc (create it if needed) and add:

set completion-ignore-case On
\$include /etc/inputrc

### Add other development tools

Your command line developer tools are generally Linux ones, and will live on the WSL2.

I code primarily on node.js, so I'd install

curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs

### Add 'edit' to handle line numbers

I usually like to refer to my current editor as `edit`. `code -g` allows vscode to open a `filename:linenumber` and immediately to go that line number in a file. It's not default because files can have a colon in them. I don't put colons in file names, so I add this to my `.bashrc`

alias edit='code -g'

### Add an `open` command to open things with Windows

WSL2 comes with a Debian package called `wslu` which contains a command to open

alias open='wslview'

### Add ~/bin to your PATH

I like to add personal scripts and files to `~/bin`

export PATH=\$PATH:~/bin

### Configure git to ignore file mode changes

Checking out a repo, some files seem to have changed in git, with no actual modification of their contents. What has changed is their Unix file modes (the `mod` in `chmod`) - let's disable this.

git config core.fileMode false
