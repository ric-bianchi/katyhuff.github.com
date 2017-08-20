---
layout: post
title: Installing node.js on RedHat/SLC6 Linux
date: '2016-04-29 11:15:06'
tags:
- sysadmin
- node-js
---

`npm` is the node.js package manager, so let us start installing that:

```bash
sudo yum install npm
```

Then, let us update npm itself (the yum packages for SLC6 are often quite old):

```bash
sudo npm install npm -g
```
and let us test:

```bash
$ node -v
v0.10.42
```

DONE! We have Node.js installed! :)
