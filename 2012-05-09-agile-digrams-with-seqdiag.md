title: Agile diagrams with seqdiag
description: How to save hours of your time with a brilliant sequence diagram generator
date: 2012-05-09
icon: http://thetechnomancer.com/img/tools/Toolbox.png


If you happen to work on research, you'll find that diagrams drawing takes a big chunk of your time.
And even worse after several hours carefully drawing your diagram, you come to realise two things:

1. Your diagram looks like crap
1. Drawing a couple of boxes and some lines took you half an hour or more

This is increasingly more frustrating if you are drawing diagrams with lots of rules, such as
UML sequence diagrams, and have to fight against whatever software you are using because an arrow is
misplaced.

A few years back I wrote a python script(instaseq) to generate diagrams for my Master thesis based on a simple description language. 
It is still available at my bitbucket.
But it was rather limited and only worked for the most basic diagrams in single step transitions.

More recently I found this brilliant application called *seqdiag*, that generates sequence diagrams from
a description language (similar to graphviz).
The benefits are that it works even for complicated diagrams, and the results are aesthetically pleasing.
Here is an example from their website:

	seqdiag {
	  browser  -> webserver [label = "GET /index.html"];
	  browser <-- webserver;
	  browser  -> webserver [label = "POST /blog/comment"];
	              webserver  -> database [label = "INSERT comment"];
	              webserver <-- database;
	  browser <-- webserver;
	}

Calling seqdiag on this file will create:

![](http://blockdiag.com/en/_images/seqdiag-f85b863bf1cd0388ce653e7ffc2eb09e590bed5f.png)

There are lots of other great features like nested calls, automatic numbering and request/response in
a single. But I am going to skip all that and just show a few quirks I found and how to address them.
For a fairly thorough tour of its features check their examples page.

## Caveats and Tricks

Seqdiag can generate multiple output types (png, pdf, svg), but I mostly use *pdf* to embed diagrams in academic papers.
Calling seqdiag as follows:

    $ seqdiag file.diag -Tpdf --font /path/to/font.ttf

My most annoying issues with seqdiag so far are:

1. Wide margin around diagram
1. Too much vertical space around elements

I was unable to find a solution to reduce the margin around the diagram, using seqdiag.
Instead I use *pdfcrop* to remove the excess margin:

    $ pdfcrop file.pdf file.pdf

To reduce vertical spacing seqdiag offers a diagram attribute *span_height* (and also span_width for horizontal spacing) to reduce the space between elements.

## A quick final remark

seqdiag is part of blockdiag, that also offers tools for other types of diagrams: block, activity and network diagrams.
I have not tested them in detail, but if they work as well as seqdiag, then we are in for a treat.

For the most part I am very pleased with the productivity boost this provides me.
There is some of lack of documentation (or its hidden in blockdiag's page), but other than that the results are very positive.

## References
* [seqdiag - simple sequence-diagram image generator](http://blockdiag.com/en/seqdiag/)



