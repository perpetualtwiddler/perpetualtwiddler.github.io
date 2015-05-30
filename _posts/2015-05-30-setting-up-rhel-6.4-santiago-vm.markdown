---
layout: post
title:  "Setting up a compact RHEL 6.4 (Santiago) VirtualBox VM"
categories: virtualbox rhel
tags: minimal
---

Create new VM in VirtualBox GUI, allocate at least a 12GB hard disk.  Edit these `Settings`:

- Enable "Shared Clipboard" under `General -> Advanced`
- Under `Storage`, make the RHEL 6.4 ISO DVD available in the CD/DVD drive
- Edit `Network` settings if required (I tend to use "Bridged Adapter")
- Add a directory if required under `Shared Folders`

Install the minimal set of packages.

Reboot, copy contents of the RHEL 6.4 ISO DVD on the local hard disk.

Install VirtualBox Guest Additions (`Devices -> Guest Additions ISO`):

 - Find and run `./VBoxLinuxAdditions.sh`.  This might fail complaining of missing packages
 - Use `rpm -ivh <pkgname>` to install the named missing packages from the copied contents of the ISO
 - Rerun `./VBoxLinuxAdditions.sh`.  Installation should succeed but for failures about X display (to be fixed later)

Reboot, create a new user in the RHEL VM.  Confirm the directory named
in the `Shared Folders` settings is accessible to `root`.  Add the new
user to the `vboxsf` group: `usermod -G vboxsf newuname`. Relogin into
the new user's account, verify the shared folder is readable & writable.
Check output of `groups` command if required.

Copy contents of the RHEL 6.4 ISO into the shared folder to setup a local yum repository using `createrepo`:

- To run `createrepo`, install these packages using `rpm` : `createrepo`, `deltarpm`, `python-deltarpm`
- Run `createrepo` in the shared folder containing ISO contents: `createrepo . -g Server/repodata/*-comps-rhel6-Server.xml.gz`
- Add a `/etc/yum.repos.d/rhel64santiagoiso.repo` file with `baseurl` pointing to the directory in which createrepo was run:

<pre>
[RHEL64ISO]
name=RHEL64ISO
baseurl=file:///media/sf_rhel64santiago/rhel64yumrepo/
enabled=1
gpgcheck=0
</pre>

- Next, `yum clean all && yum update && yum makecache`
- Ensure `yum list` and `yum grouplist` produce long lists.

Install the basic graphical environment packages: `yum groupinstall
"Desktop" "Desktop Platform" "X Window System"` Avoid installing the
`Fonts` group, instead install individual fonts packages as required.
The `Fonts` group includes packages amounting to over 100MB.

- Make graphical boot the default if required: change default run level to 5 (from 3) in `/etc/inittab`
- Reinstall the VirtualBox Guest Additions package, this time around no failure should be reported
