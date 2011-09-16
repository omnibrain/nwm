# nwm - node window manager

nwm is a dynamic window manager for X written at NodeKO 2011. 

It uses libev to interface with X11, and allows you to lay out windows in Node.

I wrote the code in this repo during Node Knockout 2011 ([old repo](https://github.com/mixu/nodeko)). Unfortunately I'm not in the competition since I forgot to confirm my initial team registration (click accept). Not a big deal.

**This is the repo you should watch/fork for future updates.**

The underlying X11 bindings are written as a Node native extension in C++/C. 

I'm now actively using this as my primary window manager, as multi-monitor support is finally OK. You can give it a try as your primary as well, and report back bugs -- or nwm in a secondary X server using Xephyr.

# Features

Cpp bindings:

- Uses X11 via libev
- API abstracts over X11 so you don't need to learn X11 just to customize your layout

Nwm.js:

- Layout decisions are done in Javascript, not C++.
- Write your own layout, or use one of the built-in layouts (screenshots below)
- Support for workspaces:
    - Access workspaces 0-9 via keyboard (and max int via JS)
    - Each workspace can have it's own layout
- Support for "main window":
    - One window is considered to be the "main window"
    - That window usually gets more space depending on the layout
    - Each workspace has it's own main window scale setting
- REPL, so you can issue commands to it interactively

# Installing

You need to use a 0.4.x branch of Node for now, since the 0.5.x branch does not have libev bundled. For instance v0.4.11 works (git checkout v0.4.11 after cloning the node repo).

From github:

    node-waf clean
    node-waf configure build

From npm (less frequently updated):

    npm install nwm

# Installing as a primary window manager

Find out what your login manager is:

    cat /etc/X11/default-display-manager

If it is GDM:

1: Create log directory and copy files

    sudo mkdir /nwm-log
    sudo chmod 777 /nwm-log
    sudo cp -R ./mnt/nwm/ /usr/local/lib/
    sudo chmod +x /usr/local/lib/nwm/nwm.sh

2: add the following as nwm.desktop to /usr/share/xsessions:

    [Desktop Entry]
    Encoding=UTF-8
    Name=nwm
    Comment=This session starts nwm 
    Exec=/usr/local/lib/nwm.sh
    Type=Application

Select "nwm" from the Sessions menu when logging in.

Some tips for running nwm in a VM:

- If you use VMware Workstation, you have to start vmware-user manually for multi-monitor support via Xinerama after starting nwm.
- If you use VirtualBox, you have to use xrandr manually for multi-monitor support (e.g. randr --output VBOX0 --auto --left-of VBOX1)

# Running under a secondary X11 server (Xephyr)

    # start Xephyr
    Xephyr -screen 1024x768 -nodri -br :1 &
    # export gedit to the X server on display 1
    DISPLAY=:1 gedit
    DISPLAY=:1 gnome-terminal
    # now start nwm.js on display 1
    DISPLAY=:1 node nwm.js

Some notes:

- Xephyr errors out under VirtualBox. You may need to start Xephyr with -nodri if you use VirtualBox with guest additions.
- You may have to Chrome start with --explicitly-allowed-ports=6000 for it to connect to some localhost ports.

# Keyboard shortcuts and layouts

The keyboard shortcuts use a different base key combination depending on whether you are running nwm on Xephyr / running nwm as the primary WM.

Under Xephyr, the base key combination is Ctrl+Meta (e.g. Ctrl+Win).

When running natively, the base key is Meta (Win).

This is so that I can test nwm inside itself, yet have decent shortcuts natively:

    # Launching programs
    Meta + Shift + Enter -- Start xterm

    # Switching between layouts
    Meta + Space -- Switch between tile, monocle, wide and grid layouts

    # Focus
    TODO Meta + j -- Focus next window
    TODO Meta + k -- Focus previous window

    # Main window
    Meta + h -- Decrease master area size
    Meta + F10 
    Meta + l -- Increase master area size
    Meta + F11
    Meta + Enter -- Sets currently focused window as main window

    # Closing windows
    Meta + Shift + c -- Close focused window

    # Workspaces
    Meta + [1..n] -- Switch to workspace n
    Meta + Shift + [1..n] -- Move window to workspace n

    # Multi-monitor keys
    Meta + Shift + , -- Send focused window to previous screen
    Meta + Shift + . -- Send focused window to next screen
    
## Vertical Stack Tiling (e.g. DWM's tiling)

![screenshot](https://github.com/mixu/nwm/raw/master/screenshots/tile.png)

## Monocle (a.k.a. fullscreen)

![screenshot](https://github.com/mixu/nwm/raw/master/screenshots/fullscreen.png)

## Bottom Stack Tiling (a.k.a. wide)

![screenshot](https://github.com/mixu/nwm/raw/master/screenshots/wide.png)

## Grid (a.k.a fair)

![screenshot](https://github.com/mixu/nwm/raw/master/screenshots/grid.png)

# Using from the console

For now, the internal API is a bit messy, but you can use tab completion with the REPL to figure it out.
