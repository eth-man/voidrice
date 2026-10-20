# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

`voidrice` — a fork of Luke Smith's LARBS dotfiles (`eth-man/voidrice`, default branch `main`), adapted for
sysadmin daily use on Arch Linux + i3-gaps + i3blocks. There is no build system, no test suite, and no
compiled code: everything here is either a config file or a POSIX-sh/bash script. "Testing" a change
means running the script or reloading the program it configures.

## Critical: the repo is NOT your $HOME

The repository lives at **`~/voidrice-repo`** and `$HOME` is not a git work tree at all. Run `git` only
inside the repo. This is deliberate: the work tree used to be `$HOME`, where a single `git add -A` would
have committed `~/.ssh`, `~/.gnupg`, 2.7GB of browser profile with saved passwords and cookies, Ledger
Live wallet data and filezilla credentials to a public repository.

The old `$HOME` repo has been deleted. Every ref was transferred to the clone first, including a 2021
stash preserved as branch `archive/stash-2021-07-25`.

**Files therefore exist in two places** and must be kept in step with `dotsync`
(`.local/bin/tools/dotsync`):

```
dotsync status     tracked files that differ between $HOME and the repo
dotsync diff [f]   full diff
dotsync capture    copy live $HOME edits INTO the repo, to commit   (-y to apply)
dotsync deploy     copy repo state OUT to $HOME                     (-y to apply)
```

The manifest is always `git ls-files` from the repo, so only already-tracked paths are ever copied and an
untracked private file has no route into the repository. Both directions are dry-run until `-y`.

The practical loop: edit in `$HOME` so the change is live, `dotsync capture -y`, then commit in the repo.
Or edit in the repo and `dotsync deploy -y`. Either way run `dotsync status` before committing, or you
will commit a half-synced tree.

Other things that matter every session:

- `.gitignore` exists now and blocks credentials, wallets, browser profiles and caches. It is a second
  line of defence, not the first — the first is that those files are not in the repo directory at all.
  Ignoring does not untrack: nothing sensitive is tracked or anywhere in history, and it must stay that way.
- Still prefer explicit paths over `git add -A`. The blast radius is now small, but the habit is cheap.
- `git ls-files` is the authoritative list of what belongs to the project (~176 files on `main`).
- Some `$HOME` entries are symlinks into `.config` and must stay that way: `.vimrc` → `.config/nvim/init.vim`,
  `.vim` → `.config/nvim/`, `.xprofile` → `.config/x11/xprofile`, `.bash_profile`/`.zprofile` → `.profile`.
  Edit the target, not the link. `dotsync` preserves symlinks rather than dereferencing them.
- `.profile` is shared by bash *and* zsh (via the `.zprofile` symlink), so it must stay POSIX and must not
  unconditionally source `.bashrc` — zsh has no `shopt`. The `[ -n "$BASH_VERSION" ]` guard at the end is
  load-bearing.
- `.config/mpv/script_modules/mpvSockets` is a git submodule. A fresh clone needs
  `git submodule update --init --recursive`, or mpv IPC sockets never appear and `pauseallmpv` and
  `mpv-hover` fail silently.

## Layout and how the pieces connect

```
.profile            login env; everything downstream depends on it (also .bash_profile/.zprofile)
.config/x11/        xinitrc -> execs i3; xprofile -> daemons (mpd, dunst, picom, remaps, setbg); xresources
.config/i3/config   keybindings; the bar's status_command is i3blocks
.config/i3blocks/config   maps each [block] to ~/.local/bin/statusbar/$BLOCK_NAME
.local/bin/         all user scripts, added to $PATH wholesale (see below)
  statusbar/        i3blocks modules
  i3cmds/           scripts bound to i3 keys (mostly dmenu UIs)
  tools/            manually-run or editor-bound helpers
  cron/             cronjob scripts (none enabled by default)
.config/getkeys/    per-program keybinding cheat sheets shown by the `getkeys` script
```

**`$PATH` is generated, not enumerated.** `.profile` runs
`du "$HOME/.local/bin/" | cut -f2 | tr '\n' ':'`, so *every* subdirectory of `~/.local/bin` is on `$PATH`.
A new script is callable by bare name as soon as it is `chmod +x` — no symlinking, and subdirectory
choice is organizational only.

**Env vars are the indirection layer.** Scripts call `$TERMINAL`, `$EDITOR`, `$BROWSER`, `$READER`,
`$FILE`, `$STATUSBAR` rather than hardcoding programs. Keep that convention in new scripts; the values
live in `.profile` (`st`, `nvim`, `google-chrome-stable`, `zathura`, `ranger`, `i3blocks`).

