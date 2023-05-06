---
title: Shell Notes
---

### Processing a Shell input line
* A series of transformations are applied to Shell input lines
    1. variable expansion, e.g. `$HOME` -> `/home/z5308693`
    2. command expansion, e.g. `$(whoami)` -> `z5308693`
    3. arithmetic, e.g. `$((6 * 7))` -> `42`
    4. word splitting - line is broken up on white-space
    5. pathname globbing, e.g. `*.c` -> `main.c i.c`
    6. I/O redirection, e.g. `<i.txt` -> stdin replaced with stream from `i.txt`
    7. first word used a program name, other words passed as arugments
* directories in `PATH` searched from program name

### Shell variables
* All shell variables are untyped - all strings
* Shell variables are not declared
* Shell variables do not need initialisation
* No local variables
    * Except sub-shells and functions (sort-of)
    * Changes to variables in sub-shells have no effect outside sub-hsell
    * Components of pipeline executed in subshell

### Shell
* `$name` is replaced with value of variable `name`
* `name=value` assigns value to name
* `$((expression))` evaluates `expression`
* `$(command)` or `` `command` `` runs `command`
* single quotes `' '` group everything inside into a single word, but no interpretation inside
    * No single quotes inside single quotes
    * Can put double quotes though
* double quotes `" "` groups everything inside into a single word, but interpretation exists inside
    * variables, commands and arithmetic are expanded
    * can use backslash to escape `$` or `"` or `'`
    * but not globbing or word-splitting inside double quotes
    * can use single quotes
* `<<word` specify multi-line string as command input until `word` is mentioned, e.g:
```
$   name=Andrew
$   tr a-z A-Z <<END-MARKER
Hello $name
How are you
Good bye
END-MARKER
HELLO ANDREW
HOW ARE YOU
GOOD BYE
```
* single quotes `''` and double quotes `""` stop word splitting by the shell
* `*?[]!` characters cause a word to be matched against path names
    * `*` matches 0 or more of any character, equivalent to regex `.*`
    * `?` matches any one character, equivalent to regex `.`
    * `[characters]` matches 1 of `characters`, equivalent to regex `[]`
    * `[!characters]` matches 1 character not in characters, equivalent to regex `[^]`
* I/O redirection
    * `< infile`: connects `stdin` to file `infile`
    * `> outfile`: send `stdout` to file `outfile`
    * `>> outfile`: append `stdout` to file `outfile`
    * `2> outfile`: sends `stderr` to file `outfile`
    * `22>> outfile`: append `stderr` to file `outfile`
    * `> outfile 2>&1`: send `stderr`+`stdout` to outfile
    * `1>&2`: send `stdout` to `stderr`

### PATH
* `echo PATH` outlines where program is searched

### Shell Scripts
* first line of `.sh` file: `#!/bin/sh`
* built in Shell variables:
    * `$0`: name of command
    * `$1`: first command line argument
    * `$2`: second command line argument
    * ...
    * `$#`: count of command line arguments
    * `$*`: command line arguments
    * `$@`: command line arguments
    * `"$*"`: all command line arguments with word splitting
    * `"$@"`: all command line arguments without splitting (use)
    * `$?`: exit status of most recent command
    * `$$`: process Id of shell
* Use `#` to comment

### Debugging Shell Scripts
* Use `echo` to print variables
* Add `set -x` to Shell script to see commands being executed
    * or equivalently run: `/bin/dash - x script.sh`

### `test`: Performs at test
* Provides useful operations:
    * string comparisons:
        * `=`
        * `!=`
    * numeric comparisons:
        * `-eq`
        * `-ne`
        * `-lt`
    * test file exists/executable/readable:
        * `-f`
        * `-x`
        * `-r`
    * boolean operations (and/or/not)
        * `-a`
        * `-o`
        * `!`
* Examples:

```shell
# does the variable msg have the value "Hello"?
test "$msg" = "Hello"

# does x contain a numeric value larger than y?
test "$x" -gt "$y"

# Error: expands to "test hello there = Hello"?
msg="hello there"
test $msg = Hello

# is the value of x in range 10..20?
test "$x" -ge 10 -a "$x" -le 20

# is the file xyz a readable directory?
test -r xyz -a -d xyz

# alternative syntax; requires closing ]
[ -r xyz -a -d xyz ]
```

### If Statements
```shell
if command1 
then
    then-commands 
elif command2
then
    elif-commands
    ...
else
    else-commands
fi
```
* Example:
```shell
if gcc a.c
then
    # you can not have an empty body
    # use a : statement which does nothing
    :
else
    rm a.c
fi
```

### While statements
```shell
while command
do
    body-commands
done
```
* Use `sleep` to slow down while loop, e.g. `sleep $repeat_seconds`

### For Statements
```shell
for var in word1 word2 word3
do
    body-commands
    ...
done
```
* Can use `break` and `continue`

### Exit Status for Conditional Execution
* all commands are executed if separated by `;`, e.g. `cmd1 ; cmd2 ; ... ; cmdn`
* execution stops if a command has a non-zero exit status if separated by `&&`, e.g. `cmd1 && cmd2 && ... && cmdn`
* execution stops if a command has a zero exit status, e.g: `cmd1 || cmd2 || ... || cmdn`
* `{}` can be used to group commands
* `()` can also be used to group commands, but executes them in a subshell

### Case Statements in Shell
```shell
case word in
pattern)
    commands1
    ;;
pattern2)
    commands2
    ;;
patternn)
    commandsn
esac
```
* Example:
```shell
#!/bin/dash
# written by andrewt@unsw.edu.au for COMP(2041|9044)
# demonstrate the simple use of read
echo -n "Do you like learning Shell?"
read answer
case "$answer" in
[Yy]*)
    response=":)"
    ;; 
[Nn]*)
    response=":("
    ;;
*)
    response="??" 
esac
echo "$response"
```
* Example:
```shell
#!/bin/dash
# written by andrewt@unsw.edu.au for COMP(2041|9044)
# over-simple /bin/cat emulation using read
# setting the special variable IFS to the empty trings 
# stops white space being stripped
for file in "$@"
do
    while IFS= read -r line 
    do
        echo "$line" 
    done <$file
done
```

### `read` - Shell Builtin
* Reads a line of input into variable(s)
    * Non-zero exit status on EOF
    * Newline is stripped
    * Leading and trailing whitespace stripped unless variable IFS unset

### Shell Functions
* Shell functions have this form:
```shell
function_name() {
    commands
}
```

* Example:

```shell
#!/bin/dash
# written by andrewt@unsw.edu.au for COMP(2041|9044) # demonstrate simple use of a shell function 
repeat_message() {
    n=$1
    message=$2
    for i in $(seq 1 $n) 
        do
        echo "$i: $message"
    done
}

i=0
while test $i -lt 4 
    do
    repeat_message 3 "hello Andrew"
    i=$((i + 1)) 
done
```

### Robust Creation and Removal of Temporary Files
* Use `trap` to ensure all temporary files are removed before the script exists
* `trap` specifies commands to be executed if a signal is received, below the signals received are interrupt, terminate or exit
```shell
TMP_FILE1=$(mktmp /tmp/plagiarism_tmp.1.XXXXXXXXXX)
TMP_FILE2=$(mktmp /tmp/plagiarism_tmp.2.XXXXXXXXXX)
trap `rm -f $TMP_FILE1 $TMP_FILE2; exit' INT TERM EXIT
```



