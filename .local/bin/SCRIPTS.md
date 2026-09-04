# Directory of Scripts

All user-created scripts live in `~/.local/bin/`, sorted into sub-directories
for management only. Every sub-directory is added to `$PATH` wholesale by this
line in `~/.profile`, so any executable here is callable by bare name
regardless of which folder it sits in:

```
export PATH="$PATH:$(du "$HOME/.local/bin/" | cut -f2 | tr '\n' ':' | sed 's/:*$//')"
```

A new script therefore needs nothing but `chmod +x`. Conversely, a script that
is not executable is silently unreachable.

Scripts marked **(fork)** do not exist in Luke Smith's upstream voidrice.

## `statusbar/`

i3blocks modules. Each is wired up by a `[block]` section in
`~/.config/i3blocks/config`, which sets `command=~/.local/bin/statusbar/$BLOCK_NAME`
globally. Modules handle clicks through `$BLOCK_BUTTON` and, because
`markup=pango` is set, colour themselves with `<span color='#rrggbb'>`. Icons
are Nerd Fonts glyphs, not emoji. A module with a `signal=N` line is refreshed
on demand by `pkill -RTMIN+N i3blocks`.

- `battery` -- Power remaining, with the icon and colour tracking charge level. Takes the battery name as an argument (`battery BAT0`).
- `brightness` -- **(fork)** Backlight level from sysfs, icon shifting with brightness. Scroll to change (needs `brightnessctl`). Signal 13.
- `clock` -- Time and date. Click for a calendar or upcoming calcurse events.
- `cpu` -- CPU temperature. Click for the most processor-intensive processes.
- `disk` -- Disk usage for the mountpoint given as an argument.
- `failed` -- **(fork)** Count of failed systemd units, system and user. Hidden entirely when nothing has failed, so it costs no bar width until it matters; a failed unit is otherwise invisible until you happen to run `systemctl`. Left click lists them. Signal 15.
- `help` -- A question mark that opens the readme when clicked.
- `i3-keyboard-layout` -- **(fork)** Current keyboard layout; cycled by `$mod+control+space`. Signal 30.
- `internet` -- **(fork, reworked)** Wifi and ethernet state. Icons grey out (`#969993`) when a link is down. Click for `nmtui`.
- `iplocate` -- Geolocation of the current public IP. Needs `geoiplookup`.
- `mailbox` -- Unread mail count, for use with mutt-wizard. Signal 12.
- `memory` -- Memory usage. Click for the most memory-intensive processes.
- `mpdupdate` -- Daemon that refreshes the `music` block whenever mpd changes. Signal 11.
- `music` -- Current song; greyed and italic when paused.
- `nettraf` -- **(fork)** Network throughput since the last run; at `interval=2` this reads as bytes per second. Turns amber above 1 MiB per interval. Complements `ping-lat` (latency) and `internet` (link state).
- `news` -- Unread newsboat articles. Opens newsboat or refreshes feeds. Signal 6.
- `pacpackages` -- **(fork, reworked)** Count of upgradable packages via `yay`. Only meaningful if a cronjob syncs repositories. Signal 8.
- `ping-lat` -- **(fork)** Network latency monitor. Colours by threshold and raises a dunst notification above 80ms/110ms. Signal 31.
- `popupgrade` -- Spawns a `yay` upgrade, then refreshes `pacpackages`.
- `gitcheck` -- **(fork)** State of this dotfiles repository: a tick when clean and level with origin, a down arrow and count when updates are waiting to pull, an up arrow when commits are unpushed, a warning sign when there are uncommitted changes. Fetches at most once every 15 minutes and runs with `GIT_TERMINAL_PROMPT=0` so it can never block the bar on a credential prompt. Signal 14.
- `sb-theme` -- **(fork)** Not a module: a palette (`C_NORM`, `C_DIM`, `C_OK`, `C_INFO`, `C_WARN`, `C_ALERT`, `C_ACCENT`) plus an `sb()` helper, sourced by the others so the whole bar can be recoloured from one file.
- `sb-bluetooth` -- **(fork)** Bluetooth state. Distinguishes off, blocked by rfkill, scanning, and connected; when a device is connected it shows an icon matching the device class (headset, phone, mouse, keyboard) plus that device's own battery when it reports one, turning amber below 20%. Left click opens the device menu, middle click toggles adapter power. Signal 17.
- `sb-price` -- **(fork)** Cryptocurrency ticker. Signal 29.
- `screen-timeout` -- **(fork)** Toggles and displays the DPMS screen timeout.
- `spotifyControls`, `spotifyPlay` -- **(fork)** Spotify transport controls and now-playing. Signal 32.
- `tasks` -- **(fork)** Outstanding background jobs queued with task spooler by `qndl`/`queueandnotify`, shown as total(queued). Hidden when the queue is empty. Signal 16.
- `torrent` -- Torrents idle, downloading or seeding. Signal 7.
- `volume` -- Volume percentage or a mute glyph. Middle click mutes, scroll changes. Signal 10.
- `vpn` -- **(fork)** VPN state for both WireGuard and OpenVPN, naming the active tunnel. Left click hands off to `wg-toggle`; middle click shows endpoint, handshake age and transfer counters.
- `weather` -- **(fork, reworked)** Forecast from wttr.in, with the temperature colour-coded by threshold. Signal 5.

