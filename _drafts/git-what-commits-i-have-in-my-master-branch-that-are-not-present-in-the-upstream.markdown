---
layout: post
title: 'Git: what commits I have in my ''master'' branch that are not present in the
  ''upstream''?'
---

## "Ops! I forgot to create a branch!"

Some days ago I started working on a new feature. But after some days of work, I realized that I was working inside my 'master' branch.

As best practice, I should have created a 'feature branch' to collect all the commits relevant to the feature I was developing.

So the problem is: how can I create a new branch to collect all those commits?

Because a picture is worth 1000 words, You would like to move from this situation:

```
o-o-X-o-a-o-o-b-o-c-o-o  (master)
```

to this one:

```
o-o-X-o-o-o-o-o-o  (master)
     \a-b-c       (branch)
```



## Intro: how the code is organized

My Git is organized like this:

```
(origin)   (upstream)
    |     /
    |   /
   (local)
```

`origin` is my own remote repository, while `upstream` is the main repository storing the production code for our software. `local` is the working copy on my local computer.

Regularely I update my `local` to the latest changes in the `'upstream`, merging them with my master by using `git fetch upstream` and `git pull upstream master`; then I push the updated master to my `origin` repository by using `git push origin master`.

## Check new commits

As I said above, I started working in my 'master', so now in my local copy I have few commits in my 'master', which of course are mixed with all the commits that have been pushed to the `upstream` 'master'.

As first step, I want to see what commits I created, which are abviously not present in the `upstream` 'master', since I didn't push any change to it yet.

For this, I can use the command `git cherry`.
This command, in its general form `git cherry -v branch1 branch2`,  gives you the list of all commits present in `branch2`, but not present in `branch1`. 

In our example:

```
$ git cherry -v upstream/master origin/master
+ 16e9500a28181ad434f0608501faf78e218bab6c fixing old Qt4 commands
+ e4edd5f3957bd576e92baa235e0845142b6d8b35 removing the REQUIRED option
+ ef6945d63db50be7abff557819d48807e91178e8 porting the fix to 'DataQualityConfigurations' from r.21 to master
+ d7a4651335aac0d1f3ef651a39c5d942f4ec5780 porting Attila's fix from r.21 to 'master'
+ 62bf6354f8ac64e656c0d9698ecf0995a0e11e9e fixing a missing implementation of 'src/TrackCollectionSettingsButton.cxx' and missing includes of 'QMimeData' and 'QDrag'
```

Now I know which commits I have to extract from 'master', to collect them in a separate branch.


