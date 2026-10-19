# Linux File Operations — Compression, Searching, I/O Redirection, Pipes and Vi Editor

## 1. File Size and Disk Usage

### `du` — Disk Usage

The `du` command displays the disk space used by files and directories.

```bash
du -sh directory/
du -sk directory/
du -s directory/
du -ah directory/
```

- `-s` — display only the total.
- `-h` — human-readable sizes, such as KB, MB, or GB.
- `-k` — display sizes in KiB.
- `-a` — include files as well as directories.

Examples:

```bash
du -sh /home/bob
du -sk /var/log
du -sh *
```

**Remember:** `du` shows disk usage, not necessarily the apparent size of a file.

---

## 2. File Archiving Using `tar`

`tar` combines multiple files and directories into a single archive.

### Create an uncompressed archive

```bash
tar -cf archive.tar directory/
```

Example:

```bash
tar -cf python.tar /home/bob/reptile/snake/python
```

### Create a gzip-compressed archive

```bash
tar -zcf archive.tar.gz directory/
```

Example:

```bash
tar -zcf /home/bob/python.tar.gz -C /home/bob/reptile/snake python
```

### List archive contents

```bash
tar -tf archive.tar
tar -tzf archive.tar.gz
```

### Extract archives

```bash
tar -xf archive.tar
tar -xzf archive.tar.gz
```

### Important `tar` options

| Option | Meaning |
|---|---|
| `-c` | Create archive |
| `-x` | Extract archive |
| `-t` | List contents |
| `-f` | Specify archive filename |
| `-v` | Verbose output |
| `-z` | Use gzip compression |
| `-j` | Use bzip2 compression |
| `-J` | Use xz compression |
| `-C` | Change directory before processing files |

---

## 3. File Compression Tools

Compression reduces the size of files. Compression tools such as `gzip`, `bzip2`, and `xz` can compress individual files. `tar` can combine multiple files and use compression.

### 3.1 GZIP

Compress a file:

```bash
gzip file.txt
```

Creates `file.txt.gz` and normally removes the original file.

Decompress:

```bash
gunzip file.txt.gz
```

Display compressed content without extracting:

```bash
zcat file.txt.gz
```

Keep the compressed file while decompressing:

```bash
gzip -dk file.txt.gz
```

### 3.2 BZIP2

Compress:

```bash
bzip2 file.txt
```

Creates `file.txt.bz2`.

Decompress:

```bash
bunzip2 file.txt.bz2
```

Display content:

```bash
bzcat file.txt.bz2
```

### 3.3 XZ

Compress:

```bash
xz file.txt
```

Creates `file.txt.xz`.

Decompress:

```bash
unxz file.txt.xz
```

Display content:

```bash
xzcat file.txt.xz
```

### Compression comparison

| Tool | Compressed extension | Decompression |
|---|---|---|
| `gzip` | `.gz` | `gunzip` |
| `bzip2` | `.bz2` | `bunzip2` |
| `xz` | `.xz` | `unxz` |

### Archive with different compression methods

```bash
tar -zcf archive.tar.gz directory/
tar -jcf archive.tar.bz2 directory/
tar -Jcf archive.tar.xz directory/
```

**Remember:**
- `tar` creates an archive.
- `gzip`, `bzip2`, and `xz` compress data.
- `.tar.gz` is a tar archive compressed with gzip.

---

## 4. Searching for Files and Directories

### 4.1 `find`

`find` searches for files and directories by name, type, size, permissions, and other attributes.

Search by exact name:

```bash
find /opt -name "caleston-code"
```

Search under `/etc`:

```bash
find /etc -name "dummy.service"
```

Search without case sensitivity:

```bash
find /opt -iname "CALeston-code"
```

Find directories only:

```bash
find /home/bob -type d
```

Find regular files only:

```bash
find /home/bob -type f
```

Find files larger than 100 MB:

```bash
find /home/bob -type f -size +100M
```

### 4.2 `locate`

`locate` searches a prebuilt database of file paths. It is often faster than searching the filesystem with `find`.

