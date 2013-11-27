# Moving to LXQt

So It seems I am now an LXQt user. Not sure how long this will last, but so far I'm having fun using a minimalist Desktop Environment that packs all the features I need.

My personal history with desktop managers in Linux/BSD is rather convoluted,
every 6 months or so I end up switching (or switching back) DEs in search
of a better one. Some invariants remain though

* I prefer KDE over GNOME - mostly because I want the power features of KDE
  to the over-simplification of GNOME. KDE is my default go-back-to DE when I
  get tired of whatever I am using (or I just pick up a minimalistic one e.g. i3/fluxbox).
* I like tilled window managers, but no need to be too radical about it - KWin can do a bit of tilling (for windows moves) but it is not enough
* **i3** has one thing that no one else has - it is the best in terms of usability
  of multiple desktops+screens (which is why it is incompatible with other WMs/pagers)
* KDE/Plasma has been slightly disappointing in the 4.X release, not because of the paradigm shift, but because it is a lot less stable than 3.X.
* GNOME/Unity are just not for me, I don't fit into their usability choices.

I tried LXDE before and liked it, but the problem with lightweight desktop environments these days is that they need a lot of work before they become usable:

* policykit
* powermanagement
* xrandr support
* X11 hints and lots of freedesktop specs
* NetworkManager (nm-applet seems to be the only choice)
* Decent notification support

LXQt caught my eye some months ago because they were porting to Qt, but more importantly they merged efforts with the guys from Razor-Qt.

On a side note, I am actually able to run i3 as the window manager for LXQt (check the end for a LXQt panel bug), which ended up being great because I get the best of both worlds. A minimalistic DE with all the features I need, and I great window manager.

## The not so good bits

I ran into weird a issue with the LXQt XRandr support, where one of my screens would not turn on. I can't be sure if it was a bug or a result of some conflict with KRandr.

The *volume control* panel plugin came configured for pulseaudio and pavucontrol - since I don't use pulseaudio I've disabled the plugin and just use kmix instead.

The desktop wallpaper (and icons) are managed by **pcmanfm**, for some reason it was using my Downloads folder for the desktop. I think this can be changed in the settings file, but I've disabled it completely (through the Autostart options). I wish this was more straightforward though.

I ran into some crashes with i3. Pressing Alt+F1 (LXQt panel menu) crashes i3, and the i3 crash handler is unresponsive. This seems to be an i3 bug. But it looks fixed in i3 **4.8**.

**xdg-su** falls back to the default xterm based password popup. This seems to be a problem with either OpenSUSE, or xdg-utils.

## References

* [lxqt-panel crashes with i3 wm when switching worskspace ](https://github.com/lxde/lxde-qt/issues/240)
* [kabal](https://bitbucket.org/equalsraf/kabal/)
* [Crashes i3-wm when start button is clicked](https://github.com/lxde/lxqt-panel/issues/22)


