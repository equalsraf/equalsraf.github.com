title: The Magic backup script
icon: http://www.androidpt.info/images/9/94/Backup.png
date: 2011-05-24
description: Doing remote rsync backups with a magic bash script
nocomments:

After looking high and low for a decent backup application I ended up 
giving up, and making my own bash script using rsync. I'm highly dependant
on client initiated remote backups, and it seems there are not many solutions
like that out there.

> **Update**: find the latest version [here](https://github.com/equalsraf/magick-backup-script), it
now supports local backups too.

Eventually I got tired of looking around and decided to cook a bash script,
based on some similar discussions I saw online. It seems a lot of people have
reached the same conclusion and created some various kinds of scripts.

But first, let me document what I would like a backup solution to provide.


## Requirements

So here are my picky requirements for a half decent backup solution for my Linux laptop:

1. Doesn't take absurd amount of space. If my home folder is 1GB and I make 3 backups, the total ammount of changes shouldn't total 1*3=3GB unless I've completely replaced all the files.
2. No special restore tools are required. Restoring a given backup should be a trivial task, such as accessing a folder and copying everything back.
3. Fast. Both doing backups as well as restoring them.
4. Support for client initiated backups. My laptop roams around a lot, so the backups must be initiated by the client.
5. Remote storage. I do a LOT of remote backups, duplicates, triplicates, whenever I get the chance.

## What are the alternatives

Here are the alternatives I checked before taking the lone-wolf path. The numbers in parenthesis are the requirements they did not fulfill:

* TimeVault(5) - In terms of design and features this is actually one of the front runners, but it only supports backup to disks, no remote shares.
* BackupPC(4) - Great professional level backup solution, but the backups must be started by a central server that pulls from all machines.
* Amanda(4) - Same as BackupPC. Also its a little to complex to configure.
* Bacula(4) - Same as the BackupPC
* Duplicity(3,2) - Most likely one of the most space-efficient solutions with support for lots of remote protocols, not to mention encrypted backups. But it stores the snapshots as a differential tar.gz. 
* FlyBack(2) - Uses Git for storage.
* Keep - Interesting, but apparently it is no longer maintained.

From all of the above, TimeVault was the closest to being what I wanted, but got left behind because I really need remote backups. Duplicity also looks like great but it does not fit my purposes.


## Enter the magic script

So basically I wanted a backup utility to backup some of my folders into remote servers (mostly ssh).
Rsync seemed like the ideal tool for the job, it can use ssh as transport and it can setup hardlinks
to save space between backups.

Backups are stored, one per folder called backup-DATE. There is also a symbolic link folder called _current_ that points to the last backup.

    $ ls backups/
    backup-2011-05-16T10:25:40  backup-2011-05-16T10:44:57  backup-2011-05-24T16:10:02
    backup-2011-05-16T10:34:09  backup-2011-05-17T09:02:43  current



Rsync saves space by hard-linking files between these folders, if there were no changes between them.
Restoring a particular backup is as simple as copying one of these directories. If you are interested
in knowing more about the rsync details check the _Rsync time machine_ link I left in the references.

The solution is split in two scripts: one that handles backups the other stores configuration 
settings (check bellow). You can create as many settings scripts as you want, as long as you keep
them on the same folder as backup.sh.

## backup-to-home.sh

Here is an example script to backup my home folder to homeserver.com:backups

    :::bash
    #!/bin/bash
    SOURCE=$HOME
    SSH_HOST="raf@homeserver.com"
    SSH_PATH="backups/"
    . backup.sh


## backup.sh

    :::bash
    # An rsync/ssh backup script
    # based on http://blog.interlinked.org/tutorials/rsync_time_machine.html 
    #
    # Some important variables that MUST be defined:
    # - SOURCE	The folder to be backed up
    # - SSH_HOST	The ssh host (i.e. user@host)
    # - SSH_PATH	The backups root path at the server
    #
    # The following are not mandatory
    # - EXCLUDEFILE A file with a list of ignore patterns($HOME/.rsync/exclude)
    
    if [ -z "$SOURCE" ]; then
    	echo "SOURCE is not defined"
    	exit
    fi
    
    if [ -z "$SSH_HOST" ]; then
    	echo "SSH_HOST is not defined"
    	exit
    fi
    
    if [ -z "$SSH_PATH" ]; then
    	echo "SSH_PATH is not defined"
    	exit
    fi
    
    
    DATE=`date "+%Y-%m-%dT%H:%M:%S"`
    TARGET="$SSH_HOST:$SSH_PATH/incomplete-$DATE"
    
    EXCLUDEFILE="$HOME/.rsync/exclude"
    
    RSYNC=/usr/bin/rsync
    RSYNC_ARGS="-azP \
    	--delete \
    	--delete-excluded \
    	--exclude-from=$EXCLUDEFILE \
    	--link-dest=../current \
    	$SOURCE $TARGET"
    
    # Check rsync
    if [ ! -x $RSYNC ]; then
    	echo "Cannot find rsync: $RSYNC"
    	exit
    fi
    
    # Check source dir
    if [ ! -d $SOURCE ]; then
    	echo "Source must be a directory"
    	exit
    fi
    
    # Check exclude file
    if [ ! -f $EXCLUDEFILE ]; then
    	echo "Exclude file not found: $EXCLUDEFILE"
    	exit
    fi
    
    CMD="$RSYNC $RSYNC_ARGS"
    
    # Wait for keypress
    echo "Preparing to snapshot"
    echo "    Command: $CMD"
    echo "<Press any key to continue>"
    read
    
    $CMD \
    && ssh $SSH_HOST \
    "mv $SSH_PATH/incomplete-$DATE $SSH_PATH/backup-$DATE \
    && rm -f $SSH_PATH/current \
    && ln -s backup-$DATE $SSH_PATH/current"


## When this does not work, and then what ...

Well a couple of days ago I saw my disk fail hard (well almost hard) and was spurred into a maniacal backup frenzy.
So just to be sure I decided to backup my files onto an external FAT disk I had lying around.
I quickly hacked the previous script into using local paths instead and then saw it fail miserably, why? Mostly because FAT does not like weird filenames and other things.



## References
+ [TimeVault](https://wiki.ubuntu.com/TimeVault)
+ [Amanda](http://www.amanda.org/)
+ [Rsync time machine](http://blog.interlinked.org/tutorials/rsync_time_machine.html)
+ [Duplicity](http://duplicity.nongnu.org/)
+ [Github project](https://github.com/equalsraf/magick-backup-script)




