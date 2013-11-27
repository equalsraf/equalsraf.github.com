title: Slides with inkscapeslide
description: Making slides with inkscape
date: 2010-11-12
nocomments:

I've been growing fond of using inkscape for drawing content for my slides.
Figures, sequence diagrams and weird charts all end up going through inkscape,
either drawn from scratch or just for some graphical fixes before they end up
on the final slides.

So for some time now I've been searching for a way to make my slides directly from 
inkscape instead of using OpenOffice. I'm unhappy with Powerpoint like apps
because these days slides are mostly design work rather than random bullet
point sorting (Presentation Zen anyone?), and as such drawing programs are more
suited for this task.

Having said that, I started to work on some python scripts to sort, convert and glue together 
multiple SVG files, and them abandoned them when I found a much better solution 
called InkscapeSlide. InkscapeSlide is tiny python script that converts several layers in a 
SVG file into multiple slides for a PDF. The creation process goes more or less like
this: first you create multiple layers in inkscape, you can have as many layers per slide
as you want. I usually put the background in one layer and then use one layer per slide 
in the presentation. Finally you create a layer called "content" with a text label
that will map the layers into slides and run InkscapeSlide.

## The content layer

InkscapeSlide relies on the SVG having a layer named "content" with a single text
label. The content of this label maps the multiple layers into slides, each line
in the label creates one slide with one or more layers:

	title
	background, content
	+bullet1
	+bullet2
	+bullet3
	background, questions * 0.5
	
This example would create 6 slides. The first slide is made of a single layer 'title'; The
second uses two layers 'background' and 'content', this allows us to set a template layer
with elements that show up on all slides; The slides 3,4,5 are incremental, the '+' sign
means the previous slide plus; the last slide is similar to the second except it sets the 
"questions" layer to have an opacity of 0.5.

## Running InkscapeSlide

Running InkscapeSlide is as simple as issuing the following command in the
console:

	
	$ inkscapeslide slides.svg
	
The command will generate a pdf with the slides, or fail if the content layer does not
exist. Be careful to check the result tough, because InkscapeSlide wont complain about
missing layers.

Here is a full example [SVG](http://media.ruiabreu.org/files/2010-11-12-slides-with-inkscape.svg)
and the resulting [PDF](http://media.ruiabreu.org/files/2010-11-12-slides-with-inkscape.pdf) with
a few slides.

## Reference

[inkscapeslide](http://projects.abourget.net/inkscapeslide/)



