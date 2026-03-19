# lnk

`lnk` is an interactive wrapper around `ln` that helps you create symbolic and hard links safely.

It asks a small set of plain-English questions, explains the decision it makes, shows the equivalent shell command, and only proceeds after confirmation.

---

## Why this exists

Creating links in Linux is simple, but the mental model is not:

* Hard links vs symbolic links
* Relative vs absolute paths
* What happens when things move or are deleted
* How links behave across filesystems

`lnk` makes those decisions explicit and teachable.

---

## Features

* Interactive wizard by default
* Explains *why* a link type was chosen
* Shows the exact `ln` command before execution
* Safe handling of existing destinations (replace / backup)
* Supports:

  * Symbolic links (default)
  * Hard links (explicit opt-in)
* Works with standard Python (no dependencies)

---

## Installation

Clone the repository and link the script into your `PATH`:

```bash
$ ./lnk ./lnk ~/bin/lnk
```

Or non-interactively:

```bash
$ ./lnk --yes --replace ./lnk ~/bin/lnk
Detected source:
  Input path:    lnk
  Absolute path: /home/user/dev/python/lnk/lnk
  Kind:          file


Planned action

Source input:      lnk
Source absolute:   /home/user/dev/python/lnk/lnk
Link path:         /home/user/bin/lnk
Link type:         symlink
Path style:        absolute
Resolved target:   /home/user/dev/python/lnk/lnk
Stored target:     /home/user/dev/python/lnk/lnk
Conflict action:   none

Why these options were chosen:
  - Non-interactive mode defaults regular files to symbolic links.
  - Non-interactive mode defaults symbolic links to absolute paths.

Equivalent command:
  ln -s /home/user/dev/python/lnk/lnk /home/user/bin/lnk

Done.
```

Make sure `~/bin` is in your `PATH`.

---

## Usage

```bash
lnk [--yes] [--dry-run] [--replace | --backup] from to
```

* `from` — source path
* `to` — link path to create

```bash
lnk --help
usage: lnk [-h] [--yes] [--dry-run] [--replace] [--backup] from_path to_path

Create a symbolic link or hard link using an interactive wizard. By default, symlinks are preferred. Hard links are only created after explicit user choice in interactive mode.

positional arguments:
  from_path   Source path
  to_path     Link path to create

options:
  -h, --help  show this help message and exit
  --yes       Run non-interactively where possible. This auto-accepts defaults, but does not replace an existing destination unless --replace or --backup is also given.
  --dry-run   Show the planned action without changing the filesystem.
  --replace   Replace an existing destination without asking.
  --backup    Backup an existing destination before replacing it.
```

---

## Examples

### Link a config directory

```bash
lnk ~/dotfiles/nvim ~/.config/nvim
```

### Preview without changing anything

```bash
lnk --dry-run ~/dotfiles/nvim ~/.config/nvim
```

### Non-interactive install

```bash
lnk --yes --replace ./lnk ~/bin/lnk
```

---

## How it decides

### Link type

* Directory → symbolic link
* File → symbolic link (default)
* Hard link only when explicitly chosen

### Path style (symlinks)

* Relative → when paths may move together
* Absolute → otherwise (default in `--yes` mode)

---

## Safety rules

* Never overwrites existing paths without confirmation
* `--yes` does **not** overwrite unless combined with:

  * `--replace`
  * or `--backup`
* Detects and skips already-correct links

---

## Limitations (v1)

* No bind mounts
* No special file support (devices, sockets, etc.)
* Hard links:

  * files only
  * same filesystem only
  * not supported for symlink sources

---

## Mental model refresher

### Symbolic link

A file that points to another path.

* Works for files and directories
* Can cross filesystems
* Breaks if target moves

### Hard link

Another name for the same file (same inode).

* Files only
* Same filesystem only
* No “original” vs “link”

---

## Development

Requirements:

* Python 3.12+ (Ubuntu 24.04 default)
* No external dependencies

Run directly:

```bash
./lnk from to
```

---

## License

MIT
