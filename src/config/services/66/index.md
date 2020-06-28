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

One can boot after the step described above but but they will soon discover that
the reboot,shutdown and poweroff commands do not work, but **CTRL+ALT+DEL** does!

That is because the new init can capture the later, but the rest are still
from the void-runit package and cannot work with 66. The 66 commands can be used 
directly by running /etc/66/halt, /etc/66/reboot etc as root, or you can symlink
them, replacing the runit commands.

- Rename the old runit commands (as root):

```
# for i in /usr/bin/{reboot,poweroff,halt,shutdown}; do mv $i $i.runit; done
```

- Create the symlinks for the 66 commands (as root):

```
#  for i in {reboot,poweroff,halt,shutdown}; do ln -s /etc/66/$i /usr/bin/$i; done
```

The two commands above will solve the problem in a system booted with 66, but
they will create a similar in a runit-booted system. To reverse them, do the folowing:

- Delete the symlinks:

```
# rm /usr/bin/{reboot,poweroff,shutdown,halt}
```

- Rename the original runit scripts/binaries (as root):

```
# cd /usr/bin && for i in {reboot,poweroff,shutdown,halt}; do mv $i.runit  $i;   done
```


### Compatibility to Void - working with runit services

66 can work with the existing runit services, replacing only the first stage
of the regular  init process. This is necessary since there are no individual 
service files for 66 at the moment.

That can be accomplised by configuring runit to run only services that are not
included in the boot-66serv package by removing the symlinks to their service
directories. That should be the agetty services:

```
# rm /var/service/agetty-*
```

If you need to reverse the above, in case you need to boot  with runit, fist boot as a single user/
recovery and then give the following command. 

```
# for i in {agetty-tty1,agetty-tty2,agetty-tty2,agetty-tty3,agetty-tty4,agetty-tty5,agetty-tty6}; do ln -sr /etc/sv/$i /var/service/$i; done
```
and reboot the system.

The use the remaining runit services, the runit tree should be created and then the runit service enabled and started:

```
# 66-tree -nE runit
# 66-enable -t runit -S runit
```

