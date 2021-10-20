+++
title = "Learning Sed Is Beneficial For Linux Users"
date = "2021-08-31T12:44:26+0000"
authors = ["kn"]
cover = "posts/sed/hesedshesed.jpg"
tags = ["linux", "sed"]
keywords = ["sed", "linux"]
description = "Sed for beginner"
showFullContent = false
+++

One of the most important command line utility in Linux is `sed` which is a stream editor.
A stream editor is used to perform basic text transformations on an input stream (a file or input from a pipeline).

Essentially what it can do is it allows you to search for String of text or specific pattern and then replace it pattern with whatever you tell sed to replace with, so it is really kind of search and replace function.

### Basic sed substitution 's'

```bash
sed 's/find/replace/' <oldfile >newfile
```

So we are telling sed that go search for the word `find` then replace it with the word `replace` and what it does is it searches every lines for its first instance of the word `find`. So it not gonna replace every instances of `find`, only the first time it appears on each line from `oldfile` and then writes it to `newfile`.

Sometimes you want to replace every `find` in `oldfile` you just need to add global substitution `g` for every instances `find`. 

```bash
sed 's/find/replace/g' <oldfile >newfile
```

But What if you just want to take a file, change it then write to that same file, you could use `-i` flag.
It will replace all occurrences of a string in a file, overwriting the file (i.e. in-place):

```bash
sed -i 's/find/replace/g' filename
```

You are not often doing this as far taking input from a file and then taking the output and write to new file. Probably most of the time, you take the output from other shell command and pipe it to `sed` command. For example:

```bash
echo "Thank Do Hoang" | sed 's/Ho/W/'
Thank Do Wang
```

Other basic usage:

- To replace only on lines matching the line pattern:

```bash
sed '/line_pattern/s/find/replace/' filename
```

- Delete lines matching the line pattern:

```bash
sed '/line_pattern/d' filename
```

- Print the first 11 lines of a file:

```bash
sed 11q filename
```

- Apply multiple find-replace expressions to a file:

```bash
sed -e 's/find/replace/' -e 's/find/replace/' filename
```

- Replace separator `/` by any other character not used in the find or replace patterns, e.g. `#`:

```bash
sed 's#find#replace#' filename
```

What if I want to search for multiple pattern?

```bash
cat /etc/shells | sed -e 's/usr/u/g' -e 's/bin/b/g'
```

Sed is not picky about the symbol that you use for the separator in the
command, if you dont want to use the /, you can use pretty much any others kind of special symbol that you can think of as long as it is not part of the search pattern.

```bash
cat /etc/shells | sed -e 's|usr|u|g' -e 's#bin#b#g'
```

You can also print specific line that match the search pattern ( kind of `grep` )

```bash
cat /etc/shells | sed -n '/usr/p'
```

The cool trick about `sed` is to go and delete extra spaces at the end of the line, for example

```bash
sed -i 's/ *$//' test.sh
```

It will search for every lines that has spaces at the end of it and replace it with nothing (basically deleting them).
How about **tabs**?

```bash
sed -i 's/[[:space:]]*$//' test.sh
```

**empty lines?**

```bash
cat test.sh | sed '/^$/d'
```

First the `^` is the begining of the line then the `$` symbol is the end of the line ( find every pattern that match the begining of the line, and the end of the line and there is nothing between it - so that means that line is empty ).

You can also replace all the lowercase characters with uppercase characters for fun 🙃

```bash
sed 's/[a-z]\/U&/g' test.sh // uppercase
sed 's/[a-z]\/L&/g' test.sh // lowercase
```

C'est tout, merci kn 🙃
