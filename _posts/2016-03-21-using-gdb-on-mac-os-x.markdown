---
layout: post
title: Using GDB on Mac OS X
date: '2016-03-21 17:04:02'
---

#### INSTALLATION:
1. if not installed already, [install](http://brew.sh/) `brew`
2. if you have brew already on your system, you might want to update the brew installation, typing: `brew update`. This will give you the latest installation recipes
3. install GDB: `brew install gdb`. This will install the latest GDB.

#### CONFIGURATION:

If you try to start using the newly installed GDB in your terminal at once, you may get an error like this one:

```bash
$ gdb ../local/bin/lcg
[...]
Starting program: ../local/bin/lcg
Unable to find Mach task port for process-id 44929: (os/kern) failure (0x5).
 (please check gdb is codesigned - see taskgated(8))
(gdb) c
The program is not being run.
(gdb) q
```
If that's the case, you should *code-sign* GDB. In order to do so, please follow the following steps.

#### CODE-SIGNING GDB:

![code-signing GDB on Mac OS X](/gblog/content/images/2016/03/gdb-cert.png)

Clicking on Continue you will get a warning like "You are about to create a self-signed certificate.", you can continue. You are now asked to set a validity period, in days; I just set 999 days (which appears to be the maximum value accepted, at least on Mavericks) so I don't have to do it again in a short while. Then, for the purpose of this self-signed certificate made to run GDB on our system, you can skip all the other certificate settings, just clicking on "Continue" when asked, until your are asked to set the location

![](/gblog/content/images/2016/03/gdb-cert-3b.png)

here, choose "System", then click on "Create".
You might be asked for your password, and you will get your new certificate!

![](/gblog/content/images/2016/03/gdb-cert-2-1.png)

Now go to the Certificate Assintant window and look for your newly created certificate "gdb-cert", then double-click on it. You will get the detailed info about the new certificate:

![](/gblog/content/images/2016/03/gdb-cert-3.png)

Click on "Trust", and set the first setting to "Always Trust"

![](/gblog/content/images/2016/03/gdb-cert-4.png)

Close the Certificate Assistant window; you might be asked for your password to save the changes you made.

Now we want to kill the `taskgated` process, to be sure to pick the new certificate when code-signing: 

```bash
sudo killall taskgated
```

Now we can code sign our GDB. To do that we open a shell, and we look for the GDB location:

```bash
$ which gdb
/usr/local/bin/gdb
```

then we can use this location to code-sign GDB, typing:

```bash
$ codesign -fs gdb-cert /usr/local/bin/gdb
```

You might then be asked for your password.

If you get an error like this

```
this identity cannot be used for signing code
```

then restarting your machine should fix the problem.

Now we can use the just installed ```gdb``` in our shell!



