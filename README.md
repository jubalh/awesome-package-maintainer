# Awesome Package Maintainer
*or How To Become A Packager*

***WIP*** I'm not yet sure how to organize this.
Let's collect all information now. We can still reorganize and restructure later.

This should become a list with awesome tools, tricks, tips, howtos for (Linux) package maintainers.
This guide will (for now) focus on traditional packaging, not so much about containerized applications.

## TODO
Cover:
* debug symbols
* debuginfod
* coredumb

Add examples of:
* quilt
* wiggle

# What does it entail to be a packager?

* Packaging software from scratch (creating build recipe, a deb rpm ebuild and so on).
* Following the rules, guidelines and regulations of your particular distribution when packaging new software.
* Tracking upstream activity or at least releases.
* Being aware and getting notified about new security issues of your packages
* React on user bugreports. Categorize them: valid, invalid, configuration mistake, user mistake, valid bug..
* Debug software and reproduce bugs.
* Patch your package and provide fixes. Might entail coordinating with upstream or forwarding the bug entirely to them.
* Send your downstream fixes to the upstream project so that everyone benefits from it.
* Coordinate with other maintainers (several maintainers for one project, dependency/library maintainers, other experts).

# Staying up to date
In a perfect world package maintainers would be in close contact with upstream.
Monitor their development and take important bugfixes.

