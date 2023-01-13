# diff

Diff is a command line tool for comparing two files or directories.

## Example

We have an upstream tarball and want to create a patch with some changes:

```
tar xfv upstream-version.tar.gz
cp -r upstream-version upstream-version.new
vi upstream-version.new/Makefile upstream-version.new/main.c
diff -urEbw upstream-version upstream-version.new > my.patch
```

This patch can later be applied with `patch -p1 < my.patch`
