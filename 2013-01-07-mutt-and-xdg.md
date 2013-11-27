title: Open attachments in Mutt with your desktop apps
description: Opening attachments in Mutt shouldn't be hard at all

Yes I'm a proud Mutt user, say what you will about user interfaces but no mail
client in the known universe can compare to the awesomeness of Mutt.

As ridiculous as this may seem Mutt is not very smart when it comes to
attachments, it basically uses the mailcap(4) file to figure what application
to open the file with. A mailcap file is basically a mapping between mimetypes
and application, for e.g. the following mailcap

    text/plain; cat %s
    text/html; lynx -display_charset=utf-8 -dump %s

would open text files with _cat_, and html files with _lynx_.

The problem with this approach is that you would have to add one line per
mimetype, for every single attachment type you want to open. This makes sense for
your console environment but looks absurd if you are running a full desktop environment
that already knows which applications to use.


## xdg-open

Large desktop environments usually have a command to open files with the correct
application, i.e. they look at the file extension and mimetype and choose the
application you configured for that file type. In GNOME this is called gnome-open
and in KDE kde-open. But if you have xdg-utils installed you can simply invoke _xdg-open_
and will use the appropriate program for your desktop environment. So running

    $ xdg-open ~/Downloads/1107.5728v2.pdf

should open your PDF viewer.

## mutt-open

The problem with using xdg-open with Mutt is that mutt expects the application being called
to block until it is finished, but xdg-open will exit as soon as it calls the app. Internally
Mutt operates as follows:

1. Get mailcap mapping
1. Copy attachment to /tmp folder
1. Open file with application from mailcap
1. Wait for application to close
1. Remove temporary file

The main problem being that Mutt will **remove** the file after the call is
complete which means any application that is called to open the file will be
unable to open the file, because it was already removed.

mutt-open is just a shell wrapper around xdg-open, that makes a copy of the
file before calling xdg-open. I first saw a script like this in a redhat bug report
(see the link at the bottom), I just rewrote it in python and added a couple of changes.

    if __name__ == '__main__':

        if len(sys.argv) < 2:
            print('Usage: %s <filename> [suffix]')
            sys.exit(-1)

        oldfile = sys.argv[1]

        suffix = file_sha1(oldfile) + os.path.basename(oldfile)
        if len(sys.argv) == 3:
            suffix += sys.argv[2]

        #newfile = mkstemp(suffix=suffix, prefix='mutt_bak_')[1]
        newfile = os.path.join('/tmp', 'mutt_bak_' + suffix)

        copyfile(oldfile, newfile)
        call(['xdg-open', newfile])


Do notice that the newly created file is never removed from _/tmp_, this is
mostly fine for me since my system will periodically clean up the /tmp folder.

Finally to make Mutt use mutt-open to open mail attachments you need to add the
following to your ~/.mailcap file.

    application/*; mutt-open '%s'; test=test -n "$DISPLAY"

This mailcap will use mutt-open for all attachments with mimetype
application/\*, but you can change the mimetype to be more restrictive. The
**test=** expression at the end of the line is there to ensure that this entry
is not used when X11 is not running.

I've also added an optional second argument to mutt-open, to specify a suffix
for the temporary file name suffix. This allows me to handle edge cases
where the attachment filename has no extension - as is the case with html formatted mails.

    text/html; mutt-open '%s' .html ; test=test -n "$DISPLAY"

## Remaining issues

There are still a couple of issues left:

1. The files are never removed from tmp, which may be a problem, depending on how you run your system
2. For the specific case of opening HTML files, the file encoding might not be your system encoding
   usually this is fixed by either converting the file on opening, or by specifying the encoding as
   an argument for the browser


## References

* [Bug 653249 - Mutt cannot open attachments w. xdg-open](https://bugzilla.redhat.com/show_bug.cgi?id=653249)
* [Character encoding in mailcap for mutt and w3m](http://www.in-nomine.org/2009/02/16/character-encoding-in-mailcap-for-mutt-and-w3m/)

