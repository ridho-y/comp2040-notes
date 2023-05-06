---
title: Filters
---

### General Notes
* Use `--` to represent that all arguments after it, are not options
    * e.g. `mv -- -source -dest` stops filename `-source` being interpreted as a flag

### `echo`: print arguments to stdout
```
echo <argument>
```
* Options:
    * `-n`: do not output a trailing newline
    * `-e`: enable interpretation of backlash escapes

### `cat`: Reads file to stdout
```
cat <file>
```
* Options:
    * `-n`: numbers the output lines (from 1)
    * `-A`: displays non-printing characters
    * `-s`: squeeze consecutive blank lines into single blank line

### `tac`: Same as `cat` but reverses order of lines (L)
```
tac <file>
```

### `rev`: Same as `cat` but reverses the order of characters in lines
```
rev <file>
```

### `grep`: Copies to stdout lines that match a regular expression
```
grep -E '<regularExpression>' <file>
```
* Options:
    * `-E`: extended grep, full power (use all the time)
    * `-i`: ignore case
    * `-v`: display lines that do not match the pattern
    * `-c`: print the number of matching lines
    * `-w`: only match pattern if it makes a complete word

### `wc`: Word counter
```
wc <file>
```
* Options:
    * `-c`: print number of characters
    * `-w`: print number of words
    * `-l`: print number of lines
* No option prints all three

### `tr`: Replace
```
tr '<sourceChars>' '<destChars>' <file>
```
* e.g `tr 'abc' '123' file.txt` changes:
    * `a -> 1`
    * `b -> 2`
    * `c -> 3`
* e.g `tr '[:upper:]' '[:lower:]'`
    * converts all upper to lower

### `head`: Prints the first 10 lines
```
head <file>
```
* Options:
    * `-n`: print the first `n` lines

### `tail`: Prints the last 10 lines
```
tail <file>
```
* Options:
    * `-n`: print the last `n` lines

### `cut`: Vertical slice
```
cut -d'<delimiter>' -f<column number(s), range> <file>
cut -f<column number> <file>
```
* Options:
    * `-f<listofCols>` prints only the specified fields (tab-separated)
    * `-c<listofPos>` prints only the chars in the specified positions
    * `-d<delimiter>`
* Lists are specified as ranges (e.g. 1-5) or comma-separated (e.g. 2,4,5)

### `sort`: Sort lines
```
sort <file>
sort -t'<delimiter>' -k<columnFrom>,<columnTo> <file>
sort -t'<delimiter>' -k6.5,6.7r <file>
```
* Options:
    * `-t<delimiter`: specify delimiter
    * `-k<columnFrom>,<columnTo>`: specify column range
    * `-c<columnFrom>.<charPosTo>,<columnTo>.<charPosTo>`: order within a field
    * `-n`: sort numerically than lexographically
* Can have multiple `-k`, which it orders by the order it is placed
* Example:
    * `sort -t'|' -k6.5,6.7r -k2,2 -k1,1 <file>`
    * Sorts by delimiter `|`, sorts by field 6 characters 5 to 7, then if they are the same sorts by column 2 to 2, then if they are the same sorts on column 1 or 1
    * The placement of the option `r` makes that its only applied to that `-k`. If it was `-kr6.5,6.7`, then all `-k`s are sorted reverse.

### `uniq`: Remove all but one copy of adjacent identical lines
```
uniq <file>
```
* Options:
    * `-c`: print number of times each line is duplicated
    * `-d`: print only one copy of duplicated lines
    * `-u`: only prints line that occur uniquely
* Often preceded by `cut`
* Almost always preceded by `sort`

### `sed`: Powerful editor
```
sed /'<word>'/d <file> (delete word)

sed /'<word1>'/'<word2'/d <file> (delete in between and including word1 and word2)

sed s/'<source>'/'<dest>'/g program.c (replace all instances of source with dest)

sed /'<word>'/p <file> (prints lines containing word)
```
* Options:
    * `p`: print the current line
    * `d`: delete the current line
    * `s/<regex>/<replace>/`: substitute first occurence of `regex` with `replace`
    * `s/<regex>/<replace>/g`: substitute all occurences of `regex` with `replace`
    * `q` terminate execution of sed
* Example 1:
    * `sed s/Dracula/Andrew/ig;s/Count/Emperor/g`
    * Replaces all occurences `Dracula` with `Andrew`
    * Replaces all occurences `Count` with `Emperor`
* Example 2:
    * `sed s/'#include "\(.*\)"'/'#include <\1>'/g`
    * Saves the stuff inside the brackets to `<\1>`
    * Must use escape brackets

### `find`: Search for files based on specified properties
```
find <location> <option> <parameter> <option> <parameter>
```
* Options:
    * `-name <file>`: type of files e.g. `<file> = '*.html'`
    * `-mtime -<time>`: files/dirs changed in the last `<time>` days
    * `-type <type>`: type is `f` (files) or `d` (directories)
    * `-exec <command> {} \;`: file management command e.g. `<command> = ls -l`
* `~` means home directory

### `join`: Merge two files using the values in a field in each file as a common key

### `paste`: Paste data

### `xargs`: Run commands with arguments from standard input
