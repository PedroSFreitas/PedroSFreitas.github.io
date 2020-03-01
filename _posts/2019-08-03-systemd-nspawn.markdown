---
layout: post
title:  "systemd-nspawn: my thoughts and its uses"
date:   2019-08-03 15:41:00 -0300
---

<div>
  <p><blockquote><strong>Note</strong>: <i>if you came here to get some quick references check the <a href="#engagement">TL;DR</a> section.</i></blockquote></p>
</div>

Quoting from the man page, `systemd-nspawn` is "*in many ways similar to <a href="http://man7.org/linux/man-pages/man1/chroot.1.html" target="_blank_">
chroot(1)</a>, but more powerful since it fully virtualizes the file system hierarchy, as well as the process tree, the various IPC subsystems and the host
and domain name... It may be invoked on any directory tree containing an operating system tree*".

Bottom line and as the <a href="https://wiki.archlinux.org/index.php/Systemd-nspawn" target="_blank">ArchWiki</a> says: "*is like the chroot command, but it
is a chroot on steroids*". Not only that but based on the description from the manual page (extract above) `systemd-nspawn` can definitely be used as an
alternative to well-known operating system-level-virtualization... such as <a href="https://en.wikipedia.org/wiki/Docker_(software)" target="_blank">Docker</a>.

But why have another solution for os-level-virtualization (containers) if we already have <a href="https://en.wikipedia.org/wiki/LXC" target="_blank">LXC</a>,
<a href="https://www.docker.com/" target="_blank"> Docker</a>, <a href="https://en.wikipedia.org/wiki/Chroot" target="_blank">chroot</a> and
<a href=" https://en.wikipedia.org/wiki/OS-level_virtualisation#Implementations" target=" _blank"> others</a>? A follow up question to that could be: Why should I use
`systemd-nspawn` if I can easily use `Docker`? Without going into the <a href="https://www.reddit.com/r/archlinux/comments/4lzxs3/why_did_archlinux_embrace_systemd/d3rhxlc/"
target="_blank">war that is among the community regarding systemd</a>, it's a fact that a lot of distros out there adopted it you like or not! If that is the case for
the distro you're using, then there is a high chance `systemd-nspawn` is already present in your machine. This means you have a way to virtualize another GNU/Linux
environment with just a couple of commands. **You can spin up a container within seconds with tools already at your hands; without installing more packages!**

If you're anything like me this is great news, specially since I'm constantly trying new softwares/application, playing around with AUR and also trying to
port packages to <a href="https://blackarch.org/" target="_blank">BlackArch</a>. Now I can use `systemd-nspawn` to spin up a vanilla Arch Linux environment and do
my tests without messing with my host installation, leaving it as clean as possible. And of course everything that you can do with Docker you can probably do with
`systemd-nspawn` too.

### Network

By default the guest, i.e., container, running on `systemd-nspawn` will share the network with the host. This is not the default behavior with Docker, so could
cause some confusion. But you can tweak the configuration in order to create private network or a bridged one. I highly recommend checking the
<a href="http://man7.org/linux/man-pages/man1/systemd-nspawn.1.html" target="_blank">man page</a> for `systemd-nspawn` since it covers all configurations,
features and more.

But going back to the point that the guest shares the network with the host, this make testing applications way easier and out-of-the-box. For instance, I didn't
know what to expect from Jekyll so I ran an vanilla Arch installation as guest using `systemd-nspawn`, installed all the necessary packages (ruby), gems and did
all the development using the container. In fact, I'm right now writing this blog post inside the container using `systemd-nspawn`. The good thing is that my base
installation is untouched and since the guest shares the network I test the blog by accessing the localhost:4000 on my browser. :)

This behavior can also make it a bit easier, or just out-of-the-box, to sandbox applications. You can spin up a small Linux base installation, install your browser
and use it from within the container. Of course you'll need to configure X properly and for that just visit:
<a href="https://wiki.archlinux.org/index.php/Systemd-nspawn#Use_an_X_environment" target="_blank">ArchWiki - Systemd-nspawn: Using X</a> and follow the instructions.

### My thoughts

`systemd-nspawn` can be a great alternative for os-level-virtualization and it's probably already installed in your distro. Having the network shared with the host
by default create the easiest scenarios for testing and sandboxing applications.

However, it always comes down to your use case. You should use solutions that address your problems in an effective matter. For me, `systemd-nspawn` won my space
for containers since it is easy to use and already in my system (base installation).

<div id="engagement">
  <h3>TL;DR: Arch Linux using systemd-nspawn</h3><p></p>
</div>

Running an Arch Linux on systemd-nspawn, considering you are on an Arch host:

```bash
# Install arch scripts to get pacstrap
sudo pacman -S arch-install-scripts --needed

# Create a folder to hold the environment files (system tree):
mkdir arch-container

# Use pacstrap to create the base directory tree for Arch, we don't need the kernel
pacstrap -i -c arch-container base base-devel --ignore linux

# Spin up the container
sudo systemd-nspawn -b -D arch-container
```

`-b` / `--boot`: automatically search for an init program and invoke it as PID 1, instead of a shell or a user supplied program.

`-D` / `--directory=`: directory to use as file system root for the container.

You'll be able to login as `root` without any password. After that you can tweak the environment as you see fit, the same way if it was a regular installation.

For full reference check:
<a href="https://wiki.archlinux.org/index.php/Systemd-nspawn#Create_and_boot_a_minimal_Arch_Linux_distribution_in_a_container" target="_blank">https://wiki.archlinux.org/index.php/Systemd-nspawn#Create_and_boot_a_minimal_Arch_Linux_distribution_in_a_container</a>
