class: center, middle, inverse

# xonsh

Bringing Python data science to your shell!

![](/ascii_conch_part_transparent_tight.png)
---
layout: false
# Install & Setup

.left-column[
  ## Install
]
.right-column[
  First, let's install xonsh (if you haven't already).

**conda**:

```bash
$ conda install -c conda-forge xonsh
```

<br/>

or, if you must, **pip**:

```bash
$ pip install "xonsh[pygments,ptk,<linux|mac|win>]"
```

.footnote[.red[*] Also, make sure you are on Python 3]
]
---
layout: false
# Install & Setup

.left-column[
  ## Install
  ## Setup
]
.right-column[
Now to startup xonsh, simply
]
---
# Sanity Checks
---
# Questions?
---
# Agenda

1. Introduction
2. Deep-dive
3. ...

[NOTE]: Note that you need active internet connection to access remark.js script file
---
class: center, middle, inverse

# Xonsh Language Basics
---
# Xonsh as a Python Interpreter
---
# Exercises
---
# Xonsh as a Shell
---
# Running Commands
---
# Environment Variables
---
# The `source` command
---
# `source-foreign`
---
# The `xonshrc` configuation file
---
# Exercises
---
# Mixing Python and Subprocess Modes
---
# Subprocess operators

`xonsh` makes using `subprocess` simple.

---
# Captured subprocesses

.left-column[
  ## `$()`
]
.right-column[
`$()` captures the standard output of a command and returns it as a string.

```bash
$ x = $(ls)
$ x
'LICENSE\nREADME.md\nascii_conch_part_transparent_tight.png\ndefault.css\nfavicon.ico\nremote.html\ntutorial.md\n'
$ print(x)
LICENSE
README.md
ascii_conch_part_transparent_tight.png
default.css
favicon.ico
remote.html
tutorial.md
```
]


---
# Captured subprocesses

.left-column[
  ## `$()`
]
.right-column[
And it really is a `str` with all the methods you would expect:

```bash
$ x.upper()
'LICENSE\nREADME.MD\nASCII_CONCH_PART_TRANSPARENT_TIGHT.PNG\nDEFAULT.CSS\nFAVICON.ICO\nREMOTE.HTML\nTUTORIAL.MD\n'
$ x.split("\n")
['LICENSE',
 'README.md',
 'ascii_conch_part_transparent_tight.png',
 'default.css',
 'favicon.ico',
 'remote.html',
 'tutorial.md',
 '']
```
]


---
# Captured subprocesses

.left-column[
  ## `$()`
  ## `!()`
]
.right-column[
`!()` captures command output and a lot of other info, too!

Beyond the output, `!()` offers the return code, process id, `stderr` and
`stdout` streams, whether the `cmd` is an alias, timestamps, and more!

```bash
$ x = !(ls)
$ x
CommandPipeline(stdin=<_io.BytesIO object at 0x7fc0d01be4c0>, stdout=<_io.BytesIO object at 0x7fc0d01be6d0>, stderr=<_io.BytesIO object at 0x7fc0d01bea40>, pid=16969, returncode=0, args=['ls'], alias=['ls', '--color=auto', '-v'], stdin_redirect=['<stdin>', 'r'], stdout_redirect=[10, 'wb'], stderr_redirect=[12, 'w'], timestamps=[1561758574.868479, 1561758575.6660624], executed_cmd=['/usr/bin/ls', '--color=auto', '-v'], input=, output=LICENSE
README.md
ascii_conch_part_transparent_tight.png
default.css
favicon.ico
remote.html
tutorial.md
, errors=None)
```

]


---
# Captured subprocesses

.left-column[
  ## `$()`
  ## `!()`
]
.right-column[
`!()` returns an instance of a `CommandPipeline` object -- this object is
"truthy" if the command completed successfully:

```bash
$ bool(!(ls .))
True
$ bool(!(ls nothingtoseehere))
/usr/bin/ls: cannot access 'nothingtoseehere': No such file or directory
False
```

]

---
# Captured subprocesses

.left-column[
  ## `$()`
  ## `!()`
]
.right-column[
You can iterate over the output from `!()` line-by-line:

```bash
$ for i, loc in enumerate(!(ls)):
°     print(f"{i}th: {loc.strip()}")
°
°
0th: LICENSE
1th: README.md
2th: ascii_conch_part_transparent_tight.png
3th: default.css
4th: favicon.ico
5th: remote.html
6th: tutorial.md
```

]

