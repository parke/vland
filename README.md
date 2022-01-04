# `vland` - a virtual userland manager

### About `vland`

`vland` is a virtual userland manager for x86_64 Linux, written in Dash shell.

`vland` allows non-root users, on x86_64 Linux systems, to install "virtual userlands" and then run programs (or build packages) inside those userlands.  More specifically:

*  `vland` automates the process of downloading, installing, and performing the essential configuration of a userland.
*  After installing a userland, `vland` can also execute programs, including interactive shells, inside the userland.
*  Supported userlands include: Alpine, Arch, and Void

### `vland` and Lxroot

`vland`'s virtualization is provided by [Lxroot](https://github.com/parke/lxroot) (via unprivileged Linux namespaces).  Both `vland` and Lxroot are designed to be lightweight and flexible.  `vland` is, more or less, an opinionated, high level, convenience wrapper around Lxroot, with some added bells and whistles.

### Learn more

*  [use cases](https://github.com/parke/lxroot/wiki/use_cases)
*  [tutorial](https://github.com/parke/vland/wiki/tutorial) - installation and usage
*  [man page](https://github.com/parke/vland/wiki/man_page)
*  [wiki](https://github.com/parke/lxroot/wiki)
