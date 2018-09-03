<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [gdb-7.11-patch](#gdb-711-patch)
    - [Summary](#summary)
    - [Impact](#impact)
    - [Version](#version)
    - [Description](#description)
    - [Usage](#usage)
    - [Changed Files](#changed-files)
- [Background](#background)
    - [Why](#why)
    - [Status](#status)
    - [Readline Quirk](#readline-quirk)
    - [References](#references)

<!-- markdown-toc end -->

# gdb-7.11-patch


## Summary

Restore objective-c language support


## Impact

* Objective-c language mode
* All operating systems


## Version

All this information applies to the **gdb-7.11** branch.


## Description

These patches restore GDB’s objective-c mode ability to evaluate such
objective-c expressions as:

```objective-c
b -[class method:]          // set breakpoint
p [object message:arg]      // method invocation
p @"foo"                    // @"string" construct
p @selector(foo:bar:)       // @selector() construct
p (NSAutoreleasePool *)pool // objective-c classes
p "foo"                     // regular c strings (yes,
                            // even c became broken
                            // in objective-c mode!)
```
*Without these patches, none of these work, and trying them causes GDB to crash.*

## Usage

To get these patches into your GDB, do:

```
git clone -b gdb-7.11-branch git://sourceware.org/git/binutils-gdb.git
(apply these patches)
./configure
make
```


## Changed Files

Only four files have been affected.

```
gdb/breakpoint.c |   1 +
gdb/c-exp.y      | 162 ++++++++++++++++++++++++++++++++++++++++++++++++++-----
gdb/objc-lang.c  |   5 +-
readline/input.c |  12 +++--
4 files changed, 161 insertions(+), 19 deletions(-)
```

*Note: You can ignore the `readline/input.c` patch if you have the
latest gnu `readline` library and do not rely on the old copy of
`readline` inside the GDB source. (See [below under “Readline
Quirk”](#readlinequirk) for details.)*


# Background

The patches enable GDB to debug Objective C code.

If someone has time to run the GDB test suite and validate these
changes, we can try to get them into the GDB repository.


## Why

I believe GDB’s objective-c language support was contributed by
NeXT in the mid 1990s.

Around 2010 or 2011 (late gdb 6 to early gdb 7), one of the GDB
maintainers made a big change which refactored a lot of the support
for the C family of languages.

Because of insufficient testing of this change, GDB’s objective-c
language mode became unusable.

The damage was so bad that even basic C language support was broken in
objective-c language mode (objective-c is a proper superset of C).

Even though clang and lldb are popular alternatives to gcc and gdb,
there are still users of gcc and gdb.

After studying GDB’s internals, I managed to figure out the puzzle and
fixed all the problems. This repository contains the resulting patches
(or diffs).

It is a pity to leave gdb broken when the fix is this simple and
actually finishes the work that the original GDB maintainer was trying
to do.


## Status

You can just get the GDB source, apply these patches, and be happy.

If you want this code pulled into the GDB source code so that it gets
into future GDB distributions, there is more work to be done. The GDB
maintainers asked for further tests to be run. This GitHub repository
is a baby step in that direction.


## Readline Quirk

The GDB code has its own copy of `readline` that is older than the
latest gnu `readline`.

If you are building GDB as part of a large package system with the
latest `readline` library, then please just ignore the
`readline/input.c` patch and the rest of this section.

Otherwise, if you are buildng GDB standalone, and you wish to rely
upon the `readline` that is built into GDB, then read on.

After I built GDB from source, I noticed that terminal command-line
input was broken. Characters typed were not echoed and command editing
keystrokes, like backpace, were transmitted as ascii bytes into the
input. Also `TUI` mode would not work.

Since I was only interested in the objective-c language support, I
just decided to hack the two or three lines of readline code to let
the terminal command-line input work a little better (and it got `TUI`
mode working too).

<i>Again: You can ignore the readline/input.c patch if you have the
latest gnu readline library and do not rely on the old copy of
readline inside the GDB source.</i>


## References

Background on GDB and Objective C

* [GDB and Objective C](https://sourceware.org/ml/gdb-patches/2016-09/msg00170.html) (Some examples of how GDB evaluates Objective C expressions)

Original patch contributions (all included in this repo):

1. [Restored Objective-C language support (Part 1)](https://sourceware.org/ml/gdb-patches/2016-09/msg00100.html)
1. [Restored Objective-C language support (Part 2)](https://sourceware.org/ml/gdb-patches/2016-09/msg00382.html)

Reported bugs fixed by these patches:

1. [Bug 11925 - objc-exp.y doesn't handle floats with suffixes, e.g. 1.1f](https://sourceware.org/bugzilla/show_bug.cgi?id=11925)
1. [Bug 20501 - Creating NSString convenience variable crashes GDB](https://sourceware.org/bugzilla/show_bug.cgi?id=20501)
1. [Bug 20503 - ObjC runtime debugging support is severely broken](https://sourceware.org/bugzilla/show_bug.cgi?id=20503)