## `cron/`

Cronjob scripts. None are enabled by default; see `IMPORTANT_NOTE.md` for the
environment exports a cronjob needs before it can raise notifications.

- `bgcron` -- **(fork)** Rotates the desktop wallpaper from `~/Wallpapers`.
- `checkup` -- Syncs package repositories and downloads (but does not install) updates.
- `cronbat` -- Warns via dunst when the battery drops below 25%.
- `crontog` -- Not a cronjob itself; toggles all user cronjobs on and off.
- `newsup` -- Refreshes newsboat feeds and updates the `news` block.

## `tools/`

Run manually or bound from within vim and other programs.

- `compiler` -- Compiles markdown, R markdown or LaTeX with the right command; runs `make && sudo make install` in a suckless source directory. Bound to `<leader>c` in vim.
- `dmenuhandler` -- Given a url, offers dmenu options for opening it. Used by newsboat as a link handler.
- `dmenupass` -- The `$SUDO_ASKPASS` password prompt.
- `ext` -- **(fork)** Extraction with options: `-c` extracts into the current directory rather than a new one.
- `extract` -- Detects an archive's type and runs the matching extraction command.
- `getbib` -- Finds a PDF's DOI via crossref.org and returns its bibtex entry.
- `getkeys` -- Prints the keybinding cheat sheets in `~/.config/getkeys/`.
- `ifinstalled` -- Checks that every named command exists, notifying and returning non-zero if any is missing. Used for optional dependencies: `ifinstalled ffmpeg sox || exit 1`.
- `linkhandler` -- Opens video urls in mpv, images in feh, music with wget, everything else in the browser.
- `lmc` -- Music/audio controller wrapping mpc and the mixer. The single place to change if you switch audio systems.
- `opout` -- "Open output": opens the PDF matching a `.md`/`.tex`/`.rmd`, or an `.html` in the browser. Bound to `<leader>p` in vim.
- `pauseallmpv` -- Pauses every running mpv instance over its IPC socket. Handles both socket layouts in use here: the `mpvSockets.lua` module's `/tmp/mpvSockets/<ppid>` and the older `/tmp/mpvsoc*` from the `mpv` alias in `aliasrc`.
- `ports` -- **(fork)** What is listening on this machine and what owns it, as a table of port/protocol/scope/process. `ports 8080` filters to a port; `ports -d` (`Mod+Ctrl+p`) picks one in dmenu to inspect, copy or kill. Sockets owned by other users need root to show a process and are marked rather than left blank.
- `podentr` -- Watches the newsboat queue with `entr` and runs `queueandnotify` on change.
- `qndl` -- Queues a download with taskspooler and notifies on completion.
- `queueandnotify` -- Reads the newsboat queue and hands each url to `qndl`. Replaces podboat.
- `remaps` -- Maps capslock to escape when tapped and super when held, and the menu key to super.
- `dotsync` -- **(fork)** Moves tracked files between `$HOME` and the repository at `~/voidrice-repo`, since `$HOME` is deliberately not a git work tree. `status`, `diff`, `capture` (into the repo), `deploy` (out to `$HOME`); the last two are a dry run until `-y`. The file list is always `git ls-files`, so only tracked paths move and an untracked private file cannot enter the repo.
- `voidrice-doctor` -- **(fork)** Self-check for the whole setup, bound to `Mod+Shift+F1`. Catches the failures that are otherwise silent: a script missing its executable bit is simply unreachable, a module that errors just shows an empty block, a glyph the Nerd Font lacks is quietly drawn by a substitute font, and a binding to an uninstalled command fails only when the key is pressed. Also flags bash-only syntax under a `sh` shebang, duplicate i3blocks entries and signal collisions. Exits with the number of failures.
- `remapd` -- **(fork, new)** Runs `remaps` again whenever a USB input device is hotplugged, so docking or attaching a keyboard does not silently lose the remap. Started from `xprofile`.
- `rotdir` -- "Rotates" a directory listing around a chosen file so the image viewer can page through the rest of the directory in order.
- `rssadd` -- Appends a feed url to `~/.config/newsboat/urls`, refusing duplicates.
- `rssget` -- **(fork, new)** Discovers a site's RSS/Atom feed and passes it to `rssadd`. Knows the hidden feed urls for YouTube, Reddit, GitHub, GitLab and Medium; otherwise scrapes the page's `<link>` tags. Reads the clipboard when given no argument.
- `sac` -- **(fork)** SSH alias creator. `sac <alias> [<user>@]<hostname> [-p <port>]` appends a Host block to `~/.ssh/config`.
- `setbg` -- Sets the wallpaper, storing it at `~/.config/wall.png`.
- `shortcuts` -- Regenerates the shell, ranger and vifm shortcut files from `~/.config/bmdirs` and `~/.config/bmfiles`. Run automatically by vim when those files are saved.
- `texclear` -- Removes LaTeX build files. Run by vim on leaving a `.tex` file.
- `transadd` -- Handles torrent magnet links, starting `transmission-daemon` if needed.
- `vifmimg`, `vu` -- **(fork)** Ueberzug image previews for vifm.

