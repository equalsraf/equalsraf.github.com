title: git workflow: pushing to multiple repositories in sync
description: A recipe for handling multiple git repositories
date: 2011-10-27
icon: http://cdn1.iconfinder.com/data/icons/containers/256/fork4.png
nocomments:


Recently I've been keeping an eye out for a solution to keep multiple git repositories in sync.
I've already discussed mirroring repositories in a previous post.
But in this case what I was looking for was the ability to push branches into multiple remote repositories, in one command.

The reason why I was looking into this is because I keep some projects in multiple online hosting services (github, gitorious, etc) at the same time.
After a while it becomes tiresome to push against all those remotes, so basically I'm looking for a way to save some keystrokes, and avoid stupid errors like forgetting to push into one of the remotes.


## The new Origin

One way to partially achieve this is by creating a new remote that aggregates all the remotes you want to push into. For example by adding the following into .git/config.

    [remote "Origin"]
    	url = git@gitorious.org:vim-qt/vim-qt.git
    	url = git@github.com:equalsraf/vim-qt.git
    	url = git@bitbucket.org:equalsraf/vim-qt.git

This essentially aggregates three remotes into a new one. I've named it "Origin" as a pun to the usual origin branch in git, but you can call it whatever you want.
You can even call it _origin_ (in lowercase), but I prefer a name that explicitly lets me know what I am doing.
At this point pushing into Origin will be equivalent of pushing into all the other branches.

    $ git push Origin master


A few notes about this remote before we continue. 
The remote, and any remote branches will not appear in git tools. 
So running *git branch -a*  will not show any Origin/master, for example. 
This is alright, since doing it would require all those remotes to be in sync which may not be true at all, but it has some disadvantages that we will discuss later.
You probably want to keep regular references to the individual remote branches anyway, so that you can push into them individually.
So my .git/config file looks like this,

    [remote "gitorious"]
    	url = git@gitorious.org:vim-qt/vim-qt.git
    	fetch = +refs/heads/*:refs/remotes/gitorious/*
    [remote "github"]
    	url = git@github.com:equalsraf/vim-qt.git
    	fetch = +refs/heads/*:refs/remotes/github/*
    [remote "bitbucket"]
    	url = git@bitbucket.org:equalsraf/vim-qt.git
    	fetch = +refs/heads/*:refs/remotes/bitbucket/*
    [remote "Origin"]
    	url = git@gitorious.org:vim-qt/vim-qt.git
    	url = git@github.com:equalsraf/vim-qt.git
    	url = git@bitbucket.org:equalsraf/vim-qt.git



## Fixing remote updates

The downside of the previous configuration is that it only works that well for pushing into the remote. 
If you try fetching from it nothing interesting happens, and more importantly pushing into it does not update remote references you may have to the individual remotes, because git sees those as unrelated remotes.
This unfortunate shortcoming requires us to run a remote update (or a git fetch for example), in order to have those remotes updated, i.e.

    $ git push Origin master
    $ git remote update

Instead of a single git push.
To make this even better we need to find a way to automatically run the _git remote_  command after the push.
Unfortunately git-hooks are of no use here, because there is no such thing as a post-push hook.

The best solution I was able to come up for this, was to create a git alias that merges those to commands, like this

    [alias]
        multipush = "!f() { git push $@; git remote update; }; f"

Basically _git multipush_ is an alias for git push, that is always followed by a call to _git remote_.
So now I would call

    $ git multipush Origin master

and that would be it, the master branch would be pushed into all remotes, and all the references would be updated.

An unfortunate side effect is that git bash auto-completion does not work for alias commands, which is unfortunate because I not keep pushing Tab for nothing.

## Watch out for dragons

This all works under the assumptions that you want to push from your local branch into multiple remote replicas, where YOU are the ONLY one pushing into those repositories.
Guard this assumption carefully, because for other workflows this might blow up in your face.
This seems to be fine for online Git hosting, where most workflows use pull-requests.

For example if you are not able to push to one the remote branches (network failure, server down, etc), the branches will remain out of sync, and might be synced again when you push later.
If someone else, other than you, were to push something into one remote branch (or all for that matter), then those branches would now hold distinct histories.




## The would-be-perfect solution!

For me, the would-be-perfect solution entails doing all this synchronization work on the server side.
Assuming that Bitbucket was my main repository, then pushing to Bitbucket would trigger a push into Gitorious and Github.
As far the git client is concerned there would be only one push (into Bitbucket).

This would be easier for the git client (a.k.a. ME) but harder for those services.
Because Bitbucket would have to be able to authenticate into those other two services.
This would be something like using a service hook, plus the ability to place some ssh keys in Bitbucket to be able to authenticate with the other services.

Does that sound like a good idea?
And more importantly would Github/Bitbucket be willing to support a service that pushes code to other services?
Or maybe what we really need is a new service just for this - codereplicator.org - wink, wink anyone?