```bash
locate dummy.service
```

Update the database:

```bash
sudo updatedb
```

Then search again:

```bash
locate dummy.service
```

**Important:** `locate` may return outdated results if its database has not been refreshed. `updatedb` may not be installed or enabled on every Linux distribution.

### `find` vs `locate`

| `find` | `locate` |
|---|---|
| Searches the filesystem directly | Searches an indexed database |
| Can find recently created files | May miss files absent from the database |
| Supports detailed conditions | Usually faster for name searches |

---

## 5. Searching File Contents Using `grep`

`grep` searches text inside files.

### 5.1 Basic search

```bash
grep "error" logfile.txt
```

Displays lines containing `error`.

### 5.2 Case-insensitive search

```bash
grep -i "error" logfile.txt
```

### 5.3 Show line numbers

```bash
grep -n "error" logfile.txt
```

### 5.4 Search recursively

```bash
grep -r "error" /var/log
```

### 5.5 Show lines that do not match: `-v`

```bash
grep -v "error" logfile.txt
```

Displays lines that do not contain `error`.

### 5.6 Match whole words: `-w`

```bash
grep -w "cat" animals.txt
```

Matches `cat` as a whole word, not as part of `category` or `catalog`.

### 5.7 Exclude lines containing a whole word

```bash
grep -vw "cat" animals.txt
```

- `-v` — invert the match and show nonmatching lines.
- `-w` — match the whole word only.

### 5.8 Fixed, exact string search

```bash
grep -F "172.16.238.197" file.txt
```

`-F` treats the search pattern as literal text rather than a regular expression. This is useful for IP addresses containing dots.

### 5.9 Find files containing a string

```bash
grep -rlF "172.16.238.197" /etc
```

- `-r` — search recursively.
- `-l` — print filenames only.
- `-F` — use a literal string.

### 5.10 Exact line matching

```bash
grep -x "hello world" file.txt
```

`-x` matches an entire line. This differs from `-w`, which matches a whole word.

### Important `grep` options

| Option | Meaning |
|---|---|
| `-i` | Ignore case |
| `-v` | Show nonmatching lines |
| `-w` | Match whole words |
| `-x` | Match entire lines |
| `-F` | Search literal text |
| `-r` | Search recursively |
| `-n` | Show line numbers |
| `-l` | Show matching filenames only |
| `-c` | Count matching lines |

---

## 6. Standard Input, Output and Error

Linux commands use three standard streams.

| File descriptor | Name | Meaning |
|---|---|---|
| `0` | stdin | Standard input |
| `1` | stdout | Standard output |
| `2` | stderr | Standard error |

### 6.1 Redirect standard output

```bash
echo "Hello Linux" > output.txt
```

`>` creates the file if needed and overwrites its contents if it already exists.

Append instead of overwriting:

```bash
echo "Another line" >> output.txt
```

### 6.2 Redirect standard error

```bash
python3 /home/bob/my_python_test.py 2> /home/bob/py_error.txt
```

Only error messages are redirected to the file.

Append errors:

```bash
command 2>> errors.txt
```

### 6.3 Redirect standard output and standard error

```bash
command > output.txt 2> errors.txt
```

Send both streams to the same file:

```bash
command > all_output.txt 2>&1
```

Or, in Bash:

```bash
command &> all_output.txt
```

### 6.4 Redirect input

```bash
sort < names.txt
```

The command reads its input from `names.txt`.

### Important redirection operators

| Operator | Purpose |
|---|---|
| `>` | Redirect stdout, overwrite |
| `>>` | Redirect stdout, append |
| `2>` | Redirect stderr, overwrite |
| `2>>` | Redirect stderr, append |
| `<` | Redirect stdin |
| `2>&1` | Send stderr to the current stdout destination |

---

## 7. Pipes in Linux

A pipe (`|`) passes the standard output of one command to the standard input of another command.

### Example 1: Search command output

```bash
ls /etc | grep "host"
```

### Example 2: Count matching lines

```bash
grep -i "error" logfile.txt | wc -l
```

### Example 3: List processes and filter