## i3blocks module conventions

Every `statusbar/` script follows the same shape, and new modules should too:

1. Handle clicks first via `$BLOCK_BUTTON` — by convention `1` = primary action, `2` = show detail via
   `notify-send`, `3` = a help notification describing the icons, `4`/`5` = scroll up/down:
   ```sh
   case $BLOCK_BUTTON in
       1) $TERMINAL -e nmtui ;;
       3) pgrep -x dunst >/dev/null && notify-send "🌐 Internet module" "..." ;;
   esac
   ```
2. Print one line to stdout. `markup=pango` is set globally in `.config/i3blocks/config`, so this fork's
   modules colorize with `printf "<span color='%s'>%s</span>\n" "$color" "$icon"`. The repo's dynamic-color
   palette: `#ffffff` normal, `#969993` disabled/greyed, `#ffff00` warning, `#DC143C`/`#e8cb61` alert.
3. Icons are **Nerd Fonts glyphs** (not emoji, despite what `SCRIPTS.md` still says) — the bar font is
   `pango:mono 14` with `separator_block_width=8` (both tuned down to stop the rightmost block
   being clipped). Copy an existing glyph rather than typing one; they are literal UTF-8 in the source.
   Use **Material Design** icons only (`nf-md`, U+F0001 and up) and light line shapes over filled boxes:
   other families sit on different grids and the Mono icon font squeezes them unevenly. Write
   **icon, one space, value**, with no trailing space; a block that must keep a fixed width pads its
   value on the right (`%-5s`), never between icon and value. `voidrice-doctor` checks the first two.
4. Optionally ask for an animation. Under ablocks (`eth-man/ablocks`, the C replacement for i3blocks)
   an extra output line `@name [ms [arg]]` plays an animation from its `config.h` for as long as the
   script keeps printing it. Guard it with `[ -n "$ABLOCKS" ]`, because i3blocks would show the line as
   short text, and use `if`, not `&&`, so a false test is not the script's exit status. See `battery`.

**Refreshing a module is signal-based.** A block declares `signal=N` in `.config/i3blocks/config`, and
anything that changes its state ends with `sigbar N` (`tools/sigbar`). Never call `pkill -RTMIN+N i3blocks`
directly: `sigbar` signals whichever bar is running, i3blocks or ablocks, so a bar switch needs no re-login.
Registered signals in this fork:

| N | block | N | block |
|---|---|---|---|
| 3 | vpn (via the `vpnupdate` daemon) | 13 | brightness |
| 4 | stocks (`stocks-toggle`; no block registered) | 14 | gitcheck |
| 5 | weather | 15 | failed |
| 6 | news | 16 | tasks |
| 7 | torrent | 17 | sb-bluetooth |
| 8 | pacpackages | 18 | ping-lat |
| 9 | recording | 19 | spotifyControls |
| 10 | volume | 29 | sb-price (`[btcprice]`, disabled) |
| 11 | music (via the `mpdupdate` daemon) | 30 | i3-keyboard-layout |
| 12 | mailbox | 20 | internet (via the `vpnupdate` daemon) |

**30 is the ceiling.** On Linux `SIGRTMIN` is 34 and `SIGRTMAX` 64, so `RTMIN+31` does not exist and
`pkill` rejects it. ping-lat and spotifyControls sat on 31 and 32 for years and were never refreshed by
signal. Pick a free number from 1–2 or 21–28.

Adding a module means: write `statusbar/<name>`, `chmod +x`, add a `[<name>]` section with `interval`
(and `signal` if it is event-driven) to `.config/i3blocks/config`, then restart the bar. i3blocks reads
its config only at startup, so a *config* change needs `i3-msg restart` (`mod+Shift+r`); a change to a
script's output alone is picked up by a bare `sigbar`, which re-runs every block.

## Applying changes without logging out

Reloading reads from `$HOME`, never from the repo. If you edited in the repo, `dotsync deploy -y` first
or you will reload the old file and conclude, wrongly, that your change did nothing.

| Changed | Reload with |
|---|---|
| `.config/i3/config` | `i3-msg reload`, or `mod+Shift+r` to restart in place |
| a `statusbar/` script | `sigbar` (all blocks); single block: `sigbar <signal>` |
| `.config/i3blocks/config` | `i3-msg restart` — i3blocks only parses its config at startup |
| `.config/x11/xresources` | `xrdb ~/.config/x11/xresources` (nvim does this automatically on write) |
| `.config/dunst/dunstrc` | `pkill dunst` (it respawns on next notification) |
| `.config/x11/xprofile` | requires restarting X |
| `.config/bmdirs` / `.config/bmfiles` | `shortcuts` (nvim runs it automatically on write) |

