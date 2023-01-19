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

# Introduction

## Software
*This will not be an accurate history lesson. We just want to give readers an idea*

In the early days people were sitting infront of their Slackware machine and when they wanted to install a new software they had to do this by hand.
They got a tarball with the sources from a website, via mail(ing list), from an (FTP) server, from CVS and so on.
They inspected the sources and learned how to build the software.
A popular way are/were Makefiles.

Let's do this now:
```
wget https://github.com/jubalh/nudoku/archive/refs/tags/2.1.0.tar.gz
tar xfv 2.1.0.tar.gz
cd nudoku-2.1.0
autoreconf -fi
./configure
make
```

And at that point they got an error. Because one of the dependencies of that software was not installed. In this example that will be `ncurses`.

So they had to stop the process. Go to find the sources for ncurses and compile and install that first.

If that dependency has dependencies? You guessed it..

## What are packages
Linux distributions provide packages to make this process much easier. It contains the upstream source files and instructions how to build them. Listing the dependencies. Doing additional work like transforming configuration files, restart services etc.

If we are using a package manager and tell it to install such a package it will install all the packages that are a dependency of this package as well.

Some dependencies are needed for building the software others only for running it.
So we have build time and run time requirements. Since we don't want huge packages we can split them. We can have an `ncurses` package and a `ncurses-devel` package.

Distributions have different naming regulations regarding such packages. Some distributions don't split packages up in development files (Arch), some call them `-dev` (Debian) some call them `-devel` (openSUSE).

