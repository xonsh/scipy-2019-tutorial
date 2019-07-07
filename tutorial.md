class: center, middle, inverse

# xonsh

Bringing Python data science to your shell!

![](/ascii_conch_part_transparent_tight.png)
---
layout: false
# Install & Setup

.left-column[
  ## Install 📩
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
  ## Install 📩
  ## Setup 🧗
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
  ## Install 📩
  ## Setup 🧗
  ## Sanity Check 🔩
]
.right-column[
Now to check that everything is working, run `xonfig`,

```bashcon
$ xonfig
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

<div style="text-align:center;">
<img src="questionsnail.jpg" style="width:300px;"/>
</div>
---
# Agenda

.bigger[
1. [Xonsh Language Basics](#xonsh-lang-basics)
2. [The Environment](#the-env)
3. [Callable Aliases](#callable-aliases)
4. [Events](#events)
5. [Macros](#macros)
6. [Advanced Configuration](#adv-config)
7. [Case Study](#case-study)
]

<div style="text-align:center;">
<img src="knight-vs-snail.jpg" style="width:600px;"/>
</div>
---
class: center, middle, inverse
name: xonsh-lang-basics
# Xonsh Language Basics
---
name: xonsh-as-a-python-interpreter
# Xonsh as a Python Interpreter 🐍
.big[Xonsh is a superset Python 3: 🐌⊇🐍]

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
<div style="text-align:center">
.large[🤹]
</div>
---
# Exercises ⛹️‍♀️
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
.big[Xonsh is also a **shell language** 🐚]

--

.big[More exactly, xonsh is a **scripting language** that is *mostly*
compatible with [Unix sh-lang](https://en.wikipedia.org/wiki/Bourne_shell).red[*].]

.footnote[.red[*] If you don't know `sh`, don't worry! The point of
xonsh is to replace the hard-to-learn, hard-to-remember bits of `sh`
with Python.]

--

> .big["Why isn't xonsh also a sh-lang superset?"]

--

.big[It is impossible to be both a Python superset **and** a `sh` superset,
and we have one simple rule:]

--

.center[.bigger[**Python always wins!**] .large[<br/>🥇🐍🥇]]

---
# Running Commands

.big[The purpose of a shell, of course, is to run commands!🏃🏾‍♀️]

--

```bashcon
# a simple echo
$ echo "Shello, World!"
Shello, World!

# create a file in a new directory
$ mkdir -p snail
$ cd snail
$ touch deal-with-it.txt
$ ls
deal-with-it.txt

# simple piping example
$ echo "Wow Mom!" | grep o
Wow Mom!
```

--

.big[Under the covers, xonsh is using Python's `subprocess` module.]

---
# Environment Variables 🌍

Environment variables are written as a `$` followed by a name.

```bashcon
$ $HOME
'/home/it-me'
```

--

You can set (and export) environment variables with normal Python syntax:

```pythoncon
$ $GOAL = "don't panic"
$ print($GOAL)
don't panic
$ del $GOAL
```

--

Coming from Python, these should look and feel like any other variable,
except that they live in the environment.

---
# Environment Lookup with `${expr}` 🔎

Sometimes you might not know the name of the environment variable,
or you might want to compute the name programatically, or maybe it
has special characters in it.

--

In these cases, xonsh provides the `${expr}` syntax.

--

This lets you use a Python expression to lookup the value of the
environment variable.

--

```pythoncon
$ x = "USER"
$ ${x}
'it-me'
$ ${"HO" + "ME"}
'/home/it-me'
```

--

🚨 In sh-langs, `$NAME` and `${NAME}` mean the same thing.<br/>
🚨 In xonsh, they have distinct meanings.

---
# The Environment Itself `${...}`

If you ever need access to the environment object, you can grab it by
passing in an ellipsis as the lookup expression, i.e. `${...}`.

--

```python
$ ${...}
xonsh.environ.Env(
{'AUTO_CD': False,
 'AUTO_PUSHD': False,
 'AUTO_SUGGEST': True,
 'AUTO_SUGGEST_IN_COMPLETIONS': False,
 'BASH_COMPLETIONS': EnvPath(
['/usr/share/bash-completion/bash_completion']
),
 'BASH_COMPLETION_USER_DIR': '/home/scopatz/miniconda/share/bash-completion',
 'BOTTOM_TOOLBAR': '',
 ...
})
```

--

This is a reference to the object that lives at `__xonsh__.env`.

--

You can use this object to test if a variable exists:

```bash
$ "PATH" in ${...}
True
```

---
# The `source` command 🌄

The `source` command:

1. Reads in the contents of a file,
2. Executes it, and
3. Brings all global variables into the current execution context.

--

**example.xsh**:

```python
$EMAIL = "snail@xon.sh"

password = "swordfish"

def combine():
    return $EMAIL + ":" + password
```

--

Running this:

```bashcon
$ source example.xsh
$ combine()
'snail@xon.sh:swordfish'
```

As Python, you could also `import example`, with the normal Python rules.

???

Context is tied correctly

```bash
$ password = "monkey"
$ combine()
'snail@xon.sh:monkey'
```

---
# `source-foreign` 🛸

Unlike other languages, xonsh enables you to source scripts written in
shells other than xonsh (or Python).

--

The most common of these is Bash.

**example.sh**

```bash
export WAKKA="jawaka"

function now_playing {
  echo "$(whoami) on drums!"
}
```

--

Running this with `source-foreign bash` or the `source-bash` shortcut:

```bashcon
$ source-bash example.sh
$ $WAKKA
'jawaka'
$ now_playing
it-me on drums!
```

--

Also stock support for `source-zsh` and `source-cmd`!

---
# The `xonshrc` configuation file 🔧

.big[Xonsh's main config file is located at `~/.xonshrc`.]

--

.big[This is a special `*.xsh` file that is loaded up before
almost anything else.]

--

.big[This is where your customizations go, and it is often used to
set environment variables.]

--

**~/.xonshrc**

```bash
$MULTILINE_PROMPT = '`·.,¸,.·*¯`·.,¸,.·*¯'
$XONSH_SHOW_TRACEBACK = True
```

--

.big[A complete listing of environment variables that xonsh knows about
is available at [https://xon.sh/envvars.html](https://xon.sh/envvars.html)]

---
# Exercises 🚴‍♂️

1. Set a random integer to the environment variable `$SECRET`.
   <details><pre><code class="python">import random
   $SECRET = random.randint(1, 42)
   </code><pre></details>
2. Print the secret value after the phrase `My secret value is:`
   <details><pre><code class="python"># Solution 1
   $ echo "My secret value is $SECRET"

   # Solution 2
   $ print("My secret value is", $SECRET)
   </code><pre></details>
3. In your xonshrc, generate a secret value. Print this value
   when xonsh starts up if another `$SAFE` environment variable
   does not exist.
   <details>
   <b>~/.xonshrc</b>

   <pre><code class="python">import random
   $SECRET = random.randint(1, 42)
   if "SAFE" not in ${...}:
       echo "My secret value is $SECRET"
   </code><pre></details>


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

And it's a `str` with all the methods you would expect:

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

`$[]` and `![]` are the uncaptured subprocess expressions. They are similar
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
.right-column-tight[
`![]` streams command output to `stdout` but also returns an instance of a `HiddenCommandPipeline` object.

```bash
$ x = ![ls .]
LICENSE    ascii_conch_part_transparent_tight.png  favicon.ico  tutorial.md
README.md  default.css                             remote.html
```

]

--

.right-column-tight[

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
# Uncaptured subprocesses exercises 🧘‍♂️

.left-column[
  ## `$()`
  ## `!()`
  ## `$[]`
  ## `![]`
]
.right-column-tight[

Take a few minutes to try out each of the subprocess operators.

Use xonsh's tab completion to explore the attributes of the `CommandPipeline`s
and get a sense of what information is in there.

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

If you prefer globs over regex, prepend a `g` to your tick expression:

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

Cool, huh?
]

---
# Exercises 🏋️‍♀️



---
# Questions
---
class: center, middle, inverse
name: the-env
# The Environment

---
# Environment

All of `xonsh`'s environment variables live in `__xonsh__.env`.

You can also access the environment using `${...}`.

--

You can use the membership operator to determine if an environment variable is
present in your current session.

```bash
$ 'HOME' in ${...}
True
```

--

If you want more information about a certain environment variable in `xonsh`,
you can ask for help!

```bash
$ ${...}.help("AUTO_CD")
AUTO_CD:

Flag to enable changing to a directory by entering the dirname or full path
only (without the cd command).

default: False
configurable: True
```

---
# Typing & Detyping

`xonsh` environment variables are Python objects. This means they also have
types like Python objects. Sometimes these types are imposed based on a variable
name.

--

Any env-var matching `\w*PATH` will be of type `EnvPath`, like

* `PATH`
* `LD_LIBRARY_PATH`
* `RPATH`

--

Other variables are boolean, others are ints. Whatever their type, in `xonsh`
you always have access to the true object, not a string representation.

---
# Detyping

`xonsh` automatically converts variables to strings whenever it feeds them to
subprocess commands.
You can also explicitly request detyped variables:

--

```bash
$ ${...}.get("PATH")
EnvPath(
['/opt/miniconda3/bin/',
 '/usr/bin',
 '/usr/local/bin',
 '/usr/bin/vendor_perl/',
 '/usr/bin/core_perl/']
)
```

--

```bash
$ ${...}.detype().get("PATH")
'/opt/miniconda3/bin/:/usr/bin:/usr/local/bin:/usr/bin/vendor_perl/:/usr/bin/core_perl/'
```

---
# Environment Swapping

There are a couple of other useful methods on `${...}`, in particular, the
`swap()` method is useful for temporarily setting environment variables.

--

```bash
$ with ${...}.swap(SOMEVAR='foo'):
°     echo $SOMEVAR
°
foo
$ echo $SOMEVAR
$SOMEVAR
```

---
# Exercises 🤸‍♂️

1. Try using `getpass.getpass()` with `swap()` to set your "password" in an environment variable temporarily.

   <details><pre><code class="python">
   $ import getpass
   $ with ${...}.swap(PASS=getpass.getpass()):
   °     echo @(f"git push https://gil:{$PASS}@github.com/xonsh/xonsh master")
   °
   Password:
   git push https://gil:hunter2@github.com/xonsh/xonsh master

   $ echo $PASS
   $PASS
   </code><pre></details>

2. A `curl` binary in your `(conda|homebrew)` install is messing with your
built-in package manager. It would be handy if you could remove the first entry
of your `$PATH` but only to run the one install command...

   <details><pre><code class="python">
   $ echo $PATH
   /opt/miniconda3/bin/:/usr/bin:/usr/local/bin:/usr/bin/vendor_perl/:/usr/bin/core_perl/
   $ with ${...}.swap(PATH=$PATH[1:]):
   °     echo "sudo pacman -S pinentry"
   °     echo $PATH
   °
   sudo pacman -S pinentry
   /usr/bin:/usr/local/bin:/usr/bin/vendor_perl/:/usr/bin/core_perl/

   $ echo $PATH
   /opt/miniconda3/bin/:/usr/bin:/usr/local/bin:/usr/bin/vendor_perl/:/usr/bin/core_perl/
   </code><pre></details>

---
class: center, middle, inverse
name: callable-aliases
# Callable Aliases
---
# Callable Aliases

.big[We've now seen how to exchange code between Python and subprocess mode.]

--

.big[But that's boring 💤💤💤]

--

.big[Let's fully integrate Python into a data-processing pipeline by
allowing functions (callables) to be executed in subprocess mode...]

--

.big[...like any other command!]

<div style="text-align:center;">
<img src="large_surfin_turtle__24434.1458626022.webp" style="width:200px;"/>
</div>

---
# Callable Aliases

.big[This works for any Python function that adheres to a certain set of
known signatures. (So not *any* function.)]

--

.big[These are known as **callable aliases** because they are often placed
in the builtin `aliases` dictionary, along side the more normal aliases.]

--

.big[By placing them in `aliases`, they are executable from anywhere, as with
normal subproess commands and aliases.]

---
# Function signatures: Nothing!

In the simplest case, the alias takes no arguments and returns a string
or an integer return code.

--

```python
$ aliases['banana'] = lambda: "Banana for scale.\n"
$ banana
Banana for scale.
```

--

We can pipe this to a normal command:

```bashcon
$ banana | wc -w
3
```

--

And if we want to destoy the alias:

```python
$ del aliases['banana']
```

---
# Function signatures: The Loneliest

Callable aliases may also optionally take command-line options
as the first positional argument.

--

These come in as a list of strings, like `sys.argv`.

--

```python
def apple(args):
    if len(args) == 1:
        print("all alone with " + args[0])
        return 0
    else:
        print("too much company!\n- " + "\n- ".join(args))
        return 1
```

--

The callable alias needs to be the first element of a command:

```bash
$ @(apple) core
all alone with core

$ @(apple) core seed
too much company!
- core
- seed
```

---
# Function signatures: Fourtified

Standard streams are also able to be passed in as keyword
arguments whose defaults are `None`.

--

These are file open objects with all of the usual Python interfaces.

--

```python
def _grape(args, stdin=None, stdout=None, stderr=None):
    for line in stdin:
        stdout.write("Grape says '" + line.strip().lower() + "'\n")
    return 0

aliases["grape"] = _grape
```

--

<u>usage</u>:

```bash
$ echo WrATh | grape
Grape says 'wrath'
```

--

Note that not all streams must be passes in, but the prior streams must
be passed in if the latter ones are. For example, if you want to use
`stdout` you have to accept `stdin` but not `stderr`.

---
# Function signatures: A Kiwious Spectacle

The command specification `spec` is available as the next keyword argument.

--

This is [a rich Python object](https://xon.sh/api/built_ins.html#xonsh.built_ins.SubprocSpec)
that represents the metadata about the callable alias that is running.

--

For example, if the alias wants to add a newline if it is uncaptured,
but ignore the newline otherwise, you need the `captured` attr of the spec.

```python
def _kiwi(args, stdin=None, stdout=None, stderr=None, spec=None):
    import xonsh.proc
    if spec.captured in xonsh.proc.STDOUT_CAPTURE_KINDS:
        end = ''
    else:
        end = ' (newline)\n'
    print('Hi Mom!', end=end)
aliases["kiwi"] = _kiwi
```

--


```bash
$ kiwi  # uncaptured
Hi Mom! (newline)


$ $(kiwi)  # captured
'Hi Mom!'
```

---
# Function signatures: Framed!

The final form takes a `stack` argument as well.

--

This is a list of [`FrameInfo`](https://docs.python.org/3/library/inspect.html#the-interpreter-stack)
instances, and is for the truly maniacal.

--

The `stack` argument provides all the alias with everything it could possible
want to know about its call site.

--

```python
def lemon(args, stdin=None, stdout=None, stderr=None, spec=None, stack=None):
    for frame_info in stack:
        frame = frame_info[0]
        print('In function ' + frame_info[3])
        print('  locals', frame.f_locals)
        print('  globals', frame.f_globals)
        print('\n')
    return 0
```

--

.center[.bigger[Please `stack` responsibly]]

---
# Exercises 🤾‍♀️

1. Write a callable alias `frankenstein`, which provides the
   content of Mary Shelley's classic novel,
   [full text here](https://www.gutenberg.org/files/84/84-0.txt).
   <details><pre><code class="python"># short version
   aliases["frankenstein"] = lambda: $(curl https://www.gutenberg.org/files/84/84-0.txt)

   # streaming version
   def _frankenstein(args, stdin=None, stdout=None):
      for line in !(curl https://www.gutenberg.org/files/84/84-0.txt):
         stdout.write(line)

   aliases["frankenstein"] = _frankenstein
   </code></pre></details>
3. Write a callable alias `upper`, that uppercases what it reads on stdin.
   <details><pre><code class="python"># short version
   aliases["upper"] = lambda args, stdin: stdin.read().upper()

   # streaming version
   def _upper(args, stdin=None, stdout=None):
      for line in stdin:
         stdout.write(line.upper())

   aliases["upper"] = _upper
   </code></pre></details>
2. Write a callable alias `words`, that returns all of the unique, sorted
   words coming from stdin.
   <details><pre><code class="python">aliases["words"] = lambda args, stdin: "\n".join(sorted(set(stdin.read().split())))
   </code></pre></details>
4. Write a callable alias `count`, that returns the number of tokens read
   from stdin.
   <details><pre><code class="python">aliases["count"] = lambda args, stdin: str(len(stdin.read().split())) + "\n"
   </code></pre></details>
5. Combine the above aliases in a single pipeline to count the number of words
   in "Frankenstein."
   <details><pre><code class="bash">$ frankenstein | upper | words | count
   11724
   </code></pre></details>

---
class: center, middle, inverse
# Break
---

class: center, middle, inverse
name: events
# Events

---
# Event Handlers

Yay for events!  An event is an... event?

An event is a trigger that can `fire` at predefined points in code.

`xonsh` has a simple, but powerful, event system that you can use to peek inside
of existing programs or to add more functionality to your own.

--

An event `handler` is a function that is called whenever a given event is
`fired`. An event can have multiple handlers -- that is, you can run arbitrarily
many functions that are all triggered by a single event firing.

Let's look at an example!

---
# `events.on_chdir`

There are several events that are already registered with `xonsh` (we'll cover
how to add _new_ events in a little bit).

`events.on_chdir` fires every time we... change directory. Because this event
already exists, all we want to do is to register a `handler` to listen for the
event and then execute.

--

```bash
$ @events.on_chdir
° def chdir_handler(olddir, newdir):
°     print(f"The directory changed from {olddir} to {newdir}!")
°
```

--

```bash
$ cd ..
The directory changed from /home/gil/github.com/xonsh/scipy-2019-tutorial to /home/gil/github.com/xonsh!
$ cd scipy-2019-tutorial/
The directory changed from /home/gil/github.com/xonsh to /home/gil/github.com/xonsh/scipy-2019-tutorial!
```

---
# Event Management

Um... how do I make this stop?

That was a verbose event handler we created.  Let's turn it off.

Each event has an associated `set` of `handlers`. The simplest way to remove a
handler is to `pop` it off.

```bash
$ events.on_chdir.pop()
<function __main__.chdir_handler>
```

Now that we can move around without the shell yelling at us, let's look at that
example in-depth.

---
# Handler Registration

You can register a handler by decorating a function with the event object:

```bash
$ @events.on_chdir
° def chdir_handler(olddir, newdir):
°     print(f"The directory changed from {olddir} to {newdir}!")
```

Note that `olddir` and `newdir` weren't specified by us - they're supplied by
the event itself.

--

Not all events provide arguments to their handlers -- you can check by calling
`help(event.name)`, although that's overly verbose.

For now, it's simpler to print the `__doc__` directly:

```bash
$ print(events.on_chdir.__doc__)

on_chdir(olddir: str, newdir: str) -> None

Fires when the current directory is changed for any reason.
```

The type-hint-esque signature tells us which variable names to expect and their
types. (If you don't want to deal with them, you can capture them using `**kwargs`)

---
# When events are fired

Some events are fired because `xonsh` has them set up to `fire` already. Others
`fire` when you tell them to. Many event names are self-descriptive, but if
there's any ambiguity, you can always check the `__doc__`.

You can also look at the list of events in the [docs](https://xon.sh/events.html)

Let's walk through defining our own event, setting it to fire, then set up a
handler to react to it.

---
# Example Event

First we have to create the new event. Believe it or not, to create an event,
you create a docstring for the event. This is truly self-documenting code.

Let's create an event that raises an alarm if it's called. This is our first
event, so we won't add in any `kwargs`:

--

```bash
$ events.doc("never_run_this", """
° never_run_this() -> None
°
° Fired when forbidden functions are run.
° """)
```

--

Now write a function that `fires` the event. This function could do a bunch of
other stuff, but for this example we'll keep it simple:

--

```bash
$ def delete_my_computer():
°     events.never_run_this.fire()
°
```

Now run `delete_my_computer`.  What happened?

--

Nothing.

---
# Example Event continued

Not nothing, truly. The event _did_ fire, but we weren't listening. What do we
need to add?

--

A handler!

--

Something suitably chastening -- this person did just delete your computer, after all.

--

```bash
$ @events.never_run_this
° def WHO_DID_IT():
°     print(f"omg user {$(whoami)} DELETED YOUR COMPUTER")
```

--

Now, go ahead and delete your computer again:

--

```bash
$ delete_my_computer()
omg user gil
 DELETED YOUR COMPUTER
```

---
# Event exercises 🏊‍♀️

1. "Fix" the previous `on_envvar_change` example to only print new environment
   variables if they aren't `CWD` or `OLDCWD`

   <details><pre><code class="python">
   @events.on_envvar_change
   def print_env(name, oldvalue, newvalue):
       if name not in ["CWD", "OLDCWD"]:
           print(f"envvar {name} changed from {oldvalue} -> {newvalue}")
   </code></pre></details>

2. Look at the `__doc__` for `events.on_postcommand` and use it to create a post
   command hook that sets the `$RIGHT_PROMPT` to display the starting and ending
   timestamps of the previous command. (`$RIGHT_PROMPT` needs to be a string)

   <details><pre><code class="python">
   @events.on_postcommand
   def update_rprompt(ts, **kwargs):
       $RIGHT_PROMPT = f"{ts[0]} -> {ts[1]}"
   </code></pre></details>

--

3. `pop` the `on_envvar_change` handler off and update it to also ignore `RIGHT_PROMPT`
   <details><pre><code class="python">
   events.on_envvar_change.pop()

   @events.on_envvar_change
   def print_env(name, oldvalue, newvalue):
       if name not in ["CWD", "OLDCWD", "RIGHT_PROMPT"]:
           print(f"envvar {name} changed from {oldvalue} -> {newvalue}")
   </code></pre></details>

---
class: center, middle, inverse
name: macros
# Macros
---
name: what-is-a-macro
# What is a Macro

.big[A **macro** is syntax that replaces a smaller amount of code with a
larger expression, syntax tree, code object, etc. after the macro has been evaluated.]

---
name: what-is-a-macro1
template: what-is-a-macro

.big[<u>The macro algorithm</u>:]
---
template: what-is-a-macro1
.big[
1. Pause or skip normal parsing
]
---
template: what-is-a-macro1
.big[
1. Pause or skip normal parsing
2. Gather macro inputs as strings
]
---
template: what-is-a-macro1
.big[
1. Pause or skip normal parsing
2. Gather macro inputs as strings
3. Evaluate macro with inputs
]
---
template: what-is-a-macro1
.big[
1. Pause or skip normal parsing
2. Gather macro inputs as strings
3. Evaluate macro with inputs
4. Resume normal parsing and execution.
]
--

.big[Xonsh's macro system is more like Rust's than those in C, C++, Fortran,
Lisp, Forth, Julia, etc.]

--

.big[If these seem scary, Jupyter magics `%` and `%%` are macros! 🧙]

---
# What is a Macro

.big[Macros use a special `!` syntax. (Rust never sleeps, after all.)]

--

.big[There are three types of macro syntax in xonsh.]

--

<br/>

.bigger.center[**Subprocess Macros**]

--

<br/>

.bigger.center[**Function Macros**]

--

<br/>

.bigger.center[**Context Macros**]

---
# Subprocess Macros

Subprocess macros turn everything after an `!` in a subprocess call into
a single string argument that is passed into the command.

--

A simple example:

```bash
$ echo! I'm Mr. Meeseeks.
I'm Mr. Meeseeks.
```

--

Xonsh will split on whitespace,
so each argument is passed in separately.

```bash
$ echo x  y       z
x y z
```

--

Space can be preserved with quotes, but that can be annoying.

```bash
$ echo "x  y       z"
x  y       z
```

--

Subprocess macros will pause and then strip
all input after `!`.

```bash
$ echo! x  y       z
x  y       z
```

---
# Subprocess Macros

Macros pause *all* syntax, even fancy xonsh bits like environment
variables.

--

For example, without macros, environment variable are expanded:

```bash
$ echo $USER
it-me
```

Inside of a macro, all parsing is turned off.

```bash
$ echo! $USER
$USER
```

--

You can put the `!` anywhere in the subprocess:

```bash
$ echo I am $USER ! and I still live at $HOME
I am it-me and I still live at $HOME
```

---
# Subprocess Macros

This is particularly useful whenever you need to write code as
string and hand that source off to a command.

--

**timeit**:

```bash
$ timeit! "hello mom " + "and dad"
100000000 loops, best of 3: 8.24 ns per loop
```

--

**bash** (for shame!):

```bash
$ bash -c ! export var=42; echo $var
42
```

--

**python** (yay 🎉):

```bash
$ python -c ! import os; print(os.path.abspath("/"))
/
```

---
# Function Macros

Xonsh supports Rust-like macros that are based on normal Python callables.

--

Macros do not require a special definition in xonsh.

--

However, they must be called with `!` between the callable and
the opening parentheses `(`.

--

Macro arguments are split on the top-level commas `,`.

--

For example functions `f` and `g`:

```python
# No macro args
f!()

# Single arg
f!(x)
g!([y, 43, 44])

# Two args
f!(x, x + 42)
g!([y, 43, 44], f!(z))
```

---
# Function Macros

.big[The function definition determines what happens to the arguments]

--

.big[Arguments in the macro call are matched up with the corresponding parameter
annotation in the callable’s signature.]

--

.big[For example:]

```python
def identity(x : str):
    return x
```

--

.big[Calling this normally in Python will return the same object we put in,
even if it is not a string!]

---
# Function Macros

.big[<u>Normal Python Call</u>:]

```python
>>> identity('me')
'me'

>>> identity(42)
42

>>> identity(identity)
<function __main__.identity>
```

.big[<u>Xonsh Macro Call</u>:]

```python
>>> identity!('me')
"'me'"

>>> identity!(42)
'42'

>>> identity!(identity)
'identity'
```

---
# Function Macros

.big[Each macro argument is stripped prior to passing it to the macro.]

--

.big[This is done for consistency.]

--

```python
>>> identity!(42)
'42'

>>> identity!(  42 )
'42'
```

--

<div style="text-align:center;">
<img src="doge-digi-wider-lte.png" style="width:300px;"/>
</div>

---
# Function Macros

.bigger[Some truly nefarious examples:]

```python
>>> identity!(import os)
'import os'

>>> identity!(if True:
...     pass)
'if True:\n    pass'

>>> identity!(std::vector<std::string> x = {"yoo", "hoo"})
'std::vector<std::string> x = {"yoo", "hoo"}'
```

---
# Function Macros Annotations

<table class="docutils align-default" id="id2">
<colgroup>
<col style="width: 20%" />
<col style="width: 20%" />
<col style="width: 20%" />
<col style="width: 20%" />
<col style="width: 20%" />
</colgroup>
<thead>
<tr class="row-odd"><th class="head"><p>Category</p></th>
<th class="head"><p>Object</p></th>
<th class="head"><p>Flags</p></th>
<th class="head"><p>Modes</p></th>
<th class="head"><p>Returns</p></th>
</tr>
</thead>
<tbody>
<tr class="row-even"><td><p>String</p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">str</span></code></p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">'s'</span></code>, <code class="docutils literal notranslate"><span class="pre">'str'</span></code>, or <code class="docutils literal notranslate"><span class="pre">'string'</span></code></p></td>
<td></td>
<td><p>Source code of argument as string, <em>default</em>.</p></td>
</tr>
<tr class="row-odd"><td><p>AST</p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">ast.AST</span></code></p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">'a'</span></code> or <code class="docutils literal notranslate"><span class="pre">'ast'</span></code></p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">'eval'</span></code> (default), <code class="docutils literal notranslate"><span class="pre">'exec'</span></code>, or <code class="docutils literal notranslate"><span class="pre">'single'</span></code></p></td>
<td><p>Abstract syntax tree of argument.</p></td>
</tr>
<tr class="row-even"><td><p>Code</p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">types.CodeType</span></code> or <code class="docutils literal notranslate"><span class="pre">compile</span></code></p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">'c'</span></code>, <code class="docutils literal notranslate"><span class="pre">'code'</span></code>, or <code class="docutils literal notranslate"><span class="pre">'compile'</span></code></p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">'eval'</span></code> (default), <code class="docutils literal notranslate"><span class="pre">'exec'</span></code>, or <code class="docutils literal notranslate"><span class="pre">'single'</span></code></p></td>
<td><p>Compiled code object of argument.</p></td>
</tr>
</tbody>
</table>
---
# Function Macros Annotations

<table class="docutils align-default" id="id2">
<colgroup>
<col style="width: 20%" />
<col style="width: 20%" />
<col style="width: 20%" />
<col style="width: 20%" />
<col style="width: 20%" />
</colgroup>
<thead>
<tr class="row-odd"><th class="head"><p>Category</p></th>
<th class="head"><p>Object</p></th>
<th class="head"><p>Flags</p></th>
<th class="head"><p>Modes</p></th>
<th class="head"><p>Returns</p></th>
</tr>
</thead>
<tbody>
<tr class="row-odd"><td><p>Eval</p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">eval</span></code> or <code class="docutils literal notranslate"><span class="pre">None</span></code></p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">'v'</span></code> or <code class="docutils literal notranslate"><span class="pre">'eval'</span></code></p></td>
<td></td>
<td><p>Evaluation of the argument.</p></td>
</tr>
<tr class="row-even"><td><p>Exec</p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">exec</span></code></p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">'x'</span></code> or <code class="docutils literal notranslate"><span class="pre">'exec'</span></code></p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">'exec'</span></code> (default) or <code class="docutils literal notranslate"><span class="pre">'single'</span></code></p></td>
<td><p>Execs the argument and returns None.</p></td>
</tr>
<tr class="row-odd"><td><p>Type</p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">type</span></code></p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">'t'</span></code> or <code class="docutils literal notranslate"><span class="pre">'type'</span></code></p></td>
<td></td>
<td><p>The type of the argument after it has been evaluated.</p></td>
</tr>
</tbody>
</table>
---
# Function Macros Annotations

.big[Say we have a function `func` with the following annotations,]

```python
def func(a, b : 'AST', c : compile):
    pass
```

--

.big[In a macro call of `func!()`,

* `a` will be evaluated with `str` since no annotation was provided,
* `b` will be parsed into a syntax tree node, and
* `c` will be compiled into code object since the builtin `compile()` function
  was used as the annotation.
]

--

.center.large[✨🤯✨]

---
# Context Macros

Context macros use `with!` to capture everything after the colon in
an otherwise normal with-statement.

--

This provides anonymous & onymous blocks.

--

```python
with! x:
    y = 10
    print(y)
```

--

This can be thought of as equivalent to:

```python
x.macro_block = 'y = 10\nprint(y)\n'
x.macro_globals = globals()
x.macro_locals = locals()
with! x:
    pass
```

--

The `macro_block` string is dedented.

--

The `macro_*` attrs are set before the context manager is entered so
the `__enter__()` method may use them.

--

The `macro_*` attributes are not cleaned up likewise for `__exit__()` method.

---
# Context Macros

.big[By default, macro blocks are returned as a string.]

--

.big[However, like with function macro arguments, the kind of `macro_block` is
determined by a special annotation.]

--

.big[This annotation is given via the `__xonsh_block__` attribute on the context manager
itself.]

--

.big[This allows the block to be interpreted as an AST, byte compiled, etc.]

---
# Context Macros Example

```python
import xml.etree.ElementTree as ET

class XmlBlock:

    # make sure the macro_block comes back as a string
    __xonsh_block__ = str

    def __enter__(self):
        # parse and return the block on entry
        root = ET.fromstring(self.macro_block)
        return root

    def __exit__(self, *exc):
        # no reason to keep these attributes around.
        del self.macro_block, self.macro_globals, self.macro_locals
```

--

```python
with! XmlBlock() as tree:
    <note>
      <to>You</to>
      <from>Xonsh</from>
      <heading>Don't You Want Me, Baby</heading>
      <body>
        You know I don't believe you when you say that you don't need me.
      </body>
    </note>

print(tree.tag)  # will display 'note'
```

---
# Exercises 🤼‍♂️

1. Run the `timeit` command on formatting the string `"the answer is: {}"` with
   the integer value `42` using a subprocess macro.
   <details><pre><code class="bash">$ timeit! "the answer is: {}".format(42)
   10000000 loops, best of 3: 169 ns per loop
   </code></pre></details>
2. Call the standard library `importlib.import_module()` function as a macro
   to import a module, such as `os` or `sys` without an explicit string.
   <details><pre><code class="bash">$ importlib.import_module!(os)
   &lt;module 'os' from '/home/scopatz/miniconda/lib/python3.7/os.py'&gt;
   </code></pre></details>
3. Write a `JsonBlock` contenxt manager that can be used to embed JSON
   into your xonsh code.
   <details><pre><code class="python">import json

   class JsonBlock:

      def __enter__(self):
          return json.loads(self.macro_block)

      def __exit__(self, *exc):
          del self.macro_block, self.macro_globals, self.macro_locals
   </code></pre></details>

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
## Disentangling results

---
# MRI Data

You are working in a lab that is concerned with MRIs of mouse lemurs.

What's a mouse lemur, you ask?

--

<div style="text-align:center;">
<img src="mouse-lemur-with-flowers.jpg" style="width:500px;"/>
</div>

---
class: center, middle, inverse
# FOCUS, THIS IS SCIENCE
---
# Where were we?

You are working in a lab examining MRI data of mouse lemurs.
The data set you need is on a webserver set up by the post-doc who disappeared last week.

--

.big[But we have some bad news.]

--

.big[A lot of it.]

---
# The state of the ~~nation~~ data

The post-doc uses `bash` to handle all of the data collection.

He didn't know how to make sure different datasets were saved to separate
directories, so he added a random 4-digit integer to the end of each `nii.gz`
file.

But, in the script, he neglected to add the same random number to the end of the
corresponding `json` metadata file and... well... we don't know if these are our
mouse lemur scans, or if they're cervical spine scans...

--

He also ran `rm` with an overly permissive `glob` and deleted all of the `json`
metadata files.

--

To make matters worse -- these files are kind of big, and the post-doc's
webserver is at his house. We don't want to have to download all of these
files if we don't have to.

---
# But don't despair!

We know a couple of things:

--

* All of the files follow the naming convention `sub-{:02d}_{:04d}.nii.gz`

--

* While `nii.gz` files are a bit large, they have some identifying information in the first 348 bytes of the file

--

* We're sure there are 19 mouse lemur scans

--

* The cervical spine study going on at the lab next door (hence the overlapping files) only has 6 subjects

--

---
# Plan of action

There is a great Python library for handling MRI data called `nibabel` -- you can install it by running

```bash
conda install -c conda-forge nibabel
```

1. Install `nibabel`
2. Use `curl` to get the file-list from the webserver -- it isn't formatted super well and will take some massaging.
3. Use `curl` to download only the header-portion of the files
4. Load the files into `nibabel` and inspect the `header` attribute and see if
   we can figure out how to separate the two sets of scans.

.footnote[
**Note:**
```
curl -r 0-347 -O path/to/file
```
will download only the first 348 bytes of a given file and save it with the same
name locally.]
