# Booting Raspberry Pi Root in read-only

So last week a lot of people around here were looking into how to boot a raspberry pi with
the SD card partition in read only mode - this came after a couple of annoying
data losses.

A lot of us are using the R-Pi as cheap automation system - 
as terminal(tty) server, 3D print server, server monitoring, and more ideas are
constantly on the way in.  Personally I use it as a media center attached to my
TV.

The main problem with this kind of use are that the devices are constantly on, and might have
to suffer trough power failures - *For my part I'm ready to admit that I've
pulled the plug a couple times*.

So I wanted to have a way to keep the root partition as read only, thus avoiding corruption
on the SD Card. I can always plug in some USB storage if I need it, and in some cases there
really is no need for persistent storage of any kind.

The following are a set of steps to accomplish this on Arch Linux (2013-05-14.img), if you use
Raspbian instead check the references at the end for a working solution.

### 1. Mount the root partition as read-only (ro) on boot

In raspberry pi the root partition flags can be set as kernel parameters in
/boot/cmdline.txt. You should add an **ro** flag right after the **root=** parameter.

    selinux=0 plymouth.enable=0 smsc95xx.turbo_mode=N dwc_otg.lpm_enable=0 console=ttyAMA0,115200 kgdboc=ttyAMA0,115200 console=tty1 root=/dev/mmcblk0p2 ro rootfstype=ext4 elevator=noop rootwait

### 2. Find a new location for resolv.conf

The /etc/resolv.conf file stores the list of DNS nameservers your machine
uses, if it is not writable it will be empty and applications won't be able
to resolve names.

I've moved mine into /tmp/ (which is a tmpfs partition by default):

    # rm /etc/resolv.conf
    # ln -s /tmp/resolv.conf /etc/resolv.conf

### 3. Mount /var/{tmp, log} as tmpfs

The /var/tmp directory is used to store temporary files, and the /var/log for system logfiles.

You can mount these two as tmpfs by adding the following to /etc/fstab

    tmpfs	/var/log	tmpfs	nodev,nosuid	0	0
    tmpfs	/var/tmp	tmpfs	nodev,nosuid	0	0

Notice that I did not make a symbolic link between /var/tmp and /tmp. This is
intentional, doing so seems to trigger a bug with systemd - search for *step
NAMESPACE* to find some references.

### 4. Disable some systemd services

Systemd has a couple of services that can be disabled:

* systemd-readahead does some optimisations based on disk access patterns
* systemd-random-seed stores entropy information between reboots

These can be disabled via systemctl

    # systemctl disable systemd-readahead-collect
    # ...
    # systemctl disable systemd-random-seed

### 5. Replace syslog-ng with rsyslog

*syslog-ng* will not run if /var/lib is not writeable (it stores persistent data there), so I've
removed it and started using rsyslog instead.


### 6. Disable ntpd and use a netctl hook instead

By default Arch will sync time on boot using ntpd (if an internet connection is available).
This is a problem for the Raspberry Pi because it has no internal clock, and if
a connection is not immediatly available, *ntpd* might take while to sync.

Personally I've had better results in setting a hook to update the
time whenever a new connection is available. The following works using dhcpcd
in */usr/lib/dhcpcd/dhcpcd-hooks/50-ntp*

    #!/bin/sh
    case "${reason}" in
    	BOUND|INFORM|REBIND|REBOOT|RENEW|TIMEOUT|STATIC)
    		# "-g" allows for large time differences
    		ntpd -qg
    		;;
    esac

Now you can disable the ntpd daemon

    # systemctl disable ntpd

## How to use 

The system will start in read-only mode, whenever I need to write into the root
I remount the root as read-write and then back:

    # mount -o rw,remount /
    # pacman <...>
    # mount -o ro,remount

Keep an eye out for the following:

* Some services really need to write to /var/lib, dhcpcd will complain about the leases
  but otherwise it works, others might not work at all (syslog-ng)
* I've mounted /var/log as tmpfs, there is probably a better way to do this
  such as using ramlog, or logging into a remote system

Finally you don't have to do it exactly like this, you can:

* Create a separate writeable partition
* Write to USB flash drive

It really depends on your setup.

## References

* [Raspberry Pi gets an Industrial Perennial Environment](http://riscpi.co.uk/nutcom-services-ltd/)

