---
layout: post
title: Clearing the bash cache (while installing Mercurial on SLC6...)
---

Today I wanted to clone a repository from Bitbucket on my SLC6 machine, but I noticed I had a very old mercurial installed:

which hg
/usr/bin/hg

hg --version
1.4.4

So I decided to install a new version of 'mercurial'.

I started removing the old SLC6 'mercurial' package with yum:

yum erase mercurial

Then, not finding any newer release in the usual SLC6 yum repositories, I decided to install it with Conda:

conda install mercurial

that installed the version 3.7 successfully. Conda also installed the 'hg' command into its folder:

/opt/python/anaconda/bin/hg

Now I wanted to make the 'hg' command available at command line, so I made a softlink from the /usr/local/bin folder (which is in my PATH) to the Conda 'hg' binary:

ln -s /opt/python/anaconda/bin/hg /usr/local/bin/hg

But then, when I tried to run it... I got this error:

$ hg
bash: /usr/bin/hg: No such file or directory

This is because bash does cache the full path to a given command. So if you changed the path of a given command in the same shell, you probably get this kind of errors. In my example the command 'hg' had been de-facto moved from /usr/bin/hg to /usr/local/bin/hg.

We can verify if the path of the command we are trying to execute is hashed (i.e. cached in a hash table) typing:

$ type hg
hg is hashed (/usr/bin/hg)

So, yes: it's cached. To clean the entry for hg, we can type:

$ hash -d hg 

this will clean and rebuild the cache for that single entry hg.
To clear the entire cache, instead, just type:

$ hash -r

Now the hg command is correctly reachable again:

$ which hg
/usr/local/bin/hg
$ hg --version
Mercurial Distributed SCM (version 3.7.3)
...



