---
layout: post
title: "Identifying package a file belongs to"
categories: debian ubuntu which-package
---

Different ways of identifying the package a file belongs to:

* `dpkg`:
 1. quick
 1. works only for installed packages
 1. does not seem to work on symlinks :(

{% highlight bash %}
$ ls -l /usr/bin/vim /usr/bin/lsof
-rwxr-xr-x 1 root root 163224 Dec  2  2012 /usr/bin/lsof
lrwxrwxrwx 1 root root     21 Apr  5 13:19 /usr/bin/vim -> /etc/alternatives/vim

$ dpkg -S /usr/bin/vim
dpkg-query: no path found matching pattern /usr/bin/vim

$ dpkg -S /usr/bin/lsof
lsof: /usr/bin/lsof
{% endhighlight %}

* `dlocate`:
 1. quick, works only on installed packages
 1. needs to be installed separately: `apt-get install dlocate`
 1. works on symlinks too
 1. need to refresh dlocate db (`sudo update-dlocatedb`) after installing a new package :(

{% highlight bash %}
$ dlocate /usr/bin/vim
vim-runtime: /usr/bin/vimtutor
vim: /usr/bin/vim.basic
vim-tiny: /usr/bin/vim.tiny

$ sudo aptitude install apt-file
$ which apt-file
/usr/bin/apt-file
$ dlocate /usr/bin/apt-file     # fails; "sudo update-dlocatedb" needed
$ dpkg -S /usr/bin/apt-file
apt-file: /usr/bin/apt-file

{% endhighlight %}

* `apt-file`:
  1. separate install
  1. quick for files in installed packages
  1. for packages not installed, prepare apt-file database: `apt-file update`
{% highlight text %}
$ apt-file update
...
$ cd ~/.cache
$ du -sh apt-file
36M	apt-file

$ ls -l /usr/bin/thunderbird
ls: cannot access /usr/bin/thunderbird: No such file or directory

$ dlocate /usr/bin/thunderbird 

$ apt-file find /usr/bin/thunderbird 
thunderbird: /usr/bin/thunderbird
{% endhighlight %}
