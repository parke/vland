# `vland` - a virtual userland manager

`vland` is a virtual userland manager for x86_64 Linux, written in Dash shell.

`vland` allows non-root users, on x86_64 Linux systems, to install "virtual userlands" and then run programs inside those userlands.

More specifically: `vland` automates the process of downloading, installing, and performing the essential configuration of userlands.  After installing a userland, `vland` can also execute programs, including interactive shells, inside that userland.  `vland`'s virutalization is provided via [Lxroot](https://github.com/parke/lxroot).  (Lxroot itself uses unprivelged Linux namespaces.)  Both `vland` and Lxroot are designed to be lightweight and flexible.  `vland` is, more or less, an opinionated, high level, convenience wrapper around Lxroot.

## An example

As an example, let's try to do the following:

1.  install `vland`
2.  use `vland` to install an Arch Linux userland named `arch`
3.  install the Chromium web browser inside the `arch` userland
4.  run the Chromium web browser inside the `arch` userland

Here's how to do that:

```
$  wget  https://github.com/parke/vland/raw/main/vland
$  chmod  +x  vland
$  ./vland  --create  arch
$  ./vland  arch  -nr  --  pacman  -Syu  chromium
$  ./vland  arch  -nx  --  chromium
```

## Some comments

*  By default, userlands are installed in `$HOME/vland`.
*  `vland` uses `lxroot`. `vland` may also use the [`aria2c`](https://github.com/aria2/aria2) download utility.  If either or both are absent, `vland` will download and use statically compiled versions as needed.
*  In the command `./vland  arch  -nr`, the `-nr` option grants network access and simulates a UID of zero (i.e. `root`).
*  In the command `./vland  arch  -nx`, the `-nx` option grants network access and shares the host's Xorg server.
*  All of the options after `./vland arch` are simply passed through to `lxroot`.
*  At present `vland` can create `alpine`, `arch`, `void` and `void-musl` userlands.
*  I expect it would be both technically possible and straightforward to extend `vland` to support architecures beyond `x86_64`.

The full syntax of the two `./vland` commands in the above example is:

```
vland  --create  distro  [userland]  [overlay]
vland  overlay  [userland]  [option ...]  [-- command [arg ...]]
```

By default, `vland` always uses both a "userland" and an "overlay".  (I will explain the difference later.)  If only one is specified, then both the userland and overlay will have the same name.

As I said previously, any `option(s)` and `command` are simply passed through to `lxroot`.  Please refer to the Lxroot documentation for further details.

##  Userlands & overlays

(This section is not yet written.)

##  `./vland --self-test`

If `vland` doesn't seem to be working, you may try running `vland`'s self test, as follows:
```
$  ./vland  --self-test
```

`vland`'s self test will attempt to do the following:

1.  Install Alpine Linux into a userland named `vland-self-test`.
2.  Install `vland` itself inside `vland-self-test`.
3.  Run `vland` inside `vland-self-test` to install a nested Arch Linux userland inside the `vland-self-test` userland.
4.  Then finally run ...
5.  ...  (on your host) a child `vland` process to run:
6.  ...  (in `vland-self-test`) a grandchild `vland` process to run:
7.  ...  (in the nested Arch userland) an interactive (simulated root) shell.

If it works, you should see a bunch of output, and then, after (probably) 30 to 120 seconds, something like this:

```


----------------------------------------------------------------
  vland  --self-test  now launching interactive root shell ...  
----------------------------------------------------------------


+  /bin/sh /home/user/vland vland-self-test -n -- vland arch -nr
+  exec lxroot /home/user/vland/land/vland-self-test /home/user/vland/over/vland-self-test -n -- vland arch -nr
+  exec /home/user/vland/dist/bin/lxroot-x86 /home/user/vland/land/arch /home/user/vland/over/arch -nr

root  -nr  ./arch  ~  
```

If `vland`'s self test fails, please consider submitting a bug report on Github.
