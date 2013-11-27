# Quick notes on encfs for incremental backups

A while back I started handling my backups using a home baked script that
calls rsync for remote or local backups and uses hard-links to save some space.

Recently I wanted to do encrypted backups to a remote Linux box (as in I don't quite trust that box). Since I was already working over SSH I saw no reason to change strategy, and decided to extend my backup recipe to use ENCFS.

The problem with ENCFS is that it negates the advantages of rsync by design. The remote folder is encrypted, and the local folder is decrypted, comparing files for incremental backups would be pointless. Fortunately encfs provides the `--reverse` option that mounts an encrypted view of a decrypted folder -
this way we can run rsync over the encrypted view.

The backup script is nearly identical to my previous script, except it mounts encfs on start and unmounts on finish. After mounting encfs in reverse mode, rsync is called as usual (backup.sh is the backup script).

    #!/bin/bash
    
    # ENCFS mount point (see below)
    SOURCE=/tmp/$USER-backup-encfs-reverse/
    SSH_HOST="ruiabreu"
    SSH_PATH="/backups/shinypants/"
    EXCLUDEFILE=""
    
    if [ -d "$SOURCE" ]; then
    	echo "ENCFS mountpoint already exists, aborting: $SOURCE"
    	exit 1
    fi
    mkdir $SOURCE
    encfs --reverse $HOME/Documents $SOURCE
    
    . backup.sh
    
    fusermount -u $SOURCE
    rmdir $SOURCE

There are however two issues that I could not address

1. The excludes file is useless - since the file names are encrypted, the rsync excludes rule I was using to blacklist filenames no longer works.
2. You need to keep a local .encfs6.xml in the decrypted source folder. The file is used by encfs to mount the folder. I also keep a copy of this file in the remote server just in case. From a security standpoint this is a liability, the file holds the key (encrypted with your passphrase).

## References

- [The magic backup script](2011-05-24-the-magic-backup-script.html)


