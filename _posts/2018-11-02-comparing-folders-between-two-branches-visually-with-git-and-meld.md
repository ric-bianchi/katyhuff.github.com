---
layout: post
title: "Comparing Folders Between Two Branches Visually, with Git and Meld"
---

You have two branches, and you want to see all the differences there are between them in files and folders.

To compare between two branches, you can use the command:

    $ git diff master..mybranch

But this gives you a text-like diff, as the standard `diff` program does.
This is fine for small differences, but not very convenient when you have many differences to review.

If you want to use a GUI to explore all the changes, you can add an external program as the git's `difftool` option. One popular visual diff tool is Meld. 
You can use it with git with this:

    $ git difftool -t meld master..mybranch

However, this will compare file by file, not entire folders. So, you will get a new Meld view for each of the files. Which is not very convenient either.

Starting [from git 1.7.11](https://stackoverflow.com/a/12815806/320369), you can use the option `-d`, to tell git to compare folders instead of files. Thus, we can combine those options together, to get a nice, visual view of al the changes in files and folders between two branches with the command:

    $ git difftool -t meld -d master..mybranch
    
You can see an example of the output in the image below.

 ![Meld screenshot](/assets/img/posts/diff_folders_git_branches_meld.png)
