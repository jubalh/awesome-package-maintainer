# patch
Patch is a command line tool that applies a [diff](diff.md) output file (patch) to an original file.

One important parameter is `-p` which strips leading slashes, basically ignoring parts of the path.

For patches created by git you will usually use `-p1` to apply them.

## Example

```
patch < my.patch
```
