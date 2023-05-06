---
title: Regex Notes
---

### Regular Expressions
* `pattern1|pattern2` denotes the union of `pattern1` and `pattern2` (OR)
* `()` used for grouping
* `\` removes the special meaning of the following character
* `.` matches any single character
* `[listofcharacters]` matches any single character from `listofcharacters`
    * e.g. `[A-Za-z]` matches
* `[^listofcharacters]` matches any single character except from `listofcharacters`
    * e.g. `[^a-e]` matches any single character other than `a` to `e`
* `^` denotes start of the string
* `$` denotes end of the string
* `p*` denotes zero or more repetitions of `p`
* `p+` denotes one or more repetitions of `p`
* `p?` denotes zero or one occurence of `p`
* `p{n}` denotes `n` repeitions of `p`
* `p{n,m}` denotes `n` to `m` repetitions of `p`
* `p{n,}` denotes `n` or more repetitions of `p`
* `p{,m}` denotes `m` or less repetitions of `p`

### Regex on Python
- `import re`
- New character classes:
    - `\d` - matches any digit, for ASCII: `[0-9]`
    - `\D` - matches any non-digit, for ASCII `[^0-9]`
    - `\w` - matches any word char, for ASCII: `[a-zA-Z_0-9]`
    - `\W` - matches any non-word char, for ASCII: `[^a-zA-Z_0-9]`
    - `\s` - matches any whitespace, for ASCII: `[ \t\n\r\f]`
    - `\S` - matches any non-whitespace, for ASCII: `[^ \t\n\r\f]`
    - `\b` - matches at a word boundary
    - `\B` - matches except at a word boundary
    - `\A` - matches at the start of the string, same as `^`
    - `\Z` - matches at the end of the string, same as `$`

### Raw Strings
- Python raw-strings are prefixed with an `r` (for raw)
- Backslashes have no special meaning in raw strings
- Regexes often contain backslashes - using raw-strings makes them more readable

### `re.search(<regex>, <string>, <flags>)`
- Search for a regex match anywhere within the string
- Returns a match object if suceeds, `None` if fails, therefore can control `if` or `while`
- Case-insensitive flag: `flags=re.I`

```py
>>> m = re.search(r'[aiou].*[aeiou]', 'pillow')
>>> m
<re.Match object; span=(1, 5), match='illo'>
>>> m.group(0)
'illo'
>>> m.span()
(1, 5)
>>>
```

- Group capturing:

```py
>>> m = re.search('(\w+)\s+(\w+)', 'Hello Andrew')
>>> m.groups()
('Hello', 'Andrew')
>>> m.group(1)
'Hello'
>>> m.group(2) 'Andrew'
```
- Matching same thing twice

```py
>>> re.search(r'^(\d+) (\d+)$', '42 43')
<re.Match object; span=(0, 5), match='42 43'>
>>> re.search(r'^(\d+) (\1)$', '42 43')
>>> re.search(r'^(\d+) (\1)$', '42 42')
<re.Match object; span=(0, 5), match='42 42'>
```
- Non-capturing group
    - Use `(?:...)`

```py
>>> m = re.search(r'.*(?:[aeiou]).*([aeiou]).*', 'abcde')
>>> m
<re.Match object; span=(0, 5), match='abcde'>
>>> m.group(1)
'e'
```

### `re.match(<regex>, <string>, <flags>)`
- Only matches at the start of the string
- Same as `re.search` starting with `^`
- Returns a match object if suceeds, `None` if fails, therefore can control `if` or `while`
- Case-insensitive flag: `flags=re.I`

```py
print("Destroy the file system? ")
answer = input()
if re.match(r'yes|ok|affirmative', answer, flags=re.I):
   subprocess.run("rm -r /", Shell=True)
```

### `re.fullmatch(<regex>, <string>, <flags>)`
- Only match the full string
- Same as `re.search` starting with `^` and ending with `$`
- Returns a match object if suceeds, `None` if fails, therefore can control `if` or `while`
- Case-insensitive flag: `flags=re.I`

### `re.sub(<regex>, <replacement>, <string>, <count>, <flags>)`
- Return a string with anywhere regex matches, substituted by replacement
- Optional parameter count, the max number of substitutions
```py
re.sub(r'(\d+) and (\d+)', r'\2 or \1', "The answer is 42 and 43?") 
'The answer is 43 or 42?'
```
- Greedy vs non-greedy match
    - Greedy: Starts match at the first place it can suceed and makes the match as long as possible
    - Non-greedy (use `?`): Starts match the first place it can suceed and makes the match as short as possible
```py
>>> s = "abbbc"
>>> re.sub(r'ab+', 'X', s) 'Xc'
>>> re.sub(r'ab+?', 'X', s) 'Xbbc'
```

### `re.findall(<regex>, <string>, <flags>)`
- Returns a list of matched strings
```py
>>> re.findall(r'\d+', "-5==10zzz200_")
['5', '10', '200']
>>> re.findall(r'(\d)\d*', "-5==10zzz200_")
['5', '1', '2']
 >>> re.findall(r'(\d)\d*(\d)', "-5==10zzz200_")
[('1', '0'), ('2', '0')]
>>> re.findall(r'([^,]*), (\S+)', "Hopper, Grace Brewster Murray")
[('Hopper', 'Grace')]
>>> re.findall(r'([A-Z])([aeiou])', "Hopper, Grace Brewster Murray")
[('H', 'o'), ('M', 'u')]
```

### `re.split(<regex>, <string>, <flags>)`
- Splits a string where the regex matches

```py
>>> re.split(r'\d+', "-5==10zzz200_")
['-', '==', 'zzz', '_']
>>> re.split(r'\s*,\s*', "abc,de, ghi   ,jk   , mn")
['abc', 'de', 'ghi', 'jk', 'mn']

```