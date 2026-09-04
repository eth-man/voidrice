# voidrice — eth-man

Dotfiles for an Arch + i3 setup, forked from
[Luke Smith's voidrice](https://github.com/LukeSmithxyz/voidrice) and shaped
around day-to-day sysadmin work.

Upstream has since moved to dwm and dwmblocks. This fork deliberately stays on
**i3-gaps, i3blocks and ranger**, and keeps `~/.local/bin` organised into
subdirectories rather than flattened.

Everything is a config file or a POSIX shell script — no build step, no
runtime, nothing to install beyond the programs being configured.

## What this fork adds

**Networking and remote work**

| | |
|---|---|
| `Mod+Ctrl+s` | pick a host from `~/.ssh/config` and ssh to it |
| `sac` | ssh alias creator — `sac <alias> [<user>@]<host> [-p <port>]`, which `Mod+Ctrl+s` then offers |
| `Mod+Ctrl+v` | WireGuard: connect, disconnect or switch tunnel |
| `Mod+Shift+v` | Pritunl profile selection |
| `Mod+Shift+w` | dmenu wifi picker — reuses saved connections, only asks for a password when it must |
| `Mod+Ctrl+p` | what is listening on this machine and what owns it; inspect, copy or kill |
| `Mod+Shift+F9` | mount an SMB share discovered on the LAN |
| `Mod+F9` / `Mod+F10` | mount and unmount drives, including LUKS volumes and MTP phones |
| `Mod+Shift+F3` | apply a saved screen layout, for docking |

**The clipboard plumber — `Mod+c`**

Looks at the selection and offers what can be done with it. A URL can be opened
or played, a MAC address converted between Cisco/Windows/Unix notation or looked
up against the IEEE vendor database, a video link played in a floating mpv
window, text turned into a QR code. Handlers are shell functions in `cabl`, so
the menu is extended by adding one guarded line.

**Status bar**

Nerd Fonts glyphs throughout, every module colour-coded through a single
palette in `statusbar/sb-theme` — white normal, grey off, amber attention, red
urgent. Beyond upstream's modules:

- **repo** — whether these dotfiles are behind, ahead or dirty; click to fetch
- **failed** — failed systemd units, system and user; hidden when there are none
- **tasks** — background jobs queued with task spooler
- **latency**, **nettraf** — round-trip time and throughput
- **VPN** — WireGuard and OpenVPN, naming the active tunnel
- **screen timeout**, **bluetooth**, **brightness**, **keyboard layout**

Weather is coloured by temperature, network icons grey out when a link drops,
and battery flashes only when actually discharging.

**Health check — `Mod+Shift+F1`**

`voidrice-doctor` checks the things that otherwise fail silently: a script
missing its executable bit is simply unreachable, a module that errors renders
as an empty block, a glyph the font lacks is quietly drawn by a substitute, a
keybinding to an uninstalled command fails only when pressed. It also flags
bash-only syntax under a `sh` shebang, duplicate i3blocks entries and signal
collisions. Exits with the failure count, so it works from a hook.

## The repository is not your home directory

Upstream deploys as a git work tree over `$HOME`. That means one `git add -A`
can commit `~/.ssh`, browser profiles with saved passwords, or wallet data to a
public repo.

Here the repository lives in its own directory and `$HOME` holds plain files,
with `dotsync` moving tracked files between the two:

```sh
dotsync status     # tracked files that differ between $HOME and the repo
dotsync diff       # show the differences
dotsync capture    # copy live $HOME edits INTO the repo, to commit   (-y to apply)
dotsync deploy     # copy the repo back OUT to $HOME                  (-y to apply)
```

The file list always comes from `git ls-files`, so only already-tracked paths
move in either direction and an untracked private file has no route into the
repository. Both directions are a dry run until `-y`.

Reloading always reads from `$HOME`, so deploy before testing a change made in
the repo.

## Layout

```
.config/i3/, i3blocks/   window manager and status bar
.config/x11/             xinitrc, xprofile, xresources
.local/bin/statusbar/    i3blocks modules
.local/bin/i3cmds/       scripts bound to keys, mostly dmenu interfaces
.local/bin/tools/        manually run or editor-bound helpers
.local/bin/cron/         cronjobs, none enabled by default
```

Every subdirectory of `~/.local/bin` is added to `$PATH` by `.profile`, so a new
script is callable by name as soon as it is `chmod +x`.

`.local/bin/SCRIPTS.md` documents every script individually.

## Notes

Keys that must keep working across a keyboard layout switch are bound with
`bindcode` rather than `bindsym`, since keysyms move with the layout and
keycodes do not.

Terminal font comes from `*.font` in `.config/x11/xresources`, which this build
of st reads at runtime — no rebuild needed to change it. Note `xrdb` passes that
file through `cpp`, so comment with `!`; a stray `/*` silently swallows
everything up to the next `*/`.

## Documentation

- `Mod+F1` — full guide as a PDF, rendered from `.local/share/larbs/readme.mom`
- `Mod+Shift+F1` — health check
- `getkeys` — per-program keybinding cheat sheets
- `.local/bin/SCRIPTS.md` — every script, with the fork-only ones marked

## Dynamic shortcuts

Directories in `~/.config/bmdirs` and files in `~/.config/bmfiles` get shell
aliases and ranger/vifm bindings generated automatically by `shortcuts`, which
the editor runs whenever those files are saved.

## Credit

The original [LARBS](https://larbs.xyz) and voidrice are Luke Smith's work, and
this fork keeps his structure and most of his scripts. The installer lives
separately at [eth-man/larbs](https://github.com/eth-man/larbs).
