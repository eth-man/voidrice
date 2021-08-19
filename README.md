## The Voidrice - ETH-MAN
Forked from Luke Smith's repo since i prefer the old style setup..
This repository still uses i3wm and Ranger, i3blocks (I don't feel that wm-blocks is the right choice for me)

currently tested on Arch on thinkpad X230, T460s, P14s, X1 Carbon 7th gen.
im trying to make changes that will fit to my regular day usage (Mostly Sysadmin task's)

## what has been changed from the original repo?
# new scripts :
+ Dmenu - SSH selector (super + CTRL + s)
+ Dmenu - Mac address convertor (Super + c  >  mac-convert)
+ Dmenu - openvpn connect
+ Dmenu - screen layout select saved screen profile for multiple screens (Super+Shift+F3)
+ sac - SSH alias creator ```Usage: sac <alias> [<user>@]<hostname> [-p <port>]```

# new status bar scripts
+ new script allow to hover mpv youtube videos when youtube link copy (Super+c > start-mpv-hover-mode)
+ improved cryptocurrency status script.
+ weather color change by temp threshold
+ network latency monitoring
+ new vpn script
+ grayd out network icons when disconnected,
+ pacpackages script.
+ i3blocks: most icons changed to support dynamic coloring when needed, cryptocurrency script improvement, weather color change by temp threshold , network latency monitoring, new vpn script , grayd out network icons when disconnected, pacpackages script.
+ new script allow to hover mpv youtube videos when youtube link copy (Super+c > start-mpv-hover-mode)

# misc

+ changed Dunst geometry to to fit higher task bar:   geometry = "350x10-0+34"
+ using nerd-fonts icons
__________



## Programs whose configs can be found here

+ i3 (i3-gaps)
+ st
+ vim
+ bash
+ vifm
+ mutt-wizard
+ calcurse
+ ncmpcpp and mpd (my main music player)
+ mpv
+ And many little scripts I use filed in the `~/.local/bin/` directory
+ ranger

## More documentation

There's a full .pdf write-up of the repository [here: https://larbs.xyz/larbs_readme.pdf](https://larbs.xyz/larbs_readme.pdf).

Or, if you actually installed my dotfiles, you can just press `Super+F1` to
show the same document offline.

In the system, you can also press `Super+Shift+e` to watch tutorial videos on
different programs used. See [my YouTube channel](https://youtube.com/c/LukeSmithxyz) for more.

The command `getkeys` will also show basic key binds for different programs.

## Dynamic Configuration Files

Store your favorite or high-traffic directories in `~/.config/bmdirs` or your most
important config files in `~/.config/bmfiles` with keyboard shortcuts. When you add
things to theses files my vimrc will automatically run `shortcuts` which will
dynamically generate shortcuts for these in bash, ranger and optionally
qutebrowser and fish.

## Like my rice?

Feel free to add other suggestions and I may implement them.
