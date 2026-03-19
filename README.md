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
./lnk ./lnk ~/bin/lnk
```

Or non-interactively:

```bash
./lnk --yes --replace ./lnk ~/bin/lnk
```

Make sure `~/bin` is in your `PATH`.

---

## Usage

```bash
lnk [--yes] [--dry-run] [--replace | --backup] from to
```

* `from` — source path
* `to` — link path to create

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

MIT (or choose your preferred license)
