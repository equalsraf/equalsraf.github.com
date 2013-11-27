title: OBEXFTP and the elusive error code 64
icon: http://t1.gstatic.com/images?q=tbn:ANd9GcQzPYmwGDp03kjLPYDrFOjU1YIq8lPeNMeMnvPAasPJauQdgWCO
date: 2012-10-23

I was trying to push some files into my Nokia this morning and failed miserably at it ...

For a while now I've been getting used to see KDE's bluedeviz fail in some curious manner, specially when I use it from other desktop environments.
One google search and one manpage later, I decided to try to send the file from the command line, using

    hcitool scan
    ...
    obexftp -b <Bluetooth Addr> -p <filename>

All this seemed pretty straightforward, except for the fact that obexftp failed
with the following message:

    $ obexftp -b XX:XX:XX:XX:XX:XX -p file.pdf
    Browsing XX:XX:XX:XX:XX:XX ...
    Connecting..\done
    Tried to connect for 29ms
    Sending "file.pdf".../failed: file.pdf
    The operation failed with return code 64
    Disconnecting..-done

Now if that is not cryptic, I don't know what is. I have no idea what "error code 64" is supposed to be (although perror treats it a network error) and Google doesn't seem to be very helpful on this.
For some reason I can not explain, I decided to backtrack and try to force the bluetooth channel that obexftp should use. I did a scan for services and found my phone's OBEX File Transfer service in channel 4:

    $ sdptool browse
    ...
    Service Name: OBEX File Transfer
    Service RecHandle: 0x10007
    Service Class ID List:
      "OBEX File Transfer" (0x1106)
    Protocol Descriptor List:
      "L2CAP" (0x0100)
      "RFCOMM" (0x0003)
        Channel: 4
      "OBEX" (0x0008)
    ...

And changed the obexftp command to

    $ obexftp -b XX:XX:XX:XX:XX:XX -B 4 -p file.pdf

and it succeeded :D. Hell if I have any idea why obexftp is sending files to the wrong channel.



