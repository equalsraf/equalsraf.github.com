# Thoughts on Vim and Sublime Text


Spurred by a reddit post a lot of people started laying out their thoughts over Vim vs Sublime Text. With some heated arguments and rebuttals going around. So I am going to write down some of my reasoning so far on the Vim vs Sublime debate.

As a Vim user I also spent some time thinking over this, after I saw some friends moving away to Sublime Text. At the time I tried to get someone to give a concrete answer on **why** would I want to move, but nobody could offer me a convincing reason. Arguments would usually go along the lines of:

* Aesthetics
* GUI vs Console debate
* Plugin management is much better
* Some specific plugin that is better
* Reverse argument: Sublime has Vi mode ...
* The minimap
* multi-select
* .vimrc causes users to OCD 

**Disclaimer:** As with any other tech related flame, real-programmers should know that it doesn't matter which editor you use, only that you can [stand your ground](http://xkcd.com/378/) with it.

### Aesthetics

I assume most hardcore Vimmers will frown at this argument. But there is some truth in that aesthetics win people around, specially if you have to choose between any two things that otherwise seem equivalent (sorry it's that kind of shallow world) then people will go for *pretty*. This phenomenon is not dissimilar to people going for novelty items over older things. And it is fine if things are otherwise equivalent. 

Usability is probably a big part of this as well. People are used to certain usability patterns, and when a tool consistently behaves differently from what they expect, people move way for no concrete reason other than a feeling of uneasiness.

Granted that Sublime looks better than most Vim installations out there - unless you take your time to pimp Vim up, and even then it might not go that well :D.

The problem is they are not otherwise equivalent. Vim does not do things the same way as Sublime and vice-versa -- *More on this in a bit.*

### GUI vs Console debate

The previous argument sometimes degenerates into a GUI vs TUI debate. I'm slightly odd in this regard in that I do absolutely prefer the GUI to the Console Vim.

Make no mistake, the fact that Vim runs on a console allows it to run remotely everywhere, and is one of the features that keeps its userbase going. The ability to have both a GUI and a TUI for the same editor is almost unique to Vim (hi Emacs :D) and is a feature that should never be forfeited.

Now the reasons why I prefer the GUI are fairly trivial:

1. *Fonts/Color* - **in general** these work better on the Gui - and if you keep staring at your editor for 10h a day it does help. It is common to find a console out there that will mess something up, specially if it is not your machine.
2. *Consistent Behaviour* - The same GUI will mostly likely behave the same across machine/OS(s), the console might not (sigh Windows). Either being key mappings or weird chars, those things just annoy you after a while.

Granted these are not strong arguments, particularly if you always work in the same machine the console might be just fine. And then again, aesthetics does matter.

### Plugin management

Sublime did a great job with their plugin management. It is easy and just works, kudos to Sublime on this.

Pathogen and now Vundle have radically changed plugin management for Vim, making things much better. It is still not as *streamlined* as Sublime, because Sublime plugins is more of a curated environment. There are downsides to this as well, specially since Sublime is closed source and plugins need to be ported across versions. In Vim you primarily head to vimscripts.org or github for your plugins.

I understand the appeal of the Sublime plugin ecosystem, but I would not want Vim to do it the same way. Still there is room for improvement in Vim.

Conversely some of the issues with plugins in Vim are the result of teardown (22 years old) - I suspect we might also see some of that for Sublime as the user base grows (if not for the API switch). Extensible tools tend to face these problems on the long run.

### The right plugin for the job

For a pragmatic person, there is no counter argument for this - the right tool for the job is the right tool for the job! If someone took the time to write a great Sublime plugin for you that is awesome

But remember then that you are not using Sublime for the text editor, you are using it for the tooling - which is similar to saying that you treat Sublime as an IDE for a certain task, this a completely different mindset from that of Vim.

### The minimap

I kept a section just for the minimap :D. I find it a bit disturbing that it gets so much attention.

Apparently a lot of people like to visually browse a file. The concept seems inefficient to me, I would prefer to move based on search terms or marks. But people deal with these things differently. Maybe my brain is not wired for minimap browsing.

Even if I were to use the minimap, I would probably never keep it on by default. I would prefer if slid in when needed or even better for me would for the minimap to be this zoom-in/out transition.

### Reverse argument: Sublime has Vi mode

Doesn't even get close to Vim ... end of discussion!

Too many features are missing. For a Vim user, trying to use Sublime in Vi mode is a painful experience.

For a non-Vim user I assume that it can be useful to have some of its power at your fingertips, but if you find yourself using it too much just switch to Vim :D

### Multi-Select

This seems to be Sublime's competitor to Vim's selection modes and macros. I may be wrong here but so far these are the use cases I've seen:

1. Replace multiple occurrences of the same string
2. Insert the same bit of text in multiple locations at the same time
3. Some sort of join/split lines

With the exception of (2.) for most cases I don't see much advantage to multiple cursors vs Vim visual modes and macros. The one advantage that multiple cursors seem to hold is that they provide visual feedback (vs macros), but not vs selection modes/replace. But all this is still mostly mouse oriented, which I personally find annoying :D.

BTW there is a multiple cursor Vim plugin and it seems to be showing up in other text editors as well.

### .vimrc causes users to OCD

I suppose any technical oriented user of Vim can relate to this, since .vimrc offers endless possibilities we explore them all until exhaustion. And then we feel bad because whe spent our day editing .vimrc instead of writing awesome code.

Now there are two sides to this issue one good and one bad. The good side is that technically savvy users will always try to tweak up their tools. This is a good thing (specially for Open Source projects) because it is what makes them move forward - users think the tools can work better and push them to the limits at which point someone might develop improvements over it.

The bad side would be the tendency to obsess over your Vim settings to perfection at a level where you get no benefit out of it. But this is a far bigger problem, people who tend to OCD will find other things to OCD over, whether it is .vimrc, a big configuration panel or random colour themes. While I can agree that a change of scenery will help (on the short run) causing you to focus on what matters, you should really consider focus as wider problems in your life.

## The Vi mindset

The goal of Vi is to be the best text editor. The hard part of being a text editor is not to insert the text - if you write all your text in perfect order then any editor will do (hell I can use cat for that) and the only limitation would be your typing speed. The **really hard** part of being a text editor is **to edit**, to change text into what you want. If it is a very long text created in non sequential order you need to find the right place to insert it. Or if you have to do a lot of repetitive work, you would like to avoid that because it is error prone or it takes too long. These are the things Vi is good at - **text editing**.

The reason good text editors are also good programming tools is because, hopefully, you spend more time editing your code than calling you build tool, or changing its settings. If during your day, you spend five minutes writing code and the rest fiddling with build/deploy/ options then you probably want an IDE. Or if 50/50 times testing outputs vs coding you need to write some unit tests.

The underlying assumption in Vi is that you are here to edit text, and occasionally you may want to do something else (like calling Make).

Now this is where things get a bit more tricky, Vi took two design decisions that non-Vimmers may find hard to understand:

1. Vi is modal
2. Use the keyboard not your mouse and HJKL

A modal editor (and Vi in particular) assumes that you as the operator are doing one of two things: either your are inserting text, or you are doing something else. Like I said earlier inserting text is easy, it is the rest that is hard. So Vi treats these two things as separate. The INSERT mode is meant for blind text insertion, just put text in and it shows up in front of the cursor. The NORMAL mode is where you **move** around the file and trigger other modes for specific tasks (search/replace, selection, copy/paste, etc).

Because of this (or perhaps in order to do it -- causality evades me) Vi commands work like a language. Vi's power is the ability of these commands to express complex operations. Delete line/word/paragraph - all these are commands in a language that Vi understands - just say (d)elete followed by *what* you want to remove.

The keyboard/HJKL usually turns into a more convoluted discussion, sometimes followed by one of these arguments

1. Using the mouse strains your wrist
1. You want to keep your hands in the home row for posture
1. HJKL is faster

While the first two points are relevant for your health and you should take some time to ponder on those issues, any of these arguments are hard to hold as absolutes - I am sure there lots of mouse users out there with the eye accuracy of a sniper, healthy wrists and impeccable posture - so I wont try to argue my way through those, and besides I'm not a very fast typist anyway :D

Instead consider that the mouse is not a very reliable input for commands - I mean commands as assertive orders - keyboards are better suited for this since they have a wider range of combinations. So Vi allows for assertive commands that are harder to express with your mouse, e.g.

* If I want to go to line 33 I don't care if it is up or down, I just want to **go** there regardless of where I am now **:33**
* If I want to delete the current word **:diw**, I don't care where the mouse is right now or if I selected too much or little - I just want it gone

Expressiveness is the key to Vi's power and since Vi commands are exact orders they tend to be more reliable than your mouse. Now if are not sure what you want to do, and need to think it over, you tend to move your mouse around along with your eyes - that is fine for those situations. But Vi assumes that you are an assertive writer that wants things done - I secretly imagine myself shouting to my Vi minion - MINION DELETE LINE - instead I do it with my fingers.

Unlike languages though, there is no need for you to learn all of Vi(m), you can be sure I didn't. Learn only the bits you need to be productive and go no further.

* If your text editing job consists of fixing config/xml files all day learn only what you need to do efficient find and replace
* If you do professional spell checking then just learn to move and replace words or sentences.
* Similarly if you are an editor learn to order Vi to move/remove paragraphs
* Doing repetitive work on CSV tables - move/delete lines and find/replace
* Programmer? I'm sure there is a tool out there for you
* There no list of Vi commands you must know - it is just easier to identify what would make your life easier and search for that instead

## Why Sublime then?

Well they got a lot right, and realistically they seem to be one of the best non-Vi editors out there. Keeping in mind that the competition in that segment is Notepad++, gedit, Textmate (dead?-ish), etc, then Sublime is probably the best of the lot.

If you are a programmer then they got even more stuff right:
* sensible settings for code (tabspaces and what not)
* *project* settings
* good organisation features: tabs, layouts, file groups

The plugin ecosystem also helps putting Sublime ahead of the competition since the only alternative, other than Emacs, would be Textmate.

Finally Sublime is cross platform. And there aren't that many editors that can say this (scratch Emacs/Textmate). Vim is one of the few, but it also struggles with issues on Windows, where things are different enough to be annoying.

## In a nutshell

It seems Sublime is a great text editor, but it is not a Vim replacement. I'm not moving away from Vim any time soon - specially now that Neovim is on the horizon.

Still it makes for a top notch text editor that you definitely should consider if you're not going the Vi way. The only downsides being the price tag and the fact that it is not Open Source.

## References

1. [Reddit discussion on Sublime vs Vim](http://www.reddit.com/r/webdev/comments/19lobl/serious_discussion_sublime_text_2_versus_Vim_on/)
1. [Just use sublime text](http://delvarworld.github.io/blog/2013/03/16/just-use-sublime-text/)
1. [Vim rebutal](http://vpaste.net/fA7rH) 
1. [Another Vim rebutal](https://sublimebullshit.wordpress.com/2013/03/22/response-to-andrew-rays-blog-post/)
1. [Stackoverflow: Your problem with Vim is that you dont grok Vi](http://stackoverflow.com/questions/1218390/what-is-your-most-productive-shortcut-with-vim/1220118#1220118)
1. [Stackoverflow: What modal editors are available aside from vi/vim?](http://stackoverflow.com/questions/371618/what-modal-editors-are-available-aside-from-vi-vim)
1. [Bram Moolenaar: Seven habits of effective text editing](http://www.moolenaar.net/habits.html)
1. [Vim-multiple-cursors](https://github.com/terryma/vim-multiple-cursors)

