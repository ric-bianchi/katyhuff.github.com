---
layout: post
title: LLDB errors
---

Running lldb debugger on my new Mac 10.12 Sierra I got a lot of those error messages:

```bash
NameError: name 'run_one_line' is not defined
Traceback (most recent call last):
  File "<string>", line 1, in <module>
NameError: name 'run_one_line' is not defined
Traceback (most recent call last):
  File "<string>", line 1, in <module>
NameError: name 'run_one_line' is not defined
Traceback (most recent call last):
  File "<string>", line 1, in <module>
```

I found this thread: https://github.com/Homebrew/homebrew-core/issues/2712

I checked my python installations:

```bash
which python
/usr/local/bin/python
```

which confirmed that I was using Python installed by Brew, and not the system python.

So I installed the 'six' package as suggested:

```bash
pip install six
```

and the problem with lldb disappeared.