Test a statusbar module the way i3blocks calls it — as a bare command with the button variable set:
`BLOCK_BUTTON= internet` for the render path, `BLOCK_BUTTON=3 internet` for the click path.

## Editing scripts

- Shebang is `#!/usr/bin/env sh` for the large majority; use `bash` only when you actually need bashisms
  (arrays, `[[`, process substitution). Indentation is **tabs**.
- New scripts must be `chmod +x` or they will be silently unreachable despite being on `$PATH`.
- `<leader>s` in nvim runs `shellcheck` on the current file, but shellcheck is **not currently installed**
  on this machine. `sh -n` / `bash -n` are the available syntax check.
- Watch the shebang against the syntax actually used: `${var^^}`, arrays and `[[` are bash-only, and
  `/bin/sh` being bash on this Arch box hides such mistakes until the script runs elsewhere.
- User-facing errors go through `notify-send`, guarded by `pgrep -x dunst >/dev/null` when the script may
  run outside a graphical session.

## Generated files — do not hand-edit

- `.config/shortcutrc`, `.config/ranger/shortcuts.conf`, `.config/vifm/vifmshortcuts` are all regenerated
  by `.local/bin/tools/shortcuts` from `.config/bmdirs` and `.config/bmfiles`. Edit the `bm*` source files.
- `.config/getkeys/*` are hand-written cheat sheets that mirror each program's config. If you change a
  keybinding in `.config/i3/config`, update `.config/getkeys/i3` to match — nothing enforces this.

## Fork-specific additions

`SCRIPTS.md` is the per-script reference and now covers every script in `~/.local/bin`, marking
fork-only ones **(fork)**. Keep it in sync when adding a script.

Upstream (`LukeSmithxyz/voidrice`) has diverged structurally and should **not** be merged wholesale: it
moved to dwm/dwmblocks, flattened `.local/bin/` (no `tools/`, `i3cmds/`; statusbar modules renamed
`sb-*`), switched ranger→lf and sxiv→nsxiv, and moved shell configs to `.config/shell/`. This fork
deliberately keeps i3/i3blocks, ranger and the sub-directory layout. Port individual scripts, not trees.

`i3cmds/cabl` (`mod+c`) is the clipboard "plumber" and the entry point for several of these: it inspects
the X selection, defines a shell function per applicable handler, and offers them in dmenu. To make a new
script reachable from it, add a guarded `... && funcname() { yourscript "$@" ;}` line — the dmenu list is
built from `declare -F`, so the function name is what the user sees.

## Multiple machines

This fork is used on several ThinkPads and they drift. `main` on GitHub is canonical; a local clone can be
far behind, so `git fetch` and check `git status -sb` before assuming a script is missing or a bug is new.
When the same script has been edited on two machines, prefer the newer committed version and re-apply the
local tweak on top, rather than reverting wholesale.

Two merge hazards seen in practice, both worth checking after any sync:

- **Duplicate i3blocks entries.** The same `[block]` added in different positions on two machines merges
  cleanly into *two* blocks and shows a doubled icon. Git reports no conflict. Check with
  `grep '^\[' .config/i3blocks/config | sort | uniq -d`.
- **Silently reverted preferences.** Resolving a conflict by taking one side wholesale can flip a setting
  the other machine deliberately changed (a commented-out block becoming enabled, for instance). Read the
  resolved hunks rather than trusting the merge.

The related LARBS installer lives in its own repo, `eth-man/larbs` — `larbs.sh` and `progs.csv`. If a new
dependency is introduced here (a status module needing `brightnessctl`, say), it belongs in that repo's
`progs.csv`, not this one.

## Known-broken things worth not re-breaking

- `statusbar/battery` was dead for a while: `[[ "$prev_status"!= "Charging" ]]` is missing the space
  before `!=`, which is a syntax error, and unguarded `"$brightnessctl_path"` calls expand to `""` when
  brightnessctl is absent. Debug `echo`s in a statusbar module print *into the bar* — send them to stderr.
- `statusbar/internet` exists in two designs across machines. The single-icon rewrite has a live bug:
  `case $signal in [0-25])` is a glob matching one character, so any two-digit signal skips every
  threshold and lands on the default branch.
- `i3cmds/oui-lookup` pins wireshark's `release-4.0` deliberately — newer branches no longer publish
  `manuf`. Do not "fix" it to `master`.
