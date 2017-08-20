---
layout: post
title: 'Bash: deleting (or operating) on same folder (or file) in different parent
  subfolders'
date: '2016-11-16 15:11:52'
tags:
- bash
- sysadmin
---


I have a tree of folders like this:

```
folderA/
        subA/
        subB/
folderB/
        subA/
        subB/
folderC/
        subA/
        subB/
```

That is, I have a folder "subA" and "subB" in different subfolders.

**The problem**: I want to delete folders "subA" and "subB" in all parent folders recursively with only one command, without deleting the parent folders.

**The solution**: the `find` command

1. the `find` command accepts the option `-name` to specifying a file or folder name to match; also it accepts a `-type` option to specify if you want find to process only files or only folders (`-type f` or `-type d`, respectively); moreover, it accepts the `-exec` command to run a command on every file or folder found
1. The command `find . -name 'sub*' -type d` will start from the current folder (the `.` folder) and will find all subfolders whose names start with the string "sub"
1. test your selection command, executing a simple `ls` command on found directories. The command `find . -name 'sub*' -type d -exec ls -d {} \;` will execute the command `ls` on all subfolders found by `find`.
1. replace `ls -d {}` with `rm -rf {}` to delete all subfolders found by `find`: `find . -name 'sub*' -type d -exec rm -rf {} \;`


**Notes:**

* if you want to limit the **level of recursion** in the file listing, you can use the `-maxdepth` option; for example: `find . -name 'sub*' -maxdepth 1` will look only among the subfolders at the first depth level

* You can use the above command to check your folders for local changes compared to a remote **SVN** repository: `find . -name 'sub*' -maxdepth 1 -type d -exec svn stat {} \;`

