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
name: startup-xonsh
# Install & Setup

.left-column[
  ## Install
  ## Setup
]
.right-column[
Now to startup xonsh, simply run the xonsh command,

```bashcon
$ xonsh
user@shell ~ $
```

]
---
# Install & Setup

.left-column[
  ## Install
  ## Setup
  ## Sanity Check
]
.right-column[
Now to check that everything is working, run `xonfig`,

```bashcon
$ xontrib
+------------------|----------------------+
| xonsh            | 0.9.6.dev33          |
| Git SHA          | 626b94db             |
| Commit Date      | Jun 28 17:20:28 2019 |
| Python           | 3.7.3                |
| PLY              | 3.11                 |
| have readline    | True                 |
| prompt toolkit   | 2.0.9                |
| shell type       | prompt_toolkit2      |
| pygments         | 2.4.2                |
| on posix         | True                 |
| on linux         | True                 |
| distro           | ubuntu               |
| on darwin        | False                |
| on windows       | False                |
| on cygwin        | False                |
| on msys2         | False                |
| is superuser     | False                |
| default encoding | utf-8                |
| xonsh encoding   | utf-8                |
| encoding errors  | surrogateescape      |
+------------------|----------------------+
```

]
---
class: center, middle
# Questions?
---
# Agenda

.bigger[
1. [Xonsh Language Basics](#xonsh-lang-basics)
2. [The Environment](#the-env)
3. [Callable Aliases](#callable-aliases)
4. [Events](#events)
5. [Macros](#macros)
6. [Advanced Confoguration](#adv-config)
7. [Case Study](#case-study)
]
---
class: center, middle, inverse
name: xonsh-lang-basics
# Xonsh Language Basics
---
name: xonsh-as-a-python-interpreter
# Xonsh as a Python Interpreter
.big[Xonsh is a superset Python 3.]

--

.big[Anything you can do in Python, you can also do in xonsh!]

--

.big[Including:

* Adding numbers together
* Opening Files
* Importing modules
* Defining functions & classes

]

--

.big[Let's try a few of these out!]
---
# Exercises
At the command line,

1. Compute the product of 2, 3, & 7.
   <details><pre><code class="python">$ 2 * 3 * 7
   42
   </code><pre></details>
2. Import NumPy (or `sys`, if you don't have NumPy installed)
   <details><pre><code class="python">$ import numpy as np
   $ np.arange(1, 6)
   array([1, 2, 3, 4, 5])
   </code><pre></details>
3. Define an `add()` function, which adds two numbers together
   and use it to sum together 2842 and 1400.
   <details><pre><code class="python">$ def add(x, y):
   .     return x + y
   .
   $ add(2842, 1400)
   4242
   </code><pre></details>

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
class: center, middle, inverse
name: xonsh-lang-basics
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

]

--

.right-column[

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

]

--

.right-column[

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

]

--

.right-column[

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

]

--

.right-column[

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

`$[]` and `![]` are the uncaptured subprocess expressions. They are very similar
to `$()` and `!()` with the notable difference that they stream output to
`stdout`.

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
```

]

--

.right-column[

```bash
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
  ## &nbsp;

]
.right-column[
<br/>
## "Curly Captures"
]

--


.right-column[

## "Square Streams"

]
---
# The Python-mode operator
.left-column[

 ## `@()`

]
--
.right-column[

The `@()` operator allows us to insert Python variables and values into
subprocess commands.

`xonsh` can always mix and match subprocess and Python commands without
additional syntax _if_ those commands are on separate lines, e.g.
]
--
.right-column[

```bash
$ for _ in range(2):
°     echo "Hi!"
°
Hi!
Hi!
```
]
---
# The Python-mode operator
.left-column[

 ## `@()`

]
.right-column[

But what about passing a variable to `echo` (or any other subprocess command?)
]
--
.right-column[

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
]
--
.right-column-tight[

```bash
$ for i in range(2):
°     echo @(i)
°
0
1
```
]
--
.right-column-tight[

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
]
--
.right-column-tight[

```bash
$ @("echo hello there".split())
hello there
```
]
--
.right-column-tight[

(strings are not split automatically)

```bash
$ @("echo hello there")
xonsh: subprocess mode: command not found: echo hello there
```

]
---
# Regex ticks

.left-column[

 ## ` `` `
]

.right-column[

Ready for super-charged file matching?
]
--
.right-column-tight[

You can wrap a regular expression in ` `` ` and it will return a list of
matching files and directories.
]
--
.right-column-tight[

```bash
$ `.*.md*`
['README.md', 'tutorial.md']
```
]
--
.right-column-tight[

The ticks are also a Python expression so you can use them in `for` loops, or
list-comprehensions, or whatever floats your boat.

```bash
$ [f.lower() for f in `.*.md`]
['readme.md', 'tutorial.md']
```

]

---
# Glob ticks

.left-column[

 ## ` `` `
 ## ` g`` `
]

.right-column[

If you prefer globs over regex, just prepend a `g` to your tick expression:

```bash
$ g`*.md`
['README.md', 'tutorial.md']
```
]
--
.right-column-tight[

Glob ticks also support recursive globbing with double `**`:

```bash
$ g`**/*.md`
```

]

---
# `xonsh` string literals

.left-column[

 ## `f""`

]
.right-column[

f-strings, or formatted string literals, are a part of Python 3.6+ and they are _great_.
]
--
.right-column-tight[

```bash
$ x = 5
$ print(f"x is {x}")
x is 5
```
]
--
.right-column-tight[

`xonsh` supports f-strings (so long as your underlying Python is 3.6+) and it
also has a few _extra_ tricks up its sleeves!

]
---
# path string literals

.left-column[

 ## `f""`
 ## `p""`

]
.right-column[

p-strings are a `xonsh` feature that allow easy construction of `pathlib.Path`s.
Any string that has a leading `p` becomes a `Path`.
]
--
.right-column-tight[

```bash
$ path = p"my_cool_folder"
$ path
PosixPath('my_cool_folder')
$ path.exists()
False
```
]
--
.right-column-tight[

If you haven't used `pathlib` before, take a moment to look through all of the
`Path` attributes and methods -- they're super useful!

Also try out using the `/` operator with a `pathlib.Path`.

**Note:** Your OS will determine what _sort_ of path object you get.

]
---
# formatted path string literals

.left-column[

 ## `f""`
 ## `p""`
 ## `pf""`

]
.right-column[

There are p-strings and f-strings, which leads to a natural question -- what
about pf-strings?
]
--
.right-column-tight[

```bash
$ home = "home"
$ user = "gil"
$ gitdir = "github.com"
$ p = pf"/{home}/{user}/{gitdir}"
$ p
PosixPath('/home/gil/github.com')
```
]
--
.right-column-tight[

But wait! Environment variables are also Python objects:
]
--
.right-column-tight[

```bash
$ p = pf"{$HOME}/{gitdir}"
$ p
PosixPath('/home/gil/github.com')
```

Pretty cool, huh?
]

---
# Exercises
---
# Questions
---
class: center, middle, inverse
name: the-env
# The Environment
---
# Typing & Detyping
---
# Environment Swapping
---
# Exercises
---
class: center, middle, inverse
name: callable-aliases
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
name: events
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
name: macros
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
name: advanced-configuration
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
name: case-study
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
