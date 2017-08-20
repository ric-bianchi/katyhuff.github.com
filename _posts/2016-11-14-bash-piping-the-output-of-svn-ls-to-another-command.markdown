---
layout: post
title: 'BASH: Piping the output of ''svn ls'' to another command'
date: '2016-11-14 12:54:54'
tags:
- sysadmin
---


```bash
svn ls $URL | sed "s,/$,," | cut -d ' ' -f6 | xargs <command>
```

In case of ATLAS, I often use it to checkout a whole folder from the SVN repository through the ```svnco``` command, with:

```bash
svn ls $URL | sed "s,/$,," | cut -d ' ' -f6 | xargs `which svnco`
```

(see: http://stackoverflow.com/questions/1407567/pipe-shell-output-to-svn-del-command)
