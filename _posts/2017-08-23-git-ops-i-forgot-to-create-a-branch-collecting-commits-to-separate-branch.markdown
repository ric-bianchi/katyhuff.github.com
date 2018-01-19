---
layout: post
title: 'Git - Ops! I forgot to create a branch! How can I collect my latest commits in a separate branch now?'
---

## "Ops! I forgot to create a branch!"

Some days ago we started working on a new feature. But after some days of work, we realized that you were working inside my 'master' branch.

As best practice, we should have created a 'feature branch' to collect all the commits relevant to the feature we were developing.

So the problem is: how can we create a new branch to collect all those commits, and only those?

Because a picture is worth 1000 words, let us take a look at the schema below. We would like to move from this situation:

```
o-o-X-o-a-o-o-b-o-c-o-o  (master)
```

to this one:

```
o-o-X-o-o-o-o-o-o  (master)
     \a-b-c        (branch)
```

hence, collecting the commits `a`, `b` and `c` into a separate feature branch.

What follows is only one of the way of achieving this, given the tools git offer to us to manipulate the history and the organization of our source code; but in my humble opinion is one of the easiest one, if you only have to re-organize few commits.


## Intro: how the code is organized

Let us suppose our Git repository is organized like this:

```
(origin)   (upstream)
    |     /
    |   /
   (local)
```

`origin` is our own remote repository, while `upstream` is the main repository storing the production code for our software. And `local` is the working copy on our local computer.

Regularly we update our `local` to the latest changes in the `'upstream`, merging them with our `master` branch by using:

<!-- ```
git fetch upstream
git pull upstream master
``` -->

{% highlight bash %}
git fetch upstream
git pull upstream master
{% endhighlight %}

then we push the updated master to our `origin` repository by using:

```
git push origin master
```

## Let us collect all our new changes

As said above, we started working in our `master`, so now in our local copy we have few commits in our `master` branch, which of course are inter-mixed with all the commits that have been pulled from the `master` branch on `upstream` .

As first step, we want to see what commits we created, to be able to separate them from all the others.
The new commits we have created while working in the past few days are, obviously, not present in the `upstream` `master`, since we didn't create any *merge requests* with it yet.

For that purpose, we can use the command `git cherry`.
This command, in its general form `git cherry -v branch1 branch2`,  gives you the list of all commits present in `branch2`, but not present in `branch1`.

In our example the command gives us this outcome:

```
$ git cherry -v upstream/master origin/master
+ 16e9500a28181ad434f0608501faf78e218bab6c fixing old Qt4 commands
+ e4edd5f3957bd576e92baa235e0845142b6d8b35 removing the REQUIRED option
+ ef6945d63db50be7abff557819d48807e91178e8 porting the fix to 'DataQualityConfigurations' from r.21 to master
+ d7a4651335aac0d1f3ef651a39c5d942f4ec5780 porting Joe's fix from r.99 to 'master'
+ 62bf6354f8ac64e656c0d9698ecf0995a0e11e9e fixing a missing implementation
```

At this point we know which commits we have to extract from 'master', to collect them in a separate branch.

## A new branch to store them all

Let us now create a new branch to store all the commits.

Since the `master` branch on our own `origin` repository is "tainted", we have to create a branch from a clean status. The easiest is to use the `upstream`.

Let us start creating a new branch:

```
git checkout -b new-feature
```

This will create a new branch called `new-feature`. Now let us *reset* our new branch to teh clean status of the `master` branch stored at the `upstream` repository:

```
git fetch usptream
git reset --hard upstream/master
```

The first line collect information about the latest changes in the `upstream` repository. The second command reset the local code to the status found in the `upstream master` branch.

Now we have a clean branch reflecting the status of the `upstream`.

## Pick the commits

We can now start collecting the commits from our *tainted* master branch. For that we can use the command `git cherry-pick`:

```
git cherry-pick 62bf6354f8ac64e656c0d9698ecf0995a0e11e9e
```

The command above will *pick* the single commit, uniquely identified from its ID, and apply it on the local code.
Repeat that for all the other commits, and at the end we will have our new branch containing only the commits we wanted to collect. We can verify that by using the command `git log`, which shows us the history of the local branch.

## Push your new branch

Now we can push our new branch to our own remote repository; for that we will use the option `-u` which create a new branch on the remote site if the local branch we are trying to pushing is new:

```
git push -u origin new-feature
```

We now have the new branch containing only few commits on our own `origin` repository; branch which is now ready to be the object of a *merge request* into the `upstream` master branch.
