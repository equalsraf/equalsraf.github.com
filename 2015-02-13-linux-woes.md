title: Linux Woes

Linux (the OS, not the kernel) is becoming a very disappointing piece of software to me! Let me elaborate, I've been a Linux user since circa 1999, but I don't think the world of Linux has frustrated me so much in the last [15 years](linux-timeline.png) as it does now.

The main problem as far as I am concerned lies at the userspace (no, not the kernel) - for the past 3-4 years there were massive changes at the Linux userspace, and most of them were not improvements. Here is a list in no particular order.

**NetworkManager** was introduced to unify network management and user control, it works  as a control daemon that desktop interfaces interact with. The problem here is that there are not that many **well supported** NetworkManager clients (outside GNOME and KDE). Smaller Desktop environments have no choice but to use *nm-applet*, since implementing their own takes too much effort. Previously this work was done by Linux distributions as part of their custom management interfaces.

**KDE4** was a rather disruptive change but even more disruptive was the migration of the main KDE components to Akonadi. KMail one the best mail apps around is now broken beyond repair, and I seriously doubt it will get back to the way it was. There is also some hate going around for plasma, while most seem unplaced my main concern here is that several KDE components are now unusable outside plasma/KDE.

**GNOME3** also suffered a backlash when doing their major version bump, with a lot of users wanting to keep the GNOME2 interface. But even more surprising was that a lot of big projects are now moving away from Gtk3. I am partial to Qt :D, but still all this contributes to destabilising the software stack as a whole

Meanwhile **Pulseaudio** took over as the default sound system in Linux. Personally I think the benefits it brings are insubstantial when compared with the problems it created, refactoring most of the sound stack for the benefit of network sound streaming seems overkill. Granted it is pretty stable now, but some features are still missing, running in system mode (e.g. for headless setups) is not officially supported, and the documentation/options for some of the modules are downright obscure. 

Finally the rise of **systemd** threw away with init scripts for most Linux distros. When I first read the initial systemd blog post I thought it was a great idea, that would allow late service startup and more importantly (to me) services like *CUPS* could be started as part of the user session. It is now almost impossible to boot a Linux system without systemd due to dependency issues.

A consequence of some of these points is that **DBUS** is now the *de facto* IPC mechanisms in Linux, even for system services like NetworkManager and Bluez. More importantly policy agents are now DBUS clients to system services implemented as part of the desktop environment - Power Management policy and Bluetooth pairing agents are now implemented as part of KDE/GNOME, but other desktop environments lack these components. In general I find DBUS to be a lot less stable than other IPC alternatives.

## Operational Shift

The main point is this: there has been a major shift in the operational responsibilities around Linux distributions. It used to be that the job of a good *distro* was to:

1. Get the base system to boot, i.e. setup the base system, including kernel version, drivers, init scripts and system services.
2. Provide management environment/configuration tools. Usually each distro had its own (Yast for SuSE, dpkg-* for Debian, etc)
3. Provide a set of package for users to install

**systemd** is quickly consuming the role of point (1.). The kernel version/drivers being the exception.

**Desktop environments** are now taking care of (2.) - each in its own way. Even package installation can involve the desktop environment (PackageKit).

Point (3.) is the only remaining aspect that is still fully handled by Linux distributions. But for many distros not even this is true (Ubuntu/Debian based) meaning they mostly handle branding and targeted software selection.

This prompts me to consider if we do need Linux distributions anymore. The major Linux distributions seem to be otherwise engaged in the affairs of their backers. Ubuntu is the flagship brand for Canonical, OpenSuse spends a lot of effort on the OpenBuildService tools and services, and Fedora ... (what is Fedora up to?)

All this seems to divert attention from what was the role of a distribution when I started using Linux - a community dedicated to build, test and support a usable software distribution. There are of course, exceptions to this issue but none seem immune to it.
 
## Fragmentation is a good thing

Over the years people complained that there was a lot of fragmentation around Linux, because there were "a lot of apps that did the same thing", or "which desktop is the best?", or "how do I install packages in ... and why is it different from ...". As annoying as these things could be for some people, variety and choice were a strong trait of this ecosystem, a fundamental part of its identity.

I see this unification of systemd as a threat to that variety and choice. Almost as if Linux is no longer the place to innovate in the Desktop. It used to be that I could mix-match the different pieces of software in a Linux system anyway I wanted (even if it was stupid) nowadays this is just not viable.

On a satirical side-note I have to wonder. If Linux distributions stopped calling themselves Linux distributions, would that help reduce the perceived fragmentation - a while back Ubuntu did a bit of word play around the Ubuntu kernel.

## On a positive note

On a final attempt to be more positive before closing up, let me try to convince myself that these things are actually a piece of the great master plan to make all our lives better.

Theoretically systemd could be evolving into a unified basesystem over which all Linux distros run, i.e. all distros would run over the same software stack (kernel+init+libc+service bits+seats) - which was why earlier I asked myself if there was any point in having Linux distributions anymore - for a lot of purposes this could be a good thing (e.g. application distribution).

User experience could also gain from this, since the desktop now seems to be *in control* of the system. This would fit well with the notion of a fully integrated experience.

## Post Scriptum Notes

* Maybe the smaller desktop environments should team up to figure out a common stack that they could share?
* If we wanted to build our own distro without systemd/pulse/dbus, how much effort would it be? i.e. what wouldn't work?
* There seems to be a reasonable systemd resistance: Slackware, ~~Gentoo~~, Funtoo, OpenRC
* I forgot to mention wayland - it is not clear to me how it fits in the long run
* For some systemd focused distro discussion - http://www.reddit.com/r/linux/comments/1y40c1/wich_distro_are_still_commited_to_not_using/
* LOL http://boycottsystemd.org/
* [Has modern Linux lost its way](http://changelog.complete.org/archives/9299-has-modern-linux-lost-its-way-some-thoughts-on-jessie)