---
# Uncaptured subprocesses

.left-column[
  ## `$()`
  ## `!()`
  ## `$[]`
]
.right-column[
`$[]` and `![]` are the uncaptured subprocess expressions. They are very
similar to `$()` and `!()` with the notable difference that they stream output
to `stdout`.

]

---
# Uncaptured subprocesses

.left-column[
  ## `$()`
  ## `!()`
  ## `$[]`
]
.right-column[
`$[]` always returns `None`.

You may be asking, what is this even for?
More on that later, but the short answer is that it allows you to force `xonsh`
to recognize a command as a subprocess command if the context is ambiguous.

```bash
$ x = $[ls .]
LICENSE    ascii_conch_part_transparent_tight.png  favicon.ico  tutorial.md
README.md  default.css
```

]

---
# Uncaptured subprocesses

.left-column[
  ## `$()`
  ## `!()`
  ## `$[]`
  ## `![]`
]
.right-column[
`![]` streams command output to `stdout` but also returns an instance of a `HiddenCommandPipeline` object.

```bash
$ x = ![ls .]
LICENSE    ascii_conch_part_transparent_tight.png  favicon.ico  tutorial.md
README.md  default.css                             remote.html
$ x.args
['ls', '.']

$ x.timestamps
[1561759961.047233, 1561759961.0570006]

$ x.alias
['ls', '--color=auto', '-v']

```

]

---
# Subprocess Operators: A helpful mnemonic(?)

.left-column[
  ## `$()`
  ## `!()`
  ## `$[]`
  ## `![]`
  ##

]
.right-column[

<br>

## "Curly Captures"

<br><br>

## "Square Streams"

]
---
# The Python-mode operator
.left-column[

 ## `@()`

]
.right-column[

The `@()` operator allows us to insert Python variables and values into
subprocess commands.

`xonsh` can always mix and match subprocess and Python commands without additional syntax _if_ those commands are on separate lines, e.g.

```bash
$ for _ in range(2):
°     echo "Hi!"
°
Hi!
Hi!
```

But what about passing a variable to `echo` (or any other subprocess command?)

```bash
$ for i in range(2):
°     echo i
°
i
i
```

]
---

# The Python-mode operator
.left-column[

 ## `@()`

]
.right-column[

The `@()` operator evaluates arbitrary Python expressions and returns the result
as string. This result can be fed directly to a subprocess command:

```bash
$ for i in range(2):
°     echo @(i)
°
0
1
```

If the output is a non-string sequence (`list`, `set`, etc.) then the results
are joined and returned as a string.

```bash
$ echo @([x for x in range(3)])
0 1 2
```

]

---

# The Python-mode operator
.left-column[

 ## `@()`

]
.right-column[

If the result of `@()` is in the first position it is treated as an alias:

```bash
$ @("echo hello there".split())
hello there
```

(strings are not split automatically)

```bash
$ @("echo hello there")
xonsh: subprocess mode: command not found: echo hello there
```

]
---
# Globbing

Glob ticks and regex ticks

---
# Path string literals

p-strings, f-strings and pf-strings
---
# Exercises
---
# Questions
---
class: center, middle, inverse
# The Environment
---
# Typing & Detyping
---
# Environment Swapping
---
# Exercises
---
class: center, middle, inverse
# Callable Aliases
---
# Function signatures
---
# Adding to pipelines
---
# Exercises
---
class: center, middle, inverse
# Break
---
class: center, middle, inverse
# Events
---
# Event Handlers
---
# Event Managment
---
# Registration
---
# When events are fired
---
# Transmogrify: Normal events vs Load events
---
# Example Events
---
# Exercises
---
class: center, middle, inverse
# Macros
---
# What is a Macro
---
# Function Macros
---
# Subprocess Macros
---
# Context Macros
---
# Exercises
---
class: center, middle, inverse
# Advanced Configuration
---
# xontribs
---
# Tips & Tricks
---
# xontribs
---
# lazy imports
---
# Examples
---
class: center, middle, inverse
# Case Study
A Composable Machine Learning Tool
---
#
---
#
---
#
---
#
---
#
