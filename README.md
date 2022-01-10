# `vland` - a virtual userland manager


### About `vland`

`vland` is a virtual userland manager for x86_64 Linux, written in Dash shell.

`vland` allows non-root users, on x86_64 Linux systems, to install "virtual userlands" and then run programs (or build packages) inside those userlands.  More specifically:

-  `vland` automates the process of downloading, installing, and performing the essential configuration of a userland.
-  After installing a userland, `vland` can also execute programs, including interactive shells, inside the userland.


### `vland` and Lxroot

`vland`'s virtualization is provided by [Lxroot](https://github.com/parke/lxroot) (via unprivileged Linux namespaces).  Both `vland` and Lxroot are designed to be lightweight and flexible.  `vland` is, more or less, an opinionated, high level, convenience wrapper around Lxroot, with some added bells and whistles.


###  Supported guest Linux distributions

The below table summarizes the Linux distributions that I have used as guests with `vland` and `lxroot`.  

|  Distro  |  Can install root filesystem?  |  Can install packages?  |  Can build packages?  |
|  :--     |  :-:                           |  :-:                    |  :-:                  |
|  Alpine  |  yes                           |  yes                    |  yes                  |
|  Arch    |  yes                           |  yes                    |  yes                  |
|  Ubuntu  |  work in progress              |  work in progress       |  unknown              |
|  Void    |  yes                           |  probably yes?          |  maybe??              |

Other Linux distributions may also work inside `lxroot`.  (Some level of custom shimming may be required.)

Due to `lxroot`'s safety, simplicity, efficiency, and ability to run without root access, `lxroot` has [some limitations](https://github.com/parke/lxroot/wiki/limitations) that may not be present in more heavyweight virtualization tools.  These limitations may or may not affect your particular use case.


### Videos

-  Lxroot presentation at PackagingCon 2021:  [**abstract**](https://pretalx.com/packagingcon-2021/talk/PMPUSW/)  |  [**slides**](https://pretalx.com/media/packagingcon-2021/submissions/PMPUSW/resources/20211110_Lxroot_7ILURuB.pdf)  |  [**video**](https://www.youtube.com/watch?v=1rw7ww0k_mk)


### Learn more

-  [use cases](https://github.com/parke/lxroot/wiki/use_cases)
-  [tutorial](https://github.com/parke/vland/wiki/tutorial) - installation and usage
-  [man page](https://github.com/parke/vland/wiki/man_page)
-  [wiki](https://github.com/parke/lxroot/wiki)
