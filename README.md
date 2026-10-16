# 🔍 Powerful Interactive Recursive Search Functions — WSL edition

A collection of efficient and flexible search tools to search inside file contents and paths — supporting everything from plain text to binary, PDF, Word, and more.

This is the WSL (Windows Subsystem for Linux) edition of [search-functions](https://github.com/bilalakbana/search-functions).
It differs only in three keys: `Enter` opens the file with its default Windows application, `Ctrl-e` shows it
selected in Windows Explorer and `Ctrl-c` copies its Windows path. All three go through
`sub-functions/windows_action`, which hands the path to PowerShell as an environment variable instead of building
a command line, so characters such as `&`, `%`, `'`, `$` or `;` in file names are never interpreted by a shell.

---

## 📑 Table of Contents

- [✨ Features](#-features)
- [🧭 Usage](#-usage)
- [🎹 Keyboard Shortcuts](#-keyboard-shortcuts)
- [🔧 Installation](#-installation)
- [🖼️ Examples](#-examples)
- [🙌 Credits](#-credits)

---

## ✨ Features

| Function          | Description                                                                 |
|-------------------|-----------------------------------------------------------------------------|
| `advancedsearch`  | Search in text, binary, and most file types                                 |
| `advancedsearch_plus` | Like `advancedsearch` + support for PDF, Word, DOCX                     |
| `detailedsearch`  | Like `advancedsearch` + displays matching lines for filtering               |
| `ugrep-fzf`       | Interactive search using `ugrep` and `fzf`, recursive in folders            |
| `ugrep-fzf-plus`  | Like `ugrep-fzf` + support for PDF, Word, DOCX                              |
| `rga-fzf`, `rga-fzfS` | Interactive search like `ugrep-fzf-plus`, slightly slower but powerful (`S`: sorted list) |
| `f`               | Search in file paths only (respects .gitignore; pass `--hidden`/`--no-ignore` through to fd) |
| `ugf`, `ugf-plus`, `rgf`, `rgfS` | Search both file contents and file paths recursively (`-plus`: PDF/Word/DOCX, `S`: sorted) |
| `fbr`             | Browse the definitions of all shell functions                               |
| `gfzf`, `gfzf-sbs`, `gfzf-full`, `gfzf-full-sbs` | Interactive git log with commit preview (side-by-side / full-file diffs) |
| `logs`, `logs-and-diffs` (+ `-sbs`, `-full`, `-full-sbs`) | Numbered git log (optionally with diffs) in `less` |

---

## 🧭 Usage

### 🔹 Basic Content Search

```bash
advancedsearch 'search string'
advancedsearch 'search string' --glob='*.cpp,*.py'

detailedsearch 'search string'
detailedsearch 'search string' --glob='*.md,*.txt'
```

### 🔹 Advanced Search in PDFs, DOCX

```bash
advancedsearch_plus 'keyword'
```

### 🔹 Interactive Search with FZF

```bash
ugrep-fzf
ugrep-fzf-plus
rga-fzf
rga-fzfS
```

### 🔹 File Path Search Only

```bash
f                          # respects .gitignore
f --hidden --no-ignore     # extra arguments go to fd
f --glob='*.cpp,*.py'     # by file-name pattern
```

### 🔹 Full Recursive Search (content + path)

```bash
ugf    # ugf-plus for PDF/Word/DOCX
rgf    # rgfS for a sorted list
(the query is typed inside fzf)
```

---

## 🎹 Keyboard Shortcuts

These shortcuts apply when using the interactive search tools (`fzf`, `ugrep-fzf`, `rga-fzf`, etc.):

| Shortcut | Action                                   |
|----------|------------------------------------------|
| `Enter`  | Open the selected file with its default Windows application |
| `Ctrl-e` | Show the file (or folder) selected in Windows Explorer |
| `Ctrl-c` | Copy the Windows path of the selected file, e.g. `\\wsl.localhost\Ubuntu\home\…` or `C:\…` (in `fbr`: the function name; use `Esc` to quit) |
| `Ctrl-o` | Open the file in nvim in a new tmux window |
| `Ctrl-t` | Search inside the selected file in a new tmux window |
| `Ctrl-u` / `Ctrl-g` / `Alt-g` / `Alt-u` | git diff / diff (delta) / short diff / log of the file |
| `Alt-j` / `Ctrl-j` | Folder list / file list in a new tmux window |
| `Ctrl-l` | Toggle the preview                       |

> These are customizable if you tweak your FZF or terminal bindings.

---

## 🔧 Installation

1. Install required tools inside WSL (latest versions recommended; Debian/Ubuntu package names — if your
   release lacks one, take it from the project's GitHub releases):

```bash
sudo apt install ugrep git ripgrep fd-find bat git-delta tmux neovim
sudo npm install -g git-split-diffs
cargo install ripgrep-all  # if using Rust
```

   - **fzf 0.58 or newer** is required (the scripts use `--style`). Distribution packages are often older
     (`fzf --version`); install a current release from [github.com/junegunn/fzf](https://github.com/junegunn/fzf) instead.
   - Some Debian/Ubuntu releases install `fd` as `fdfind` and `bat` as `batcat`. If `fd` or `bat` is missing:
     `mkdir -p ~/.local/bin && ln -s "$(command -v fdfind)" ~/.local/bin/fd && ln -s "$(command -v batcat)" ~/.local/bin/bat`
     (`~/.local/bin` must be in `PATH`).
   - The `_plus` / `-plus` variants need `ug+` / `ugrep+` (shipped with recent ugrep releases) and its filters
     (`pdftotext`, `antiword`, `pandoc`, `exiftool`).
   - Windows interop must be on (the WSL default): `command -v powershell.exe` has to print a path. If it does
     not, check that `/etc/wsl.conf` does not set `enabled=false` or `appendWindowsPath=false` under `[interop]`.

2. Clone the repo into `~/.SearchFunctions` — the scripts call each other through this fixed path, so the
   folder name matters:
```bash
git clone https://github.com/bilalakbana/search-functions-wsl.git ~/.SearchFunctions
```

3. `sub-functions/is_text_file` is a statically linked x86-64 build, so it runs on every WSL 1/2 distribution.
   On Windows on ARM, rebuild it inside WSL:
```bash
gcc -Wall -Wextra -O2 ~/.SearchFunctions/sub-functions/is_text_file.c -o ~/.SearchFunctions/sub-functions/is_text_file
```

4. Then add those to your shell for example ~/.zshrc
```bash
source ~/.SearchFunctions/advancedsearch                  # advancedsearch
source ~/.SearchFunctions/advancedsearch_plus             # advancedsearch_plus
source ~/.SearchFunctions/detailedsearch                  # detailedsearch
source ~/.SearchFunctions/search_in_file_content          # ugrep-fzf | ugrep-fzf-plus | rga-fzf | rga-fzfS
source ~/.SearchFunctions/search_in_file_path_and_content # ugf | ugf-plus | rgf | rgfS
source ~/.SearchFunctions/find_files                      # f
source ~/.SearchFunctions/sub-functions/list_functions    # fbr
source ~/.SearchFunctions/sub-functions/git_logs          # gfzf* | logs* (need git-delta; show_diffs needs git-split-diffs)
```

### Taking over changes from the Linux edition

This repo shares its history with search-functions, so fixes made there can be merged:
```bash
git remote add upstream https://github.com/bilalakbana/search-functions.git   # once
git pull upstream main
```
Conflicts are expected only where the editions differ: on the `Enter`, `Ctrl-e` and `Ctrl-c` lines keep this
edition's `windows_action open|reveal|copy-path|copy-text` call; in `README.md` keep the WSL parts; if upstream
rebuilt `sub-functions/is_text_file`, rebuild it statically again:
`gcc -Wall -Wextra -O2 -static sub-functions/is_text_file.c -o sub-functions/is_text_file && strip sub-functions/is_text_file`.

---

## 🖼️ Examples
To be added

---

## 🙌 Credits

Built and maintained by Bilal Akbana.

A special thanks to the developers and contributors of the incredible open-source tools that power this project:

[`fzf`](https://github.com/junegunn/fzf) [`ugrep`](https://github.com/Genivia/ugrep) [`ripgrep`](https://github.com/BurntSushi/ripgrep) [`ripgrep-all`](https://github.com/phiresky/ripgrep-all) [`git-delta`](https://github.com/dandavison/delta) [`git-split-diffs`](https://github.com/banga/git-split-diffs)

Without your dedication to open source, this project wouldn't exist — thank you!

> 💬 Pull Requests are welcome! If you have improvements, ideas, or bugfixes, feel free to contribute.