---
title: Python Notes
---

### General Notes
- Use `#!/usr/bin/env python3` as first line of file to run as `./<file name>`
- Python does not have arrays
    - The library `numpy` does have arrays though
- Python has lists, tuples and ranges
    - lists are mutable
    - tuples are immutable
    - ranges are immutable sequence of numbers used for loops

### Files
- `file = open('data.txt', '<option>`)
    - option can be `r` (read) or `w` (write) or `a` (append), and/or `b` (binary)
- `data = file.read()` gets the file as a string
- `file.write(data)` writes data to the file `data.txt`
- Opening a file may fail:
```python
try:
    file = open('data')
except OSError as e: 
    print(e)
```

### Function Arguments
```python
def f(*args, **kwargs): 
    print('positional arguments:', args) 
    print('keywords arguments:', kwargs)

>>> f("COMP", 2041, 9044, answer=42, option=False)
positional arguments: ('COMP', 2041, 9044)
keywords arguments: {'answer': 42, 'option': False}
```
```py
>>> arguments = ['Hello', 'there', 'Andrew']
>>> keyword_argments = {'end' : '!!!\n', 'sep': ' --- '}
>>> print(arguments, keyword_argments)
['Hello', 'there', 'Andrew'] {'end': '!!!\n', 'sep': ' --- '}
>>> print(*arguments, **keyword_argments)
Hello --- there --- Andrew!!!
```

### List Comprehension
```py
>>> [x**3 for x in range(10)]
[0, 1, 8, 27, 64, 125, 216, 343, 512, 729]
>>> [str(round(math.pi, digits)) for digits in range(1,7)]
['3.1', '3.14', '3.142', '3.1416', '3.14159', '3.141593']
>>> [x**3 for x in range(10) if x % 2 == 1]
[1, 27, 125, 343, 729]
```

### Lambda Expression
- Helps with the creation of small anonymous functions

```py
>>> f = lambda x: x + 42 >>> type(f)
<class 'function'>
>>> f(12)
54
```

### `enumerate()`
```py
>>> languages = ['C', 'Python', 'Shell', 'Rust']
>>> list(enumerate(languages))
[(0, 'C'), (1, 'Python'), (2, 'Shell'), (3, 'Rust')]
>>> list(enumerate(languages, start=42))
[(42, 'C'), (43, 'Python'), (44, 'Shell'), (45, 'Rust')]
```

### `zip()`
```py
>>> languages = ['C', 'Python', 'Shell', 'Rust']
>>> editors = ['vi', 'emacs', 'atom', 'VScode', 'nano']
>>> list(zip(editors, languages))
[('vi', 'C'), ('emacs', 'Python'), ('atom', 'Shell'), ('VScode', 'Rust')]
```

### `map()`
- Lambda expression must perform a function to every value in the list
```py
>>> list(map(str, range(10)))
['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
>>> list(map(lambda x: x**3, range(10)))
[0, 1, 8, 27, 64, 125, 216, 343, 512, 729]
>>> list(map(lambda x, y: x**y, range(10), range(10)))
[1, 1, 4, 27, 256, 3125, 46656, 823543, 16777216, 387420489]
```

### `filter()`
- Lambda expression must be return true or false
```py
>>> list(filter(lambda x: x % 2 == 0, range(10))) 
[0, 2, 4, 6, 8]
```

### `sorted()`
```py
DAY_LIST = "Sunday Monday Tuesday Wednesday Thursday Friday Saturday".split() 
DAY_NUMBER = dict((day, number) for number, day in enumerate(DAY_LIST))
def random_day_of_week():
    return random.choice(DAY_LIST) 
def sort_days0(day_list):
    return sorted(day_list, key=lambda day: DAY_NUMBER[day]) 
def sort_days1(day_list):
    return sorted(day_list, key=DAY_NUMBER.get)
```
```py
# Sort by length of item, then if same, alphabetical order ~
list = sorted(list, key=lambda item: (len(item), item))
```

### Python Environments
- Create a virtual environment: `python3 -m venv <venv_name>`
- To activate the environment: `. <venv_name>/bin/activate`

### `sys` module
- `sys.exit(1)` to terminate program with exit status 1
- `sys.argv[]` for arguments
- `sys.argv[]` for first argument
- `sys.stdin` gets the standard input
- Examples:
```python
import sys
line_count = 0
for line in sys.stdin:
    line_count += 1
print(line_count, "lines")
```
```python
import sys
lines = sys.stdin.readlines()
line_count = len(lines)
print(line_count, "lines")
```
```python
import sys
lines = list(sys.stdin)
line_count = len(lines)
print(line_count, "lines")
```

### `subprocess` module
- `output = subprocess.getoutput(<shell command>)`
- `p = subprocess.run([<shell command>])`
    - each word of shell command must be a part of a list
    - `p.returncode` gives the return status
    
```python
>>> p = subprocess.run(["date"], capture_output=True, text=True) 

>>> p.stdout
'Mon 18 Jul 2022 10:27:28 AEST\n'

>>> p.returncode
0

>>> q.stderr
"ls: cannot access 'no-existent-file': No such file or directory\n" ]

>>> q.returncode
2
```
```python
subprocess.run("sort *.csv | cut -d, -f1,7 >output.txt", shell=True)
```

```
p = subprocess.run(["curl", "--silent", url], text=True, stdout=subprocess.PIPE)
webpage = p.stdout
```

### `fileinput` module
- Typical use:
```python
for line in fileinput.input():
    process(line)
```
```python
with fileinput.input(files=('spam.txt', 'eggs.txt')) as f:
    for line in f:
        process(line)
```

### `urllib` module
```python
while True:
response = urllib.request.urlopen(url) webpage = response.read().decode()
if not re.search(regexp, webpage):
        time.sleep(REPEAT_SECONDS)
continue
mail_body = f"Generated by {sys.argv[0]}"
subject = f"website '{url}' now matches regex '{regexp}'"
# the echo is for testing, remove to really send email subprocess.run (["echo", "mail", "-s", subject], text=True, input=mail_body) sys.exit(0)
```

### `BeautifulSoup` module
```python
import bs4 as BeautifulSoup
IGNORE_WEBPAGE_ELEMENTS = set("[document] head meta style script title".split()) 
for url in sys.argv[1:]:
    response = urllib.request.urlopen(url)
    webpage = response.read().decode()
    soup = BeautifulSoup.BeautifulSoup(webpage, "lxml") 
    for element in soup.findAll(text=True):
        parent = element.parent.name.lower() 
        if parent in IGNORE_WEBPAGE_ELEMENTS:
            continue
        text = element.getText()
        # remove empty lines and leading whitespace text = re.sub(r"\n\s+", "\n", element)
        text = text.strip()
        if text:
            print(text)
```