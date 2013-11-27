title: Build Systems

Build systems are curious pieces of software, in that the requirements for a
good build system are different from most other pieces of software. Over the
years I've used the following

- Autotools
- qmake
- CMake
- scons
- bjam
- gyp
- insane scripts or batchfiles, or whatever
- msbuild, and VS
- Maven, ant

Notice I did not include **Make** by itself, because it would not be a fair
comparison, Make does one thing well, but finding external dependencies and
testing compilers is not it. If your project can build with just a Makefile
that is good and done, but if you need to work on systems that don't have
Make then that is a different problem altogether.

> Full disclosure: I am a big fan of CMake, its one distinguishable feature
> above all others is cross platform support, which quite frankly is all that
> matters to me at this point, that said it sometimes turns into a messy pile
> of abstractions

Generally speaking writing build configurations is awful, either the
syntax/semantics are terrible or the multiple build combinations make
everything too confusing for a single person to understand.

I can't recall exactly what was the first build system from that list I ever
used, but it was probably Autotools. I'm pretty sure I hated it at the time,
but it did get the job done.

Mostly build files are the kind of code you only change when things are
breaking apart. Most people don't dive into an autoconf file unless the build
is broken (which in itself already makes you grumpy to being with). This I think,
contributes towards the hate people feel over them. If they just work, then
you don't care, and live in ignorant bliss - until they break. We talk about
code reviews and proactive bug hunting for code, but have you ever done it for
your build files?

qmake for example is a very lean system, for the simplest cases that is. If
your are developing in Qt, and don't have any intricate dependencies going on
it is a wonderful system. For complex projects, specially if you have
dependencies, it is as painful as any other system out there (lots of .pri and .pro).

Then you have less know tools like SCons, and bjam. At some point I honestly
hoped SCons succeeded - I think we all hoped it would magically inherit some
of the simplicity of Python, it didn't. bjam and other toolset specific build
tools seem to me are mostly useless outside their respective projects. Not to
say they don't make sense - it is natural for any development environment to
attempt to be self contained.

One thing that I learned from SCons and bjam is that it does not matter if the
tool is good, bad or awful. The primary feature that makes or breaks a
buildsystem is support for finding dependencies, almost nothing else matters,
provided it can find libraryX or programY in most system. Developers just want to
add `find(LibXYZ)` and move on. Most of the times this only comes after a build
system is around for a while, because developers are more likely to contribute
a module to find their own libraries if they already use the build system.

CMake did well for itself because it has generators for a lot of systems, and
it now has modules to find the most popular tools/libraries (putting 
in par with Autotools in Unix). This and some natural support for cross
compiling seem to be its defining features. On a bad day the syntax and the
internal cache can be as unforgiving as any Autotools setup.

Gyp seems to compete for the same slot as CMake. I've only used it briefly, or
rather I've only seen it break - which no doubt is a bias over any opinion I
can offer - the only honest comment I have at the moment is "why the hell are
we using json for this?!".

## Dependencies are contagious (in Windows)

In the Unix world we have several tools that help with dependencies, pkg-config
finds build options for you, and your package manager installs libraries and
development headers.

Windows however is an entirely different matter, there is no package manager to
install library XYZ, and no tools to help you find it. Many projects build
their own dependencies along with the project, and bundle all the libraries
along with the package installer.

For the most part they end up relying entirely on their build system to manage
dependencies. More importantly this results in this infectious choice of build
systems "We choose build system X because most of our dependencies also use
it.". I've seen a lot of this lately on discussions of CMake vs Gyp.

This also has a curious side effect in Windows. Several build scripts don't
care about install targets, they just build the lib/headers and leave them
wherever they stand. It seems someone assumed I would just take the time and
effort to copy those files to the proper places - or more accurately install
instructions are managed by different tools.
In the Unix world this would just be rude, wouldn't it?

## TLDR;

- All build systems suck as programming languages go
- Third party  support is the only thing that matters for a build system
- People never remember the build system for its good parts only the bad,
  because they never realised it is there, until it fails

These notes, sort of turned into a discussion, that sort of turned into a
bashful [slide deck](http://ruiabreu.org/slides-build-systems/#1). I would
love to hear [comments or ideas to extend them](https://github.com/equalsraf/slides-build-systems).


