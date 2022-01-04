# `vland` - a virtual userland manager

`vland` is a virtual userland manager for x86_64 Linux, written in Dash shell.

`vland` allows non-root users, on x86_64 Linux systems, to install "virtual userlands" and then run programs inside those userlands.

More specifically:
*  `vland` automates the process of downloading, installing, and performing the essential configuration of a userland.
*  After installing a userland, `vland` can also execute programs, including interactive shells, inside the userland.
*  At present, `vland` can download and install userlands for: Alpine, Arch, and Void.

`vland`'s virutalization is provided via [Lxroot](https://github.com/parke/lxroot).  (Lxroot itself uses unprivileged Linux namespaces.)  Both `vland` and Lxroot are designed to be lightweight and flexible.  `vland` is, more or less, an opinionated, high level, convenience wrapper around Lxroot, with some added bells and whistles.

For more information, please see the following pages:
*  [tutorial](tutorial) - download, installation, and usage tutorials
*  [man page](manpage) - the man page for `vland`
*  [wiki](/parke/lxroot/wiki) - the Lxroot wiki contains additional information
