## The Voidrice - ETH-MAN
Forked from Luke Smith's repo.
this repo still use the i3wm and ranger.

currently tested on Arch  thinkpad X230, T460s, P14s
im trying to make changes that will fit to my regular day usage (Mostly Sysadmin task's)

## what has been changed from the original repo?
# new scripts :
+ Dmenu - SSH selector (super + CTRL + s)
+ Dmenu - Mac address convertor (Super + c  >  mac-convert)
+ Dmenu - openvpn connect
+ Dmenu - screen layout select saved screen profile for multiple screens (Super+Shift+F3)
+ sac - SSH alias creator ```Usage: sac <alias> [<user>@]<hostname> [-p <port>]```
+ i3blocks: most icons changed to support dynamic coloring when needed, cryptocurrency script improvement, weather color change by temp threshold , network latency monitoring, new vpn script , grayd out network icons when disconnected, pacpackages script.
+ new script allow to hover mpv youtube videos when youtube link copy (Super+c > start-mpv-hover-mode)

# misc
+ changed Dunst geometry to to fit higher task bar:   geometry = "350x10-0+34"
+ using nerd-fonts icons

__________






## Programs whose configs can be found here

+ i3 (i3-gaps)
+ ~~Xresourses/Xdefaults settings~~ Now moved to [my terminal (st) build](https://github.com/lukesmithxyz/st) which uses them
+ vim
+ bash
+ vifm
+ ~~mutt/msmtp/offlineimap~~ Now moved to [LukeSmithxyz/mutt-wizard](https://github.com/LukeSmithxyz/mutt-wizard)
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

I have a job, but every penny I get from followers or subscribers is more incentive to perfect what I'm doing.
You can donate to me at [https://paypal.me/LukeMSmith](https://paypal.me/LukeMSmith).
Donations are earmarked for whatever the donator wants, usually to go to funds for buying new equipment for the [YouTube channel](https://youtube.com/c/LukeSmithxyz).

# "Dependencies" and programs used

The programs I use here are always changing, but luckily you can just look at the installation list for [LARBS](http://larbs.xyz) here:

+ [List of programs installed by LARBS, including optional packages](https://github.com/eth-man/larbs/blob/master/progs.csv)

`A` marks programs in the AUR, `G` marks git repositories.
