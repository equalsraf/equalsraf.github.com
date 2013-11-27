# Ditching pulseaudio

Some quick notes on ditching pulseaudio. There are several valid reasons to throw it away. I have sound latency issues, cropped sounds with PA. Also I do need to have sound without a user DBus session.

## Disabling pulseaudio

You might not want to uninstall pulseaudio. Depending on your distribution some packages may depend on the pulseaudio package, so uninstalling is not an option.

You can just disable it, by disabling its auto spawn, edit /etc/pulse/client.conf and make sure to set 

    autospawn = no

That is all, just kill the pulseaudio process and you are done.

## Multi user environments

In multi user environments you might get an error from applications trying to play sound but failling with "permission denied". Setting /etc/asound.conf as follows fixes it for me.

    pcm.dmixer {
      type dmix
      ipc_key 1024
      ipc_key_add_uid false
      ipc_perm 0666                       # mixing for all users
      slave {
        pcm "hw:0,0"
        period_time 0
        period_size 1024
        buffer_size 8192
        rate 44100
      }
      bindings {
        0 0
        1 1
      }
    }
    
    pcm.dsp0 {
      type plug
      slave.pcm "dmixer"
    }
    
    pcm.!default {
      type plug
      slave.pcm "dmixer"
    }
    pcm.default {
      type plug
      slave.pcm "dmixer"
    }
    ctl.mixer0 {
      type hw
      card 0
    }

## Things that work

- MPlayer
- VLC
- Firefox
- mpv

## Things that need a little nudge

**speech-dispatcher** assumes pulseaudio is available, but you can change the backend to alsa in ~/.config/speech-dispatcher/speech.conf

    AudioOutputMethod "alsa"

The **lxtqt-panel** mixer uses pulseaudio but can be configured to use ALSA instead.

VM snapshots in **VirtualBox** need to be shutdown and configured to use ALSA.

A good way to look for packages that might not work is to look for packages that depend on libpulseaudio e.g.

    xbps-query -X libpulseaudio

## Things that don't work

- Skype

You might want to try to use apulse for these.

## References

- [Re: Sound permission problem - solved](https://www.redhat.com/archives/rhl-list/2007-November/msg05220.html)

