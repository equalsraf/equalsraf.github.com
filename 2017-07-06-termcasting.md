title: termcasting

I cant remember where I saw it, but there was this nifty little rust app to share your tty over the network.

Historically the original tool for this was `script`, but there is also `ttyrec` and `termrec`

## ttyrec

First lets resize your tty into a standard size

	$ stty rows 24 cols 80

Now start recording your terminal.

	$ ttyrec termcastfile

This will record your session into the file termcastfile.

We can serve this file with netcat on port 9999. I'm using openbsd's nc command
here, but any other tool that forwards the output of ttyplay to the client can
be used.

	$ ttyplay -p termcastfile | openbsd-nc -l -p 9999 -k >/dev/null

In the client I find it useful to disable echo, otherwise some terminal input
can be printed out over the output - be warned that this breaks bash output

	$ stty -echo

you can watch this stream with nc (telnet works too)

	$ nc localhost 9999

## termrec

termrec seems like an alternative to ttyrec. It is supposed to store additional information
such as the terminal dimensions. Unfortunately I was unable to make it work for a streaming
setup.

termrec also has a command `termplay` that works a lot like mplayer but for ttyrec files.

## ttyrec in web pages

TermRecord generates web pages that display script/ttyrec files using term.js. playterm.org and
showterm.io use similar tricks to display ttyrec files in webpages.

## Forward onto remote ssh host

If you are behind a firewall you can easilly forward this port onto a public ssh host

	$ ssh -NR \*:8080:localhost:9999 vps

Check the sshd_config option `GatewayPorts`, by default ssh does not allow listening on the wildcard address.

## some scripts

termcast.sh to simultaneously start recording a session and stream over a tcp port

	#!/bin/sh
	# Usage: termcast [tcpport]
	set -eu
	
	PORT=${1:-9999}
	
	# first we force a size of 35 rows by 120 cols, this is the larger size
	# accepted by playterm.org
	stty rows 35 cols 120
	
	# the session will be saved in this file
	SESSION=$(mktemp termcast-XXXXXX.ttyrec)
	echo "Storing session in $SESSION"
	
	ttyplay -p $SESSION | openbsd-nc -l -p $PORT -k >/dev/null &
	SERVERPID=$!
	echo "Server started on port $PORT (pid: $SERVERPID)"
	
	trap "kill $SERVERPID" EXIT
	
	ttyrec $SESSION
	echo "Session recording finished: $SESSION"
	kill $SERVERPID

termwatch.sh to watch a stream from a remote host

	#!/bin/sh
	set -e
	
	# Disable echo while printing to the terminal
	stty_orig=`stty -g`
	stty -echo
	nc $1 $2
	stty $stty_orig

## Conclusions

Its like twitch for your terminal :D

## References

- [ttyrec - a tty recorder](http://0xcc.net/ttyrec/)
- [streaming setup with script from HN](https://news.ycombinator.com/item?id=3784793)
- [playterm hosts public tty records](http://www.playterm.org/)
- [showterm is a ttyrec player](http://showterm.io/)
- [termrec](https://github.com/kilobyte/termrec)
- [TermRecord](https://github.com/theonewolf/TermRecord)
