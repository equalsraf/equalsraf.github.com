title: Portable Vim settings
date: 2012-02-20
description: A quick hack to move your Vim settings around
icon: http://dribbble.com/system/users/2086/screenshots/121306/shot_1298917103.png?1309031063

Several people have acquired the habit of keeping their Vim settings under version control and sharing those settings as git/mercurial repositories.
Generally people do this for three reasons:

1. To keep a close eye on their settings ( raise your hand if **.vimrc** is the most important settings file in your pc).
1. To share their vimrc with other people.
1. ... and to have a trivial way to get their vimrc when they are in another terminal.

What I'm describing below is a quick hack on how to cleanly handle point n.º 3.
What I wanted was a way to quickly get my Vim settings when I am working at another terminal:

* Don't just store the _vimrc_ file but also the plugins and color schemes
* Don't need to overwrite the Vim config files on the system/home folder
* Do this as cleanly as possible - don't store redundant information, or require lots of steps to download it

The objective here is to keep all my Vim settings under version control and easily import then when on another machine, preferably without altering the local settings.
I'll be using Git for this, but I'm sure any other version control system will do.

## Keeping your Vim settings under version control

In Unix, Vim loads user settings from two locations: settings files from the home folder (.vimrc and .gvimrc) and additional files from the .vim/ folder (plugins, color schemes, etc)
The problem with these two locations is that they are not easy to keep separate from the remainder of my files, and I'm not keen on creating a git repository in my home folder.

To solve this I moved all my Vim settings files inside the _.vim_ folder and created symbolic links:

    $ mv ~/.vimrc ~/.vim/vimrc
    $ ln ~/.vim/vimrc ~/.vimrc

This way I can create a git repository inside my .vim folder to track all files.
There is no need to move files around after creating the initial symlinks.

It is also a good idea to have Git ignore some files such as plugins or settings that can only work on the original machine.


## Being portable

The core issue here is finding a way to get your settings from a remote git repo without needing to overwrite the local settings, that is ignoring the local _.vim_ and _.vimrc_.

I got this neat idea from a stackoverflow question on how to override local settings completely.
Vim already has a **-u** options to load settings from a given file instead of _.vimrc_, what is missing is a way to alter the runtime path to load plugins and.
Below is the script I found on stackoverflow along with some changes to fix the runtimepath and load a second vimrc file from the same folder:

    ::VimL
    " Portable VIM setup, ignore local vim settings and use settings from this folder
    " @warning this only works in a script - it can't be called from the Vim command
    "		prompt
    "
    
    " set default 'runtimepath' (without ~/.vim folders)
    let &runtimepath = printf('%s/site,%s,%s/site/after', $VIM, $VIMRUNTIME, $VIM)
    
    " what is the name of the directory containing this file?
    let s:portable = expand('<sfile>:p:h')
    
    " add the directory to 'runtimepath'
    let &runtimepath = printf('%s/,%s,%s/after', s:portable, &runtimepath, s:portable)
    
    let s:realvimrc = printf('%s/vimrc', s:portable)
    exec ":source" . s:realvimrc


So whenever I want to get my vim settings I will do:

    $ git clone git clone https://bitbucket.org/equalsraf/vimrc.git
    $ vim -u vimrc/portable-vim

And there you go, you are now running vim with my Vim settings.


## Extra tricks

These days most sane people use pathogen to manage their Vim plugins, which means that plugins are stored as Git/Mercurial repositories inside the _bundle_ folder.
If that is the case, then there is not point to storing the content of those folders in the repositories.
You might as well setup git submodules to handle those repositories.

I've added a link to my Vim settings in the references below.
If you check it you'll see that some plugins are inside the repository, while others are references to external git repositories.
So the previous example becomes:

    $ git clone git clone https://bitbucket.org/equalsraf/vimrc.git
    $ git submodule init 
    $ git submodule update
    $ vim -u vimrc/portable-vim

Now, not only do we have the config files and color schemes but we also have plugins.


## A few Caveats

Before I finish this up, here are a few a caveats

* This requires Git (or other version control system)
* Some distributions load vim settings from unusual locations, such as /etc/vimrc. Make sure your _vimrc_ has everything you need (set **nocp** and set backspace=2 for example)
* Some plugins will not work in other systems, if they have local dependencies.

## References

* [My vimrc](https://bitbucket.org/equalsraf/vimrc)
* [Stackoverflow: How can I override ~/.vim and ~/.vimrc paths (but no others) in vim?](http://stackoverflow.com/questions/3377298/how-can-i-override-vim-and-vimrc-paths-but-no-others-in-vim)
* [Pathogen](https://github.com/tpope/vim-pathogen)

