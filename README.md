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
[object message:arg] messages,
@"foo" NSStrings, and
@selector(foo:bar:) selectors.
"foo" c strings
p (NSAutoreleasePool *)pool
```

Author
------

Giah De Barag
gdb@crelg.com
