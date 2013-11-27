title: Into the Void (...so far)

So after years using OpenSUSE I ditched it in favour of Void Linux. These are
the customary notes that always follow using a new system, tracking down the
little issues that I encounter.

## OpenBSD man

The OpenBSD man does not know to use less as its pager, add the following
to your shell

    export MANPAGER=less

## Fish wants manpath

When completing manpages, the fish shell complains that the command `manpath`
was not found. Add the following to ~/.config/fish/config.fish

    setenv MANPATH /usr/share/man

## Skype

There is a nonfree repo where we can get skype

    $ sudo xbps-install void-repo-multilib-nonfre void-repo-nonfree
    $ sudo xbps-install skype

Some issues afterwards between skype and pulseaudio, had to log out
for it to work properly (but maybe a HUP is enough).

## Avahi

As one would expect

    sudo xbps-install avahi
    sudo ln -s /etc/sv/avahi-daemon/ /var/service

But had to restart the dbus service for it to work

    sudo sv restart dbus

## Blacklisting Nouveau

I had some issues blacklisting nouveau, adding an entry in /etc/modprobe.d/*
was not enough (but it is still needed). This does not seem to be Void
specific, there are similar issues reported in other systems that use dracut -
anyway the module can be blacklisted from the kernel command line passed in
grub

Set GRUB_CMDLINE_LINUX_DEFAULT in /etc/defaults/grub

    GRUB_CMDLINE_LINUX_DEFAULT="loglevel=4 module.blacklist=nouveau rd.driver.blacklist=nouveau"

Update the grub config file

    sudo grub-mkconfig -o /boot/grub/grub.cfg

And reinstall Grub

    sudo grub-install /dev/sda

## mbsync

mbsync using IMAPS fails with the following error

    Error: SASL(-4): no mechanism available: No worthy mechs found

Just install the cyrus-sasl-modules pkg

    $ sudo xbps-install cyrus-sasl-modules-2.1.26_8


## CUPS: Samba backend

After installing cups and sbmclient the Samba option is still not available from
CUPS, create the backend symlink as

    $ sudo ln -s /usr/bin/smbspool /usr/lib/cups/backend/smb
    $ sudo sv restart cupsd

## cupsdLoadBanners: Unable to open banner directory

Sometimes this results in an error when trying to print (No such file or
directory), install **cups-filters** - Don't forget to restart cupsd too.

    $ sudo xbps-install cups-filters
    $ sudo sv restart cupsd

## git add -p

The command `git add -p common/shlibs` caused the following error

    git: 'add--interactive' is not a git command. See 'git --help'.

Its in a separate package, need to install **git-perl**.

## graphviz: dotty: cannot locate the lefty program

dotty (from the graphviz package) fails to run

    dotty: cannot locate the lefty program

Maybe there is no lefty pkg for void?

## libcrypto.a is not -fPIC

I think (but its late and I'm not certain) that libcrypto.a (from libressl-devel)
is not built using fPIC.

## dhcpcd+wpa\_supplicant

The default system setup uses dhcpcd for all network settings. For wireless
interfaces a dhcpcd hook launch wpa_supplicant. Just check
/usr/libexec/dhcpcd-hooks.

Or if yours is missing `ln -s /usr/share/dhcpcd/hooks/10-wpa_supplicant /usr/libexec/dhcpcd-hooks/10-wpa-supplicant`.

Sometimes wpa_supplicant fails to associate after suspending - I'm not sure
why, it just lingers there in the previous wifi connection. I suspect its
because connections created using the UI have disabled=1 in the config
file by default but this doesn't seem to be the only reason.

If needed you can hook to /etc/pm/sleep.d - and force wpa_supplicant to
reassociate on resume.

    #!/bin/sh
    #
    # /etc/pm/sleep.d/wpa_supplicant
    # On resume reassociate wifi
    case "$1" in
        hibernate|suspend)
            ;;
        thaw|resume)
            wpa_cli reassociate
            ;;
        *) exit $NA
            ;;
    esac

upowerd will need to be restarted for this to work.

## Where are the devel man pages?

    xbps-install man-pages-devel

## XBPS

Void uses XBPS, its own package manager. Packages are compressed tarballs (tar/xz) with the package contents (along with some metadata).

For example to list the contents of a package file

    xzcat file.xbps | tar --list

which is basically the same as `xbps-query -f`

### List repositories and installed packages

For a list of installed packages

    $ xbps-query -l

And similarly for a list of repos

    $ xbps-query -L

### Map files to packages

If you want to find which package owns a specific file

    $ xbps-query -o /usr/bin/avahi-publish
    avahi-utils-0.6.31_26: /usr/bin/avahi-publish (regular file)

The following also works, but its slow as hell

    $ xbps-query -Ro /usr/bin/avahi-publish

### xbps-src

**xbps-src** is one of the things that make XBPS awesome. Its a package
builder for all Void packages. Just drop a bash script with a build recipe
into srcpkgs and call xbps-src to build it.

Just clone the void-packages repository, it should have everything you need
in there

    $ git clone https://github.com/voidlinux/void-packages.git

Some friendly resources can be found here

- [the MANUAL](https://github.com/voidlinux/void-packages/blob/master/Manual.md)
- [Creating a Void Package](http://wiki.springlinux.org/index.php?title=Creating_A_Void_Package)

### Cross compile

It cannot be emphasized enough - xbps-src can cross compile to other targets, e.g. I build packages in my laptop for my raspberry pi, check the **-a** flag.

### Mapping library SONAME to packages

xbps-src might complain it cannot find a package matching a library name, e.g.

    SONAME: libxerces-c-3.1.so <-> UNKNOWN PKG PLEASE FIX!

The manual covers this, there is a file `common/shlibs` that stores a mapping
from SONAME to pkg name. The mistake I made was to include the pkgname-version
**WITHOUT** the revision number, this caused xbps-src to continue complaining as
usual.

### Playing with versions

I played a bit with xbps-src. Just for fun I decided to try and change the recipe
for libressl, causing a version bump in libcrypto, libssl and libtls, it ended up
being a nice to check what xbps-src is doing.

The libressl recipe is split into multiple packages

- libressl has the /etc settings, binary and license
- libcrypto35 has the shared lib major version 35 (.so.35 and .so.35.0.0)
- libssl35 has the shared lib major version 35 (.so.35 and .so.35.0.0)
- libtls9 has the shared lib major version 9 (.so.9 and .so.9.0.0)
- libressl-devel has the header files, symlinks to the static and shared libs
  (.a/.so) from the previous packages

Which is pretty neat, it means different versions of those shared libraries
can be installed on the same system and satisfy dependencies for other packages.

xbps-src does warn you if a package update conflicts with a previous package
forcing a library version bump, and lists the packages that need to be changed.

## void-mkimage

If you are into automating builds, have a look at the void-mklive repository. It holds the scripts that build void linux installers and images. You can whip up
your own raspberry pi image with a custom selection of packages in a few minutes.

    sudo ./mkrootfs.sh -p "unbound avahi socklog-void" rpi2-musl

This will generate a rootfs tar file, e.g.

    void-rpi2-musl-rootfs-20160118.tar.xz

For minor file changes to the rootfs you can update the tar file, for
example add my SSH key to the root user

    unxz --keep void-rpi2-musl-rootfs-20160118.tar.xz
    mkdir -p root/.ssh
    cat ~/.ssh/id_rsa.pub > root/.ssh/authorized_keys
    chmod -R og-rwx root
    sudo chown -R 0:0 root
    sudo tar --update -f void-rpi2-musl-rootfs-20160118.tar ./root/.ssh/authorized_keys

To generate the image

    sudo ./mkimage.sh -r f2fs void-rpi2-musl-rootfs-20160118.tar

The tar trick works ok, but its not very flexible.

## Other stuff

The next issues on my radar are mainly to package the stuff I really miss.

- vim-qt
- [Neovim/muslc](https://github.com/neovim/neovim/issues/3799)
- acpi_call, there is already a package for bbswitch, but that usually
  does not wokr on my laptop

