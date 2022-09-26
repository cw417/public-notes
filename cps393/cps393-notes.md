# CPS393 - Unix, C, C++

- [Linux/Bash](#linuxbash)
  - [Commands](#commands)
  - [Etc](#etc-1)
  - [Globs](#globs)
  - [Bash](#bash)
  - [Grep](#grep)
  - [Regex](#regex)
- [Vim](#vim)
  - [Movement](#movement)
  - [Motions](#motions)
  - [Editing](#editing)
  - [Etc](#etc-2)

## Linux/Bash

### Commands

- `ls` - lists all files and directories in specified directory
  - `-a` - (all);  list all files, including hidden ones (those prefixed with `.`, ie. `.vimrc`)
  - `-l` - (long); long listing of file properties
    - includes file permissions
  - `-h` - (human readable); prints file sizes in human readable format
  - `-t` - sort newest to oldest
  - `-R` - recursively list all sub-directories and files
  - `-p` - print following directory names
- `cd` - change into specified directory
- `pwd` - print the absolute path of the current working directory
- `cat {file}` - print specified file contents to terminal
  - `tac {file}` - print file contents to terminal in *reverse*
- `more` - print *paged* file contents/output to terminal; can navigate through with spacebar
- `less` - like more, but has vim movement
  - protip: `less` is better than `more`
  - vim movement
  - type `/{keywords}` to search the output, and `n` to move through the results
- `cp {src} {dest}` - copies source file to destination
- `rm {file}` - remove file; will not work on non-empty directories unless `-r` is specified
- `touch {file}` - create the specified file
- `mkdir {dir}` - create specified directory
- `rmdir {dir}` - removes an empty directory
- `mv {src} {dest}` - move source file to destination location
  - also used to rename files
  - ex. `mv hello_world.txt helloWorld.txt` would rename `hello_world.txt` to `helloWorld.txt`
- `echo "{string}"` - print the specified string to the terminal
  - protip: can be used to append to a file
  - ex. `echo "Hello, world!" >> hello.txt` will create a file named `hello.txt` (if it does not already exist) with the contents `Hello, world!`
    - if the file already exists, it will append instead of overwriting
    - can use single `>` to overwrite if the file does exist
- `wc {file}` - print `lines | words | chars` of the specified file
- `man {command}` - go to man page for specified command
  - can use `/{keywords}` to search, and `n` to navigate forward through the search results
  - `q` will quit
- `man -k {keyword}` - search for commands that include specified keyword
- `chmod {permissions} {file}` - change file permissions
  - can be hard to remember the permission format
  - permission specifiers are:
    - `u` - user
    - `g` - group
    - `o` - other
    - `a` - all
  - possible permissions are:
    - `r` - read; can look at file contents
    - `w` - write; can edit the file
    - `x` - can execute the file
  - ex. `chmod u+x script.sh` will allow the user to execute `script.sh`
- `head` - print first 10 lines of a file or output
  - `-n` - print n lines
- `tail` - print last 10 lines of a file or output
  - `-n` - print n lines
- `cut`
  - `-c {#}` to specify columns
    - ex. `cut -c 1-8`
  - `-f {#}` to specify fields
    - `-d "{delim}"` to specify delimiter
    - ex. `cut -f 1-3 -d ":"`
- `paste` - basically the same as `cat`?

### Etc-1

- `~` - home directory (ex. `cd ~` will change into home directory)
  - protip: you can also just run `cd` to change into the home directory
- `/` - root directory
- `.` - current directory
- `..` - one directory *up* the tree
- your user home directory is located at `/home/{user_name}/`

### IO

- `stdin` -  standard input; keyboard, pipes, etc...; redirect with `0>`
- `stdout` - standard output; generally the terminal; redirect with `1>`
- `stderr` - standard error; outputs error messages; redirect with `2>`
- can redirect both stdout and stderr at once with `&>`
- `/dev/null` - a black hole to send undesired stuff to
  - ex. you can redirect `stderr` to `/dev/null` to suppress error message in the terminal
    - `cat file.txt 2> /dev/null` will run `cat` on the file, but send any error messages to the void
  - anything sent to here is gone forever

### Globs

- `?` - matches any **single** char
  - can chain multiple together to match multiple chars
- `*` - matches any sequence of char(s)
- `[]` - match one of any listed chars
  - ex. `ls lab[123].txt` will match `lab1.txt`, `lab2.txt`, and `lab3.txt`
    - can also do `ls lab[1-3].txt`
  - can use `!` to specify **not** these chars
    - ex. `ls lab[!123].txt` will list any matching besides `lab1.txt`, `lab2.txt`, and `lab3.txt`
  - `[:alnum:]` - alphanumeric
  - `[:alpha:]` or `[A-Za-z]` - all letters
  - `[:upper:]` or `[A-Z]` - uppercase letters
  - `[:lower:]` or `[a-z]` - lowercase letters

### Bash

- `#!` - shebang; goes in first line of file along with what shell to use to execute it
  - ex. `#!/bin/bash` will use bash in the `bin` folder to execute the script
- `$1 $2 $3` etc... -  first, second, third, etc... arguements
- `$0` - name of shell script
- `$#` - number of args passed
- `$@` - list args as one string
- `$*` - list args as separate
- `read {var_name}` - read user input into variable name
  - access with `${var_name}`
- example script:

  ```bash
  #!/bin/bash
  echo "Connecting to TMU Moon Servers."
  echo "Username:"
  read username 
  ssh $username@moon.scs.ryerson.ca
  ```

  - This script will prompt the user for their username, and then attempt to connect to the TMU Moon servers
  - Could combine the 3rd and 4th lines by doing `read -p "Username: " username`
    - `-p` - prompt string to prompt for user input
    - `-sp` - secure prompt; do not display user input as they type

### Grep

- `grep string filename(s)` is command format
  - can also pipe output into grep
    - no need to specify filename if this is done
    - ex. `ls -a | grep ".*rc"` would list all files that start with `.` and end with `rc`

#### Grep Tips

- exclude directories: `grep -v ^d`

#### Options

- `-i` - case insensitive matching
- `-v` - print lines not matching
- `-x` - search string msut match entire line
- `-E` - use extended regex

#### Syntax

- `\` - escape char; necessary when matching any special char
- `.` - match any char except `\n`; line `?` in globs
- `{char}*` - match 0 or more of previous char; **NOT** like with globs
- `^` - pattern must be at start of line
- `$` - pattern must be at end of line
- `[]` - match any char in brackets; works line in glob
- `[^]` - match any chars **not** in brackets; works line in glob
- does not match multiple chars automatically
  - `\{m\}` - match exactly m repetitions of previous char
  - `\{m,\}` - match at least m repetitions of previous char
  - `\{m,n\}` - match between m and n repetitions of previous char
- `\<{char}` - match any line that contains a word beginning with char
- `{char}\>` - match any line that contains a word ending with char

### Regex

- Regular expressions are used to match patterns

## Vim

- `:q` -  quit; will not work if file has been edited
- `:q!` - quit, discarding any changes
- `:wq` or `:x` - save and quit

### Movement

- `h` - left
- `j` - down
- `k` - up
- `l` - right
- `$` - move to end of line
- `0` - move to beiginning of line
- `gg` - to top of file
- `G` - to bottom of file
- `ctrl + d` - down one half page
- `ctrl + u` - up one half page
- `:{line_number}` - move to specified line number

### Motions

- Combine these with other commands to perform combo actions
- movements also work as motions (ex. see `d` delete command below in Editing)
- `w` - to end of word
- `iw` - inner word; include entire word under cursor

### Editing

- all commands are relative to normal mode
- `u` - undo; undo last change since save to buffer
  - note: does not work exactly like a normal word processor
  - sometimes undoes more than you might want it to
- `R` - redo
- `i` - insert; enter insert mode at space *before* cursor
- `a` - append; enter insert mode at space *after* cursor
- `d{motion}` - delete according to motion
  - `dd` - delete current line
  - see [useful combos](#useful-combos) for more commands
- `o` - insert new line *below* cursor and enter insert mode
- `O` - insert new line *above* cursor and enter insert mode
- `y` - yank; copy highlighted area
- `p` - paste *after* cursor
- `P` - paste *before* cursor
- `v` - begin highlighting
- `V` - highlight entire line

### Etc-2

- `/{word/phase}` to search for a given word/phrase
  - use `n` to move forward through the results
- Find & replace
  - can do a file search and replace on a word/phrase
  - `:s/{word_to_remove}/{word_to_insert}` will find and replace on a single line
    - use `:%s` to match the entire file

### Useful combos

- `Vy` - copy line
- `V>` - indent line; highlights and then indents
- `V<` - unindent line
- `dd` - delete line
- `dw` - delete from cursor to end of word
- `diw` - delete current word that cursor is inside
- `d$` - delete from cursor to end of line
- `d0` - delete from cursor to beginning of line
- `dgg` - delete from cursor to beginning of file
- `dG` - delete from cursor to end of file
