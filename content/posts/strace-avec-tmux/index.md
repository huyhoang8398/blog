+++
title = "Using Strace with Tmux"
date = "2023-08-16T22:39:31+0000"
authors = ["kn"]
tags = ["tmux", "system"]
keywords = ["tmux", "strace"]
description = "How to track the execution of a program using strace? By using tmux!"
showFullContent = false
+++

# How to track the execution of a program using `strace`? By using `tmux`!

We will have two panels:

> one for the execution of the command;
>
> the second dedicated to displaying `strace`.

Don't forget that you can search within everything displayed in a panel with `tmux`...

```bash
#!/bin/bash

TMPDIR=/tmp
FIFODIR=$(mktemp -p "${TMPDIR:-.}" -d strace-XXXX) || exit 1
FIFOPATH=$FIFODIR/fifo
mkfifo "$FIFOPATH" || { rmdir "$TMPDIR"; exit 1; }

COMMAND=$1
shift
ARGS=$@
PANE_ID=$(tmux if-shell "[ $(($(tmux display -p '8*#{pane_width}-20*#{pane_height}'))) -lt 0 ]" "splitw -v -d -c '#{pane_current_path}' -P -F '#{pane_id}' cat $FIFOPATH" "splitw -h -d -c '#{pane_current_path}' -P -F '#{pane_id}' cat $FIFOPATH")
tmux set-option -t $PANE_ID -p @mytitle "strace:$COMMAND"
tmux set-option -t $PANE_ID -p remain-on-exit on
strace $COMMAND $ARGS 2> $FIFOPATH
```

Thus, the `strace` tool sends its output to the `stderr` channel, which is then redirected to a `fifo` that is subsequently displayed in a `tmux` panel...

