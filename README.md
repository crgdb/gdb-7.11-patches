gdb-7.11-patch
==============

Summary
-------

* Restore objective-c language support.
* Relative to the head of the gdb-7.11-branch.


Impact
------

* Objective-c language mode
* All operating systems


Description
-----------

These patches restore GDB’s objective-c mode ability to evaluate such
objective-c expressions as:

```objective-c
p [object message:arg]      // method invocation
p @"foo"                    // @"string" construct
p @selector(foo:bar:)       // @selector() construct
p (NSAutoreleasePool *)pool // objective-c classes
p "foo"                     // regular c strings (yes,
                            // even c became broken
                            // in objective-c mode!)
```


How to use
----------

To get these patches into your GDB, do:

```
git clone -b gdb-7.11-branch git://sourceware.org/git/binutils-gdb.git
(apply these patches)
./configure
make
```

List of Files Changed
---------------------

Four files are affected.

```
gdb/breakpoint.c |   1 +
gdb/c-exp.y      | 162 ++++++++++++++++++++++++++++++++++++++++++++++++++-----
gdb/objc-lang.c  |   5 +-
readline/input.c |  12 +++--
4 files changed, 161 insertions(+), 19 deletions(-)
```

*Note: You can ignore the `readline/input.c` patch if you have the
latest gnu `readline` library and do not rely on the old copy of
`readline` inside the GDB source. (See below under “Readline Quirk”
for details.)*

Background
----------

#### Objective-C Support

I believe GDB’s objective-c language support was originally
contributed by someone from NeXT in the mid 1990s. Whoever you are,
thank you.

A few years ago (in version 6 iirc), one of the GDB maintainers made a
big change which refactored a lot of the support for the C family of
languages. (If you look through the GDB discussion lists, you’ll find
this change.) Because of insufficient testing, GDB’s objective-c
language mode became absolutely unusable. Even basic C language
support was broken in objective-c language mode (recall that
objective-c is a proper superset of C).

After studying GDB’s internals, I managed to figure out the puzzle and
fixed all the problems. This repository contains the resulting patches
(or diffs).

#### Status

You can just get the GDB source, apply these patches, and be happy.

If you want this code pulled into the GDB source code so that it gets
into future GDB distributions, there is more work to be done. The
GDB maintainers asked for their tests to be run before they pull these
patches into the GDB repository. This GitHub repository is a baby step
in that direction.

#### Readline Quirk

If you are building GDB as part of a large package system with the
latest `readline` library, then plese just ignore the
`readline/input.c` patch, and stop reading here. Otherwise, if you are
just buildng GDB standalone from its own source, read on.

After I built GDB from source, I noticed that terminal command-line
input was broken. Characters typed were not echoed and command editing
keystrokes, like backpace, were transmitted as ascii bytes into the
input. Not even `TUI` mode would work.

The problem is that the GDB code (this 7.11 branch anyway) has its own
copy of `readline` that is older than the latest gnu `readline`. Funny
that the GDB maintainers retain this old version and do not either
update it or clean it out.

Since I was only interested in the objective-c language support, I
just decided to hack the two or three lines of readline code to let
the terminal command-line input work a little better (and it got `TUI`
mode working too).
