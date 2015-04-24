chromebook-scripts
================
This repository contains some scripts which may be of use to people using their Chromebook in dev mode. Testing to date has been done on an [Acer C720][1]. Your mileage may vary. I use these scripts on a daily basis without any problems, but **no warranty** or support of any kind is offered. Do feel free to report any bugs, though.

bruschetta
----------
Usage:

    bruschetta [COMMAND [ARG]...]

*Bruschetta* is a script to simplify starting a [crouton][2] session on removable storage (USB or SD card). To install, simply copy it to somewhere on your path and make it executable:

    $ sudo cp bruschetta /usr/local/bin
    $ sudo chmod 0755 /usr/local/bin/bruschetta

The filesystem must be labelled so it can be identified, and it must support the POSIX semantics necessary for a working chroot (unjournalled ext4 works well). In its root must be the `bin` and `chroots` directories installed by crouton. Only the desired chroot(s) need to be present; therefore it is possible to have each installation on its own nice, big flash drive.

There is no need to invoke it as root, as long as sudo is available. `COMMAND`, if supplied, must be the name of one of crouton's launch scripts. As a convenience, the prefix "start" will be added if the specified script does not exist. e.g.

    $ bruschetta xfce4 trusty

will enter an [XFCE4][3] session in the "trusty" chroot by running the `startxfce4` script, if that target is installed.

Bruschetta will unmount the external filesystem if nothing else is using it when it exits. 

To configure, set variables in `~/.bruschetta/bruschetta.conf`:

`FSLABEL`: The label of the external filesystem. This needs to not be the same as any other attached filesystem. (default: *crouton*)

`DEFAULT_CMD`: The crouton command to execute if none is specified on the command line. (default: *enter-chroot*)

`MOUNTPOINT`: Where the external filesystem should be mounted. Although the device may have been automounted, crouton requires more permissive mount options than Chrome OS's (very sensible) security policy allows. (default: */run/bruschetta/`$FSLABEL`*)

`MOUNT_OPTS`: Filesystem mount options. There should be little need to change this; if you do, be aware that the first four are essential. (default: *rw, dev, exec, suid, async, dirsync, noatime, discard*)

`HOSTNAME`: Optionally assign a hostname to the system before starting crouton. For the sake of sanity, this will be ignored if the current hostname is not "localhost". Note that some programs can become confused if the local hostname is not resolvable. See `update-local-hosts` below. (default: not set)

A different configuration file can be specified by setting the environment variable `BRUSCHETTA_CONFIG`. Alternatively, create a link to the script with a different name, and a second file under `~/.bruschetta` to match. e.g.

    $ cd /usr/local/bin
    $ sudo ln -s bruschetta xfce4
    $ echo "DEFAULT_CMD=xfce4" >~/.bruschetta/xfce4.conf

Note that the configuration is just shell script, and will be **executed** as **root**. Bruschetta will refuse to run if the config is world-writable, but if somebody can inject variables into your environment then they can simply point it somewhere else. Scripts with superuser rights are rarely watertight: You have been warned.

The code may be used under the same two-clause license as [FreeBSD][4]. See the file `LICENSE.BSD2` for the license text.

unmount-lib-modules
-------------------
This is a very simple script to unmount all filesystems mounted under `/lib/modules`, so you can install your own kernel modules. Call it from (or paste it into)  `/etc/rc.local` inside the chroot. As it does no error checking there will be ways to break it by racing it etc, but it Works For Me under sensible conditions.

As applying copyright to a one-liner is perverse, this script is released into the public domain (via the [Creative Commons CC0 tool][CC0]).

update-local-hosts
------------------
This script rewrites the local host entry in `/etc/hosts` to ensure that the current hostname is resolvable inside the chroot. If your hostname is always "localhost" then you won't need this. Again, simply call it from `/etc/rc.local` and let it do its thing. And again, errors are not explicitly handled, but it should work if used as intended.

This script is also released into the public domain ([CC0]).

----------
**Copyright © 2015 Julian R Yon** < [julian@julianyon.net][7] >
At the user's discretion, this document may be used under either of the following licenses:

 - [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).
 - [2-clause BSD License](LICENSE.BSD2).

To view the terms of each license, follow the links above. You are reminded that there is NO WARRANTY. This document and the software it accompanies may contain errors, omissions or be deficient in other ways. Use at your own risk.

[1]: http://www.acer.co.uk/ac/en/GB/content/series/c720
[2]: https://github.com/dnschneid/crouton
[3]: http://www.xfce.org/
[4]: http://www.freebsd.org/
[7]: mailto:julian@julianyon.net
[CC0]: http://creativecommons.org/publicdomain/zero/1.0/
