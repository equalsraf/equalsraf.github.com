title: Git Workflow: obsessing over data
description: Mirroring Git repositories for easy failsafe
date: 2011-02-08
nocomments:

Don't panic, this is not yet another Internet post on how should you use git, well
it actually is, but only slightly. So on Monday I was stressing with some co-workers
that they should commit often, in small incremental changes (etc, etc, common sense and
puppies). But at the same time I was also annoying someone to commit and push a 
considerable changeset, for fear of disk corruption, which happened before.

At this point, everyone was scratching their heads and asking: should I push often
for fear of data loss, even if I piss everyone off? Or should I keep my local branches
to myself and wait until a proper rebase of history. Fortunately for us these are not
mutually exclusive, disk is cheap, and there should be no problem for you to keep backups of
your local branches.

Which leads me to the purpose of this post, that is, to show off my git push workflow!


## Local, Origin and Backup

Typically my repositories live in three locations:

1. My local machine where I work, i.e. my laptop where I build and run code
1. The remote central server (origin) where the code is hosted for the world to see (gitorious, github, etc)
1. My remote backup server at home, that exists to ensure that even if my laptop is hit by lightning
   all my brilliant code is safe.

Typically we don't like the world the see our incomplete code, we want people to see it when its
brilliant or at least when it builds and runs. The workflow is simple, I keep incomplete code in both my laptop and
backup server, and complete features in all three machines.



## The Workflow

A few words of warning, I'll often talk about rebase here, this is mostly because I use branches, a lot. For every little feature/fix I
do, I create a topic branch. If you don't branch a lot, then you should probably replace rebase with merge bellow.

### Setting up my backup server

First let me create a remote for the backup server:

	:::text
	$ ssh raf@backup.home.pt git init --bare /home/raf/git-repos/website.git
	$ git remote add --mirror backup-home ssh://raf@backup.home.pt/home/raf/git-repos/website.git

The *--mirror* is the key here. It means when we push to the server, we send in all references.


### Before leaving work...
... commit one last time, and push to your backup server.

	:::text
	$ git push backup-home
	Counting objects: 49, done.
	Delta compression using up to 2 threads.
	Compressing objects: 100% (44/44), done.
	Writing objects: 100% (49/49), 10.00 KiB, done.
	Total 49 (delta 12), reused 26 (delta 2)
	To ssh://raf@backup.home.pt/home/raf/git-repos/website.git
	 * [new branch]      master -> master
	 * [new branch]      origin/HEAD -> origin/HEAD
	 * [new branch]      origin/master -> origin/master

There you go, all your source code is now safely tucked away for the night.
If you check the message, you'll see it copied all references, even the origin server,
it is basically a backup of our repository.

### When your changes are ready

You may want to rebase, to rewrite history, if you want to squash a commit that does not build, or simply to
have one commit per feature. Now you can push to the production AND the backup servers.


### Making it all automatic

If you lack the discipline to run _git push backup-home_ every day you can also automate this process. The following
script triggers a backup whenever you commit a change.

	:::text
	cd /path/to/repo
	echo 'git push' > .git/hooks/post-commit
	chmod 755 .git/hooks/post-commit
	

