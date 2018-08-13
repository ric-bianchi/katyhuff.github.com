---
layout: post
title: 'How to list files and folders based on date/time on Linux using Find in the Bash shell'
---

With the command `ls` I can list files and folders based on many filters, but what if I want to list them based on a certain timestamp or those which have been modified/created in a given time slot?

By using the bash `find` command, I can easily list folders and files based on timestamps.

For example, I can list all top folders and files from a certain directory created/updated between the 18th and the 19th of Jan 2016 (here I used `-maxdepth 1` to avoid recursion into subfolders and to only list top folders):

```bash
find . -maxdepth 1 -newermt "2016-01-18" ! -newermt '2016-01-19'
```

By using the option `-exec`, I can also pipe the result into another command, for example `ls`, to list more details of the returned files and folders:

```bash
find . -maxdepth 1 -newermt "2016-01-18" ! -newermt '2016-01-19' -exec ls -ld {} \;
```

Using the same mechanism, I can copy the returned items too, for example to make a copy:

```bash
find . -maxdepth 1 -newermt "2016-01-18" ! -newermt '2016-01-19' -exec cp -r {}_copy \;
```