## `i3cmds/`

Bound to keys in `~/.config/i3/config`; mostly dmenu interfaces.

- `cabl` -- **(fork)** The clipboard "plumber", `$mod+c`. Inspects the X selection, defines a shell function for each applicable handler, and offers them in dmenu. Because the menu is built from `declare -F`, adding a handler means adding a guarded `... && name() { script "$@" ;}` line and the function name is what the user sees.
- `camtoggle` -- Starts or kills the webcam preview.
- `ddspawn` -- Creates, shows and hides the dropdown terminal (`$mod+u`) and calculator (`$mod+a`).
- `displayselect` -- Choose which displays to use. `$mod+F3`.
- `dmenu-bluetooth` -- **(fork)** Bluetooth device picker. *Untracked by git.*
- `dmenu-markdown` -- **(fork)** Opens any markdown file under `~/Documents` in the editor.
- `dmenu-screenlayout` -- **(fork)** Applies a saved `~/.screenlayout` profile. `$mod+Shift+F3`.
- `dmenumount` / `dmenuumount` -- The original mount helpers. Superseded by `mounter`/`unmounter` but kept and still callable by name.
- `dmenumountcifs` -- **(fork, new)** Discovers SMB servers on the LAN with avahi and mounts a share under `/mnt`. `$mod+Shift+F9`. Needs `avahi`, `smbclient`, `cifs-utils`.
- `dmenurecord` -- Audio, video or screencast recording. End with `killrecording`.
- `dmenuunicode` -- Searchable emoji picker; copies the character to the clipboard.
- `dropdowncalc` -- The dropdown calculator behind `$mod+a`.
- `ducksearch` -- DuckDuckGo search prompt, bangtags included.
- `hover` -- Floats the focused window into a bottom corner at a third of screen size.
- `i3resize`, `winresize` -- Window resizing, mapped to `$mod+Y/U/I/O`.
- `killrecording` -- Ends a recording cleanly. `$mod+Delete`.
- `lockscreen` -- **(fork, new)** Screen locker behind `$mod+x`. Pauses mpd and every mpv instance, mutes, runs `slock`, then restores the previous mute state. Replaces an inline `xset ... && mpc pause && pauseallmpv && slock` chain in the i3 config, which left the screen unlocked whenever `mpc pause` failed.
- `maconv` -- **(fork)** Converts a MAC address between Cisco, Windows and Unix notation. Reached through `cabl`.
- `maimpick` -- Screenshot picker: area, window or full screen, to file or clipboard.
- `mounter` -- **(fork, new)** Mounts USB drives, LUKS volumes and Android phones. `$mod+F9`. Supersedes `dmenumount`, which could not handle encrypted drives; also tries a bare `mount` first so fstab entries just work.
- `mpv-hover` -- **(fork)** Plays a copied video url in a floating mpv window. Reached through `cabl`.
- `openvpn` -- **(fork)** Picks a `.ovpn` profile from `~/.local/ovpn-config` and connects.
- `oui-lookup` -- **(fork)** Looks up a MAC address vendor in the Wireshark `manuf` database, cached at `~/.local/share/manuf` and refreshed monthly. Reached through `cabl`.
- `prompt` -- Yes/No confirmation prompt used by the destructive bindings.
- `samedir` -- Opens a terminal in the focused window's directory. `Mod+Shift+Return`. Walks the process tree outward and takes the first process with a usable cwd; the previous version took whichever process sorted last in `pstree`, which landed in `$HOME` whenever the shell had a child running.
- `showclip` -- Shows the clipboard and primary selection as notifications.
- `sshmenu` -- **(fork)** Picks a host from `~/.ssh/config` and connects. `$mod+Ctrl+s`.
- `sysact` -- **(fork, new)** Session and power menu: lock, leave/restart i3, sleep, hibernate, reboot, shutdown, display off. `$mod+BackSpace`.
- `td-toggle` -- Starts or stops `transmission-daemon`. `$mod+F7`.
- `tmuxdd` -- Startup script for the dropdown terminal.
- `toggletouchpad` -- Toggles the touchpad.
- `torwrap` -- Opens the transmission TUI, starting the daemon if needed.
- `tutorialvids` -- Menu of tutorial videos. `$mod+Shift+e`.
- `unmounter` -- **(fork, new)** Unmounts drives and phones, and re-locks LUKS volumes afterwards. `$mod+F10`. Supersedes `dmenuumount`.
