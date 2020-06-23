# 66

## Overview

> 66 is a collection of system tools built around s6 and s6-rc created to make the implementation and manipulation of service files on your machine easier.
[Wiki](https://wiki.obarun.org/doku.php?id=66intro)

### Trees

66 manages services and other as trees. From the [Wiki](https://wiki.obarun.org/doku.php?id=66intro):

> Trees are bundles of services, programs, daemons, that should run once, all the time, when you need them, depending on the type.

The tree `boot`, created in [Installation](#Installation), initialises system (setting hostname, timezone, open luks devices, etc) and starts agetty on tty1-4.

## Installation

Install `boot-66serv` package and enable mandatory tree `boot`:
```
# 66-tree -n boot
# 66-enable -C -F -t boot boot@system
```
### Configure
Configure bootprocess with:
```
# 66-env -t boot -e boot@system
# 66-enable -F -t boot boot@system
```
First command invokes $EDITOR to edit and the second applies the edits.

Add `init=/usr/bin/66` to kernel commandline to boot with 66 instead of runit.

### Compatibility to Void

This is necessary since there are no individual service files for 66 at the moment.

Enable servicefile `runit` to start runsvdir to start services in `/var/service`:
```
# 66-tree -nE runit
# 66-enable -t runit runit
```
> Reconfigure `boot@system` to not start any tty (TTY=!0) or remove symlinks in `/var/service` 