gdb-7.11-patch
==============

Summary
-------

Patches are presented which restore objective-c language support on all operating systems.

These patches are relative to the head of the gdb-7.11-branch.


Impact
------

These changes impact only objective-c language mode.


Description
-----------

These patches enable GDB to (once again) evaluate such objective-c
expressions as:

```objective-c
p [object message:arg]      // method invocation
p @"foo"                    // @"string" construct
p @selector(foo:bar:)       // @selector() construct
p "foo"                     // regular c strings (even these were broken in objective-c mode!)
p (NSAutoreleasePool *)pool // objective-c classes
```


How to use
----------

```
git clone -b gdb-7.11-branch git://sourceware.org/git/binutils-gdb.git
(apply patches)
./configure
make
```

Questions
---------

Giah De Barag<br>
gdb@crelg.com
