gdb-7.11-patch
==============

Summary
-------

Patches are presented which restore objective-c language support.

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

Author
------

Giah De Barag
gdb@crelg.com
