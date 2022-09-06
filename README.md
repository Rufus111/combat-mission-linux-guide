# Combat Mission linux guide
##### Guide about launch Combat Mission games on Linux systems for newbies with Proton

https://www.winehq.org/pipermail/wine-bugs/2017-May/470327.html
So, we have well-known bug with opengl emu, and must compile own Proton with some changes. Sounds difficult? Not at all!
This guide created for people, who new in Linux, for example, Steam Deck users. If you know how to work in linux console - you can skip this guide, there will be nothing new for you here.
Despite the abundance of text, all actions are quite simple and in most cases you only need to copy-paste.
Also, I will not thoroughly analyze everything that happens on the monitor, I just want people who have recently made friends with Linux to be able to play their favorite game.
Working with all Combat Mission games series.

### Prepairing
So, I'm working with the OpenSuse Tumbleweed distro, but in general, for a Linux-based OS, the commands will differ only in the package manager. For example, installing some package `sudo zypper in <package>` will look like `sudo apt install <package>`/`sudo yum install <package>`/`etc...` in your favorite distro.

We will need for next step:
- Installed packages: git, podman, fontforge, make, gcc
- Configured podman
- Cloned github repo with source code of Proton
- Patched wine

##### Install packages:
```bash
sudo zypper in git podman fontforge make gcc nano
```
##### Configure podman:
So, here we must to set up podman for rootless execute. You can find oficial tutorial here: https://github.com/containers/podman/blob/main/docs/tutorials/rootless_tutorial.md.
In a nutshell, after podman installation you must check that some packages installed in your system, some file are created and restart user session (just close Console Emulator and open it again).
Because we still have to install these packages, we can just run the command to install them - if the packages are already installed, we will receive a message in the console:
```bash
sudo zypper in slirp4netns fuse-overlayfs
```
and check some files contains text:
```bash
cat /etc/subuid
cat /etc/subgid
```
like this:
```
rufus:100000:65536
```

If all done, restart user session and run for test:
```
podman -v
```

If something going wrong - just go deeper to https://github.com/containers/podman/blob/main/docs/tutorials/rootless_tutorial.md#administrator-actions

##### Clone Proton repo
Go to https://github.com/ValveSoftware/Proton and clone repo with command:
```bash
git clone --recurse-submodules https://github.com/ValveSoftware/Proton.git proton
```
This action can take some time, because we downloading source code from many projects.

##### Patch wine
After the download is finished - go to "proton" directory.
```bash
cd proton/
```
We must to edit opengl.c file from wine.
```bash
nano ./wine/dlls/winex11.drv/opengl.c
```
find the variable: press Ctrl+W, enter text (ppfd->cAlphaShift) and press Enter.
So, we have two of them, we need first
Edit this string by change value from 0 to 24 - like here https://www.winehq.org/pipermail/wine-bugs/2017-May/470327.html.
Press Ctrl+X for save and exit.

### Build Proton
##### _WARNING! This action can take A LOT of time - about hour. On weak processors, like on Steam Deck - it can be about two, three or more hours_
Let's build Proton!
We will choose simplest method - making with default Makefile and set build name with Environment Variable. I choose "combatmission" - but you can name it as you wish.
Just launch inside proton directory:
```bash
make install build_name=combatmission
```
and wait for some time. If something going wrong - "make" program inform you into console.

### Launch game
After succesful installation you must restart Steam.
If all is ok, after Steam launch go to your Library -> press right mouse button on game -> Properties -> Compatibility -> press on "Froce the use of a specific..." -> and choose "combatmission" from list.
Press Ok, and try to run this game.

Thats all!