```bash
ps aux | grep nginx
```

### Example 4: Search and count

```bash
find /etc -type f | wc -l
```

### Pipe vs redirection

**Pipe:**

```bash
cat file.txt | grep "error"
```

Passes output to another command.

**Redirection:**

```bash
cat file.txt > copy.txt
```

Saves output into a file.

---

## 8. Vi Editor Basics

`vi` is a terminal-based text editor used to create and edit files.

Open or create a file:

```bash
vi notes.txt
```

### 8.1 Three useful modes

- **Normal mode:** Navigate, copy, delete, and execute editing commands.
- **Insert mode:** Type text into the file.
- **Command-line mode:** Save, quit, and execute editor commands.

Press `Esc` to return to Normal mode.

### 8.2 Enter Insert mode

| Key | Action |
|---|---|
| `i` | Insert before cursor |
| `a` | Append after cursor |
| `o` | Open a new line below |
| `O` | Open a new line above |

### 8.3 Save and quit

Press `Esc` first, then type:

| Command | Action |
|---|---|
| `:w` | Save |
| `:q` | Quit |
| `:wq` | Save and quit |
| `:x` | Save and quit if changes exist |
| `:q!` | Quit without saving |
| `ZZ` | Save and quit when changes exist |

Press `Enter` after commands beginning with `:`.

### 8.4 Copy, paste and delete

| Command | Action |
|---|---|
| `yy` | Copy (yank) current line |
| `p` | Paste below cursor/current line |
| `P` | Paste above cursor/current line |
| `3yy` | Copy three lines |
| `dd` | Delete current line |
| `3dd` | Delete three lines |
| `x` | Delete character under cursor |
| `u` | Undo change |
| `Ctrl+r` | Redo change |

### 8.5 Navigation

| Key | Action |
|---|---|
| `h` | Move left |
| `j` | Move down |
| `k` | Move up |
| `l` | Move right |
| `gg` | Go to first line |
| `G` | Go to last line |
| `0` | Go to beginning of line |
| `$` | Go to end of line |
| `/word` | Search forward for `word` |
| `n` | Repeat search forward |

### 8.6 Practical example

Create and edit a file:

```bash
vi /home/bob/notes.txt
```

1. Press `i`.
2. Type `Learning Linux commands`.
3. Press `Esc`.
4. Type `:wq` and press `Enter`.

Verify:

```bash
cat /home/bob/notes.txt
```

---

## 9. Practical Lab Commands

### Archive and compress a directory

```bash
tar -zcf /home/bob/python.tar.gz -C /home/bob/reptile/snake python
```

### Read a compressed manual without extracting it

```bash
zcat /usr/share/man/man1/tail.1.gz > /home/bob/pipes
```

### Find a file under `/opt`

```bash
find /opt -name "caleston-code"
```

### Find a string inside `/etc`

```bash
sudo grep -rlF "172.16.238.197" /etc 2>/dev/null
```

### Save command output into a file

```bash
echo "a file in my home directory" > /home/bob/file_with_data.txt
```

### Redirect Python errors

```bash
python3 /home/bob/my_python_test.py 2> /home/bob/py_error.txt
```

---

## 10. Quick Revision

- `du -sh directory/` — show total directory disk usage.
- `tar -cf archive.tar directory/` — create an uncompressed archive.
- `tar -zcf archive.tar.gz directory/` — create a gzip-compressed archive.
- `gzip file` — compress a file.
- `zcat file.gz` — display decompressed content without extracting a separate file.
- `find /path -name "filename"` — search for a file by name.
- `locate filename` — search a file-path database.
- `grep "text" file` — search file contents.
- `grep -v "text" file` — display lines without the match.
- `grep -w "word" file` — match a whole word.
- `>` — redirect stdout and overwrite.
- `2>` — redirect stderr.
- `|` — pass output from one command to another.
- `yy` — copy a line in vi.
- `p` — paste below.
- `u` — undo.
- `Ctrl+r` — redo.
- `:wq` — save and quit.
- `:q!` — quit without saving.
