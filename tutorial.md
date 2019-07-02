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
.big[Xonsh is also a **shell language**.]

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

.center[.bigger[**Python always wins!**]]

---
# Running Commands

.big[The purpose of a shell, of course, is to run commands!]

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
# Environment Variables

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
# Environment Lookup with `${expr}`

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

.red[**Warning!**] In sh-langs, `$NAME` and `${NAME}` mean the same thing.
In xonsh, they have distinct meanings.

---
# The Environment Itself `${...}`

If you ever need access to the environment object, you can grab it by
passing in an elipssis as the lookup expression, i.e. `${...}`.

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
# The `source` command

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
# `source-foreign`

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
# The `xonshrc` configuation file

.big[Xonsh's main config file is located at `~/.xonshrc`.]

--

.big[This is just a special `*.xsh` file that is loaded up before
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
# Exercises

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
# Uncaptured subprocesses exercises

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
# Exercises

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

.big[...just like any other command!]

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

The callable alias just needs to be the first element of a command:

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
# Exercises

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
