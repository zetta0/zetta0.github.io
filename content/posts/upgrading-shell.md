+++
categories = ["Offensive Security"]
date = "2022-12-03"
description = ""
featured = ""
featuredalt = ""
featuredpath = "date"
linktitle = ""
title = ""
slug = ""
type = "post"
tag = "pentesting"
+++

## Make an interactive reverse shell

It's annoying to get a primitive shell on a linux box so this walkthrough will show you how to create an interactive shell.

First check which version of python is installed.

```bash
which python python2 python3
```

Next we create our new shell from pty.

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

Then we background the job back to our attacker box

```bash
CTRL + Z
```

Then we set our size and raw

```bash
stty size;stty raw -echo;fg
```

Finally this will bring us back to the reverse shell

```bash
export TERM=xterm-256color
```

This makes our shell have color, tab completion, and you can backspace without breaking the shell!