We recommend to read the [RPM Packaging Tutorial](http://www.mac-vicar.eu/tutorials/rpm-packaging/index.html) now. Even when you are not interested in becoming a packager for an RPM based distribution this will serve as a good introdcution.

## What is a package maintainer
A packager/packgage maintainer is the person who makes this magic happen.
Thanks to them you can easily install software using your package manager eg `zypper install libreoffice`.

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

## Technical

### Coredump
A coredump is a file which contains the memory of a process. We can configure our system to create them when a program terminates unexpectedly. Like when a segmentation fault occurs.
A seg fault happens when a program tries to access memory that it should not access.
Read up on how to configure core dumps on your system. And check `ulimit`.

#### Blog(posts)
* [Linux core dump analysis](https://sergioprado.blog/linux-core-dump-analysis/) by [sergioprado](https://github.com/sergioprado).
* [How to get a core dump for a segfault on Linux](https://jvns.ca/blog/2018/04/28/debugging-a-segfault-on-linux/) by [jvns](https://github.com/jvns).
* [Debug a crash after it happened – without knowing about it upfront](https://dominique.leuenberger.net/blog/2014/04/debug-a-crash-after-it-happened-without-knowing-about-it-upfront/) by [DimStar77](https://github.com/DimStar77).
* [Per service ulimits](https://nordisch.org/posts/per-service-ulimits/) by [darix](https://github.com/darix).

## The packaging process
Even if you are not interested in RPM we would like to suggest to read the [RPM Packaging Tutorial](http://www.mac-vicar.eu/tutorials/rpm-packaging/index.html) since it gives a good overview and introduction.

### Buildroot
* [Contribute](https://buildroot.org/downloads/manual/manual.html#_contributing_to_buildroot) to Buildroot
* Our [buildroot cheat sheet](cheatsheet/buildroot.md).

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

Our [openSUSE cheat sheet](cheatsheet/openSUSE.md) contains useful tips, tricks and tools.

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
* [RPM Packaging Guide](https://rpm-packaging-guide.github.io/).
* [RPM Packaging Tutorial](http://www.mac-vicar.eu/tutorials/rpm-packaging/index.html) by [dmacvicar](https://github.com/dmacvicar).

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

### quilt
[quilt](http://savannah.nongnu.org/projects/quilt) is a tool for managing many patches.

`quilt series` shows a list of all patches.
`quilt push/pop` navigate the patch stack up and down. Before a patch is applied, all affected files are backed up to the .pc/patch directory.

Sometimes there is a patch that can't be applied because of the file difference. In these situations you can use `wiggle` or try to adjust patch manually:

1. Use quilt to apply patch
2. If quilt failed, force it to apply as much as possible by the parameter -f (force)
3. Quilt applies what it is able to apply and saves the rest to file.rej files
4. Check the file.rej and do desired changes in the specified files manually
5. refresh the patch

We will use an example based on an openSUSE package. The quilt part is universal though.

#### Create a new patch
Check out the package and call `quilt setup` which will create a new folder with the source code:
```
osc bco graphics/tiff
cd graphics tiff
quilt setup tiff.spec
```

There might already be patches mentioned in the spec file so lets apply them and then start a new patch:
```
quilt push -a
quilt new tiff-security-fix-1.patch
```

Edit the files that need fixing and create a patch:
```
quilt edit libtiff/tif_dirwrite.c
quilt header -e  #at the top of the header we want some explanation/references etc
quilt refresh
```

Instead of `quilt edit file` you could also use `quilt add file` and `$EDITOR file`.

Now we have a new file `tiff-security-fix-1.patch` which we can apply in the spec file.

#### Edit an existing patch
After initializing of a source tree use quilt push X, where X stands for the number of patches you want to apply. Use this command in order to get to the position of the patch you want to alter. Afterwards just edit the file corresponding to the patch and refresh this patch. If you edit a file that isn't contained in the patch, remember to add this file by quilt add.

### wiggle
[wiggle](https://github.com/neilbrown/wiggle/) applies rejected patches and performs word-wise diffs.

1. Use quilt to apply patch
2. If quilt failed, force it to apply as much as possible by the parameter `-f` (force). It applies what it is able to and saves the rest to `*.rej` (rejected) files
3. Use `wiggle --replace file file.rej` to try and apply the changes that quilt could not apply

#### Example

We have the following patch:
```
--- telnet-bsd-1.2.orig/telnet/commands.c
+++ telnet-bsd-1.2/telnet/commands.c
@@ -2450,17 +2450,21 @@ tn (int argc, char *argv[])
    error = getaddrinfo (aliasp, "0", &ahints, &ares);
    if (error)
    {
+     printf ("Couldn't get address for %s\n", aliasp);
       warn ("%s: %s", aliasp, gai_strerror (error));
       close (net);
-      freeaddrinfo (ares);
+     net = -1;
       continue;
}
```

Since the patch was created the `telnet/commands.c` file has already been changed.
```
warn ("%s: %s", aliasp, gai_strerror (error));
printf("This is a test");
int a = 5;
freeaddrinfo (ares);
printf("This is another test");
break;
```

Try quilt:
```
quilt push
Applying patch patches/telnet-bsd-1.2-hostalias.patch
patching file telnet/commands.c
Hunk #1 FAILED at 2450.
1 out of 1 hunk FAILED -- rejects in file telnet/commands.c
patching file telnet/telnet.1
Patch patches/telnet-bsd-1.2-hostalias.patch does not apply (enforce with -f)
```

Use `-f` option:
```
$ quilt push -f
Applying patch patches/telnet-bsd-1.2-hostalias.patch
patching file telnet/commands.c
Hunk #1 FAILED at 2450.
1 out of 1 hunk FAILED -- saving rejects to file telnet/commands.c.rej
patching file telnet/telnet.1
Applied patch patches/telnet-bsd-1.2-hostalias.patch (forced; needs refresh)
```

The telnet/commands.c.rej file was created. Now the Wiggle can be used:
```
$ wiggle --replace commands.c commands.c.rej
```

Inspect the `telnet/commands.c` file to see that the changes from the patch were successfully applied:
```
printf ("Couldn't get address for %s\n", aliasp);
warn ("%s: %s", aliasp, gai_strerror (error));
printf("This is a test");
int a = 5;
net = -1;
printf("This is another test");
break
;
```

### meld
[Meld](https://meldmerge.org/) is a GUI tool for comparing two files or directories.
It also let's you easily merge changes.

### grep, ack, ag
These are tools to search in files. `grep` is the oldest of them.
`ack` is faster since it skips some directories like the `.git` and `.subversion` directories by default.
Useful for developers and maintainers. It can also search only specific file types.
For example `ack --cc close` searches all C files for the word `close`.
`ag` is yet a little faster than ack and ignores files mentioned in `.gitignore`. Additionally it can search compressed files.

### Debugging
The following tools will mostly be about debugging.

* [What is That Process Doing?](http://price.mit.edu/tracing-w2014/#1) by Greg Price.
* [Linux debugging tools I love](https://jvns.ca/blog/2016/07/03/debugging-tools-i-love/) by [jvns](https://github.com/jvns).
* [Recovering files using /proc](https://jvns.ca/blog/2014/03/23/recovering-files-using-slash-proc-and-other-useful-facts/) by [jvns](https://github.com/jvns).
* [Some ways to get better at debugging](https://jvns.ca/blog/2022/08/30/a-way-to-categorize-debugging-skills/) by [jvns](https://github.com/jvns).
* [A debugging manifesto](https://jvns.ca/blog/2022/12/08/a-debugging-manifesto/) by [jvns](https://github.com/jvns).

### gdb
[gdb](https://www.gnu.org/software/gdb) is a debugger.

* [How does gdb work?](https://jvns.ca/blog/2016/08/10/how-does-gdb-work/) by [jvns](https://github.com/jvns).

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

* [Using ltrace to debug a memory leak](https://jvns.ca/blog/2016/06/15/using-ltrace-to-debug-a-memory-leak/) by [jvns](https://github.com/jvns).

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

* [What problems do people solve with strace?](https://jvns.ca/blog/2021/04/03/what-problems-do-people-solve-with-strace/) by [jvns](https://github.com/jvns).
* [A workshop on strace & tcpdump](https://jvns.ca/blog/2016/05/06/a-workshop-on-strace-and-tcpdump/) by [jvns](https://github.com/jvns).
* [Debugging shared library problems with strace](https://jvns.ca/blog/2014/03/10/debugging-shared-library-problems-with-strace/) by [jvns](https://github.com/jvns).
* [Debug your programs like they're closed source!](https://jvns.ca/blog/2014/04/20/debug-your-programs-like-theyre-closed-source/) by [jvns](https://github.com/jvns).
* [Understanding how killall works using strace](https://jvns.ca/blog/2013/12/22/fun-with-strace/) by [jvns](https://github.com/jvns).
* [Spying on ssh with strace](https://jvns.ca/blog/2014/02/17/spying-on-ssh-with-strace/) by [jvns](https://github.com/jvns).

### ftrace
[ftrace](https://www.kernel.org/doc/html/latest/trace/index.html) is a tracing framework for the Linux kernel.

* [ftrace: trace your kernel functions!](https://jvns.ca/blog/2017/03/19/getting-started-with-ftrace/) by [jvns](https://github.com/jvns).

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

## Credit
Thanks to everybody who contributed to this guide and to the authors of all the linked Websites, blogs, etc.

Special thanks and credit to the SUSE Pack team.