Here are some ways how you can monitor a project or get notified about new releases:
* Join the `-announce` mailing list of the project.
* *watch* the project on GitHub/GitLab (a button in the top right corner).
* Some distributions have their own tooling that reminds you about new releases. Debian uses a [watch file](https://wiki.debian.org/debian/watch).
* Subscribe to the projects RSS feed. If they use GitHub (and maybe Gitlab etc as well) they automatically publish an RSS feed eg. `https://github.com/profanity-im/profanity/releases.atom`. I use the newsboat RSS reader and each morning check for new releases via `newsboat -u ~/watchedprojects.rss`.
* Use [nvchecker](https://github.com/lilydjwg/nvchecker).
* Monitor their websites via [urlwatch](https://thp.io/2008/urlwatch/).
* Use the website [fresh code](https://freshcode.club/).
* Use the website [newreleases](https://newreleases.io/).
* Use the website [release monitoring](https://release-monitoring.org/).

To get an overview of which distro ships which version of a particular software we like to use [repology](https://repology.org/). For example see this [overview](https://repology.org/project/profanity/versions).

## License
The software you package will come with a license. You need to be aware which licenses your distribution allows to ship in their repositories.
You can find a list of licenses and their identifier on the [SPDX website](https://spdx.org/licenses/).

## Distribution and system specific guides

### Debian
* [Debian New Maintainers' Guide](https://www.debian.org/doc/manuals/maint-guide/)
* [Packaging on the Debian wiki](https://wiki.debian.org/Packaging)
* [Debian Developer's Reference](https://www.debian.org/doc/manuals/maint-guide/index.en.html)

### Fedora
* [Fedora Packaging Guidelines](https://docs.fedoraproject.org/en-US/packaging-guidelines/)

### openSUSE
Start by creating an account on:
* the issue tracker: https://bugzilla.opensuse.org/
* the openSUSE Open Build Service (OBS): https://build.opensuse.org/

Then:
* Use the [Build Service Tutorial](https://en.opensuse.org/openSUSE:Build_Service_Tutorial) as an introduction.
* Read the [openSUSE Packaging Guidelines](https://en.opensuse.org/openSUSE:Packaging_guidelines).
* Learn how to write a [ChangeLog](https://en.opensuse.org/openSUSE:Creating_a_changes_file_(RPM)).
* Bookmark and skim through [Packaging documentation on the openSUSE wiki](https://en.opensuse.org/Category:Packaging_documentation) 
* Read about [Build Service Tips and Tricks](https://en.opensuse.org/openSUSE:Build_Service_Tips_and_Tricks)
* Learn [how to contribute to Factory](https://en.opensuse.org/openSUSE:How_to_contribute_to_Factory). It also contains a lot of useful snippets.
* Read the general [RPM Packaging Guide](https://rpm-packaging-guide.github.io/).
* Read the RPM section of this readme as well.

Check our [openSUSE cheat sheet](cheatsheet/openSUSE.md) for tips, tricks and tools.

#### Blog(posts)
* [RPM packaging for beginners](https://dominique.leuenberger.net/blog/2009/05/rpm-packaging-for-beginners/) by [DimStar77](https://github.com/DimStar77).
* [Per service ulimits](https://nordisch.org/posts/per-service-ulimits/) by [darix](https://github.com/darix).
* [Howto patch a rubygem based package?](https://nordisch.org/posts/howto-patch-a-rubygem-based-package/) by [darix](https://github.com/darix).
* [Quilt Quick How-to](https://nordisch.org/posts/quilt-quick-how-to/) by [darix](https://github.com/darix).
* [OSC interactive review](https://nordisch.org/posts/osc-interactive-review/) by [darix](https://github.com/darix).
* [Hooking up instantiated services with RPM](https://nordisch.org/posts/hooking-up-instantiated-services-with-rpm/) by [darix](https://github.com/darix).
* [OSC helpers for lazy packagers](https://nordisch.org/posts/osc-helpers-for-lazy-packagers/) by [darix](https://github.com/darix).
* [Debug a crash after it happened – without knowing about it upfront](https://dominique.leuenberger.net/blog/2014/04/debug-a-crash-after-it-happened-without-knowing-about-it-upfront/) by [DimStar77](https://github.com/DimStar77).

### RPM
* [RPM Packaging Guide](https://rpm-packaging-guide.github.io/)

#### Snippets and tricks

##### List files in a package

```
rpm -ql vim
```

##### Find out which package owns a particular file

```
rpm -qf /usr/bin/vim
```

##### Get expanded spec file from src.rpm
Since rpm 4.18 you can find the parsed and expanded spec file in the src.rpm headers.
This can be useful to know which values are actually used, what the macros were expanded to.
Generally what the actual build recipe looked like.

```
rpm -q --queryformat="%{Spec}" /path/to/src.rpm
```

## Tools

### diff
Diff is a command line tool for comparing two files or directories.

##### Example

We have an upstream tarball and want to create a patch with some changes:

```
tar xfv upstream-version.tar.gz
cp -r upstream-version upstream-version.new
vi upstream-version.new/Makefile upstream-version.new/main.c
diff -urEbw upstream-version upstream-version.new > my.patch
```

### patch
Patch is a command line tool that applies a diff output file (patch) to an original file.

One important parameter is `-p` which strips leading slashes, basically ignoring parts of the path.

For patches created by git you will usually use `-p1` to apply them.

#### Example

```
patch < my.patch
```

### wiggle
[wiggle](https://github.com/neilbrown/wiggle/) applies rejected patches and performs word-wise diffs.

### quilt
[quilt](http://savannah.nongnu.org/projects/quilt) is a tool for managing many patches.

### meld
[Meld](https://meldmerge.org/) is a GUI tool for comparing two files or directories.
It also let's you easily merge changes.

### grep, ack, ag
These are tools to search in files. `grep` is the oldest of them.
`ack` is faster since it skips some directories like the `.git` and `.subversion` directories by default.
Useful for developers and maintainers. It can also search only specific file types.
For example `ack --cc close` searches all C files for the word `close`.
`ag` is yet a little faster than ack and ignores files mentioned in `.gitignore`. Additionally it can search compressed files.

### gdb
[gdb](https://www.gnu.org/software/gdb) is a debugger.

#### Example
Basic example that shows how to use gdb in general.

```
cat main.c
#include <stdio.h>
#include <stdlib.h>

int plus(int a, int b)
{
    return a+b;
}

int main()
{
    int n1 = 2;
    int n2 = 3;
    int r = plus(n1, n2);
    printf("Result: %d\n", r);
    return EXIT_SUCCESS;
}
```

Compile it with debug symbols:
```
gcc main.c -g -o plus
```

Debug:
```
(gdb) break plus
Breakpoint 1 at 0x401140: file main.c, line 6.
(gdb) run
Starting program: /home/user/src/plus
Breakpoint 1, plus (a=2, b=3) at main.c:6
6	    return a+b;
(gdb) print a
$1 = 2
(gdb) p b
$2 = 3
(gdb) next
7	}
(gdb) n
main () at main.c:14
14	    printf("Result: %d\n", r);
(gdb) p r
$3 = 5
```

With `break` we set the breakpoint to the function `plus`.
Then we start the program. It breaks at our first breakpoint.
Here we investigate the values of the variables `a` and `b`.
And then we continue to step through the program. At the end we look at the result.

### ltrace
[ltrace](http://www.ltrace.org/) is a debugging program which runs a specified command until the command exits.  While the command is executing, ltrace intercepts and records both the dynamic library calls called by the executed process and the signals received by the executed process. 

#### Example
Running ltrace on the `plus` example program from above we see that it only calls `printf`:

```
ltrace ./plus
printf('R'Result: 5
)                                                                                                          = 10
+++ exited (status 0) +++
```

### strace
[strace](https://strace.io/) monitors and tampers with interactions between processes and the kernel. Useful for watching system calls and signals.

Try running strace on the `plus` example program and examine its output.

### lsof
[lsof](https://people.freebsd.org/~abe/) is a utility that lists open files.

```
lsof -c vim
vim-nox11 17582 user  cwd    DIR   0,43       82 6826592 /home/user/awesome-package-maintainer
vim-nox11 17582 user    5u   REG   0,43    16384 7061198 /home/user/awesome-package-maintainer/.README.md.swp
```

### ldd
ldd lists the shared libraries required by a program.

```
ldd ./src/myprog
	libcurl.so.4 => /lib64/libcurl.so.4 (0x00007f36e4297000)
```

### readelf and objdump
They display information about object files and can be used to view an executable in assembly.

## Scripts and helpers
* [release-notes-scraper](https://github.com/dcermak/release-notes-scraper) - grab the release notes for projects from github that do not keep a CHANGELOG, but publish their release notes via the releases page.
