# `vland` - a virtual userland manager


### About `vland`

`vland` is a virtual userland manager for x86_64 Linux, written in Dash shell.

`vland` allows non-root users, on x86_64 Linux systems, to install "virtual userlands" and then run programs (or build packages) inside those userlands.  More specifically:

-  `vland` automates the process of downloading, installing, and performing the essential configuration of a virtual guest userland.
-  After installing a virtual guest userland, `vland` can also execute programs, including interactive shells and package managers, inside the guest.


###  Quickstart tutorial

```
$  wget  https://github.com/parke/vland/raw/main/vland
$  /bin/sh  vland  --create  arch  arch-guest
$  /bin/sh  vland  --pkg  arch-guest  install  chromium
$  /bin/sh  vland  arch-guest  -nx  --  chromium
```

The above commands create an Arch Linux guest named `arch-guest`, and then install and run the Chromium web browser.  (The guest will be created inside `$HOME/.local/vland`.)

`vland` can also build packages.  For example, the below commands will build and install Arch Linux's `lua` package:

```
$  /bin/sh  vland  --pkg  arch-guest  build-install  lua
$  /bin/sh  vland  arch-guest  --  lua  -v
```

`vland` is a Dash shell script.  The actual virtual environment is created by [`lxroot`](https://github.com/parke/lxroot).  `lxroot` is a small C++ program. 
 Both run without root access.  `vland` is, more or less, an opinionated, high-level convenience wrapper around `lxroot`, with some added bells and whistles.

Learn more by reading the [**`vland` tutorial**](https://github.com/parke/vland/wiki/tutorial) and the [**`lxroot` tutorial**](https://github.com/parke/lxroot/wiki/tutorial).

### Videos

-  Lxroot presentation at PackagingCon 2021:  [**abstract**](https://pretalx.com/packagingcon-2021/talk/PMPUSW/)  |  [**slides**](https://pretalx.com/media/packagingcon-2021/submissions/PMPUSW/resources/20211110_Lxroot_7ILURuB.pdf)  |  [**video**](https://www.youtube.com/watch?v=1rw7ww0k_mk)


###  Supported guest Linux distributions

The below table summarizes the Linux distributions that I have used as guests with `vland` and `lxroot`.  

|  Guest distro  |  Can install as guest?  |  Can install packages?  |  Can build packages?  |
|  :--           |  :-:                    |  :-:                    |  :-:                  |
|  Alpine        |  yes                    |  yes                    |  yes                  |
|  Arch          |  yes                    |  yes                    |  yes                  |
|  Ubuntu        |  work in progress       |  work in progress       |  probably             |
|  Void          |  yes                    |  yes                    |  probably             |

Other Linux distributions may also work inside `lxroot`.  (Some level of custom shimming may be required.)

Due to `lxroot`'s safety, simplicity, efficiency, and ability to run without root access, `lxroot` has a few [limitations](https://github.com/parke/lxroot/wiki/limitations).  These limitations may or may not affect your particular use case.


### Learn more

-  [**use cases**](https://github.com/parke/lxroot/wiki/use_cases)
-  [**tutorial**](https://github.com/parke/vland/wiki/tutorial) - installation and usage
-  [**man page**](https://github.com/parke/vland/wiki/man_page)
-  [**wiki**](https://github.com/parke/lxroot/wiki)

<!--  version  20220119.1  -->
