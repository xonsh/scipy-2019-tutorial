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

```bashcon
$ conda install -c conda-forge xonsh
```

<br/>

or, if you must, **pip**:

```bashcon
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

```bashcon
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

```bashcon
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

```bashcon
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

```bashcon
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

```bashcon
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

```bashcon
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

```bashcon
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
`@()`
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
