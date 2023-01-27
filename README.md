# HOWTO Awesome Package Maintainer!
*or How to Become a Packager*

Goals:
* short general introduction on what are packages and why we have them
* general collection of tools that packagers use
* general collection of tips, snippets and best practises useful for packagers of all distributions and systems
* distribution/system specific cheat sheets, HOWTOs, walk throughs and hints

This guide will (for now) focus on traditional packaging, not on containerized applications.

## TODO
Cover:
* debug symbols
* debuginfod

# Introduction

## Eating your own sushi
It helps tremendously if you, as a maintainer, are using the software yourself. If you stop using the software, it's best to let someone else that actively uses it to maintain it. This will make your contributions more reliable and more meaningful for yourself. More importantly, it will feel less of a job and be more of a rewarding experience -- you keep software you need in updated state and at same time, you are keeping the entire distribution updated and bug free.

## Software
*This will not be an accurate history lesson. We just want to give you some ideas.*

In the early days people were sitting in front of their Slackware machine and when they wanted to install a new software they had to do this by hand.
They got a tarball with the sources from a website, via email or mailing list, from an (FTP) server, from CVS and so on.
They inspected the sources and learned how to build the software. Some of this software even had Makefiles and were configured with Autotools.

As an example, let's do this now:

```
wget https://github.com/jubalh/nudoku/archive/refs/tags/2.1.0.tar.gz
tar xfv 2.1.0.tar.gz
cd nudoku-2.1.0
autoreconf -fi
./configure
make
```

And at that point you probably get an error because one of the dependencies of Nudoku is not installed. In this example that will be `ncurses`.

So now you stop the install process and go find the sources for ncurses and compile and install that first.

And if that `ncurses` has additional missing dependencies? You guessed it!

## What are packages?
Linux distributions provide packages to make this process much easier. Packages contains
 * the upstream source files
 * instructions how to build them
 * listing the dependencies and build dependencies
 * doing additional work like transforming configuration files, restart services etc.

If you are using a package manager and tell it to install such a package, it will install all the packages that are a dependency of this package as well.

Some dependencies are needed for building the software while others only for running it.
So you have build time and run time requirements. After you build the package, you will notice that there are parts that do different things. Some parts can be used for building other software while others are used at run-time. So being a clever maintainer, you create multiple binary packages - `ncurses` for run-time part and a `ncurses-devel` package for development headers.

Distributions have different naming regulations regarding such packages. Some distributions don't split packages up in development files (eg. Arch), some call them `-dev` (Debian) some call them `-devel` (openSUSE).

Some distributions and systems provide build recipes (Gentoo, pkgsrc, AUR) while others ship built binaries (Debian, Fedora, openSUSE) ready to be used.

Some distributions require the packager to build and upload the binaries on his own machine others use build systems.

To maintain integrity of packages, today these will be usually signed and signatures are verified prior to installation. For example the Open Build Service will check whether the tarball that is in a project actually is the same as the upstream tarball (if you use the URL in the spec file), it can then also check a hash (if provided) and also the author in case upstream signed the tarball with their GPG key.
It then builds the package for the specified architectures and will sign those packages with repository key.

By the way, the Open Build service can not only build rpms for openSUSE. It can also build packages for Fedora, RHEL, Arch, Debian, Ubuntu, AppImages..

It's recommend to read the [RPM Packaging Tutorial](http://www.mac-vicar.eu/tutorials/rpm-packaging/index.html) now. Even if you are not interested in becoming a packager for an RPM based distribution this will serve as a good introduction.

## Who is a package maintainer?
A packager or a package maintainer is the person who makes this magic happen.
Thanks to them you can easily install software using your package manager eg `zypper install libreoffice`.

# Tasks

## What does it entail to be a packager?

* Package software from scratch (creating build recipe, a deb rpm ebuild and so on).
* Follow rules and guidelines of your particular distribution when packaging new software.
* Track upstream activity or at least new releases.
* Be aware and/or get notified about new security issues of your packages
* React to bugreports and categorize them as valid, invalid, configuration mistake, user mistake, valid bug..
* Debug software and reproduce bugs.
* Patch your package and provide fixes. This may entail coordinating with upstream or forwarding the bug entirely to them.
* Forward your fixes to the upstream project so that everyone benefits from it, including you when new version is released.
* Coordinate with other maintainers. There can be several maintainers for one package.

## Staying up to date
In a perfect world, you, as package maintainers, are in close contact with upstream.
You monitor their development and take important bugfixes.

Here are some ways how you can monitor a project or get notified about new releases:
* Join the `-announce` mailing list of the project.
* *watch* the project on GitHub/GitLab (a button in the top right corner).
* Some distributions have their own tooling that reminds you about new releases. Debian uses a [watch file](https://wiki.debian.org/debian/watch).
* Subscribe to the projects RSS feed. If they use GitHub (and maybe GitLab etc as well) they automatically publish an RSS feed eg. `https://github.com/profanity-im/profanity/releases.atom`. I use the newsboat RSS reader and each morning check for new releases via `newsboat -u ~/watchedprojects.rss`.
* Use [nvchecker](https://github.com/lilydjwg/nvchecker).
* Monitor their websites via [urlwatch](https://thp.io/2008/urlwatch/).
* Use the website [fresh code](https://freshcode.club/).
* Use the website [newreleases](https://newreleases.io/).
* Use the website [release monitoring](https://release-monitoring.org/).

To get an overview of which distro ships which version of a particular software we like to use [repology](https://repology.org/). For example see this [overview](https://repology.org/project/profanity/versions).

[maintainer.opensuse.org](https://maintainer.opensuse.org/) also tracks packages from various sources.

## Updates
Try not be to be "hit and run" maintainer. Doing just a version bump is not enough when updating your package.

You should also:
* check that it builds
* write a changelog with changes interesting for users (depending on distribution guidelines)
* run the upstream testsuite, ideally as part of the build process
* test it yourself (best is if the maintainer themself is a regular user of the software)
* run your distribution tests (openSUSE, Debian and Fedora use [openQA](http://open.qa/) for automated tests)

If there are too many (known) issues with the new version you need to decide whether an update is actually in the interest of the users.

Rolling release distributions want the *latest working* version and not the latest no matter whether broken or not.

If the update is a point release with bug fixes only, you need to consider whether you have to update stable repositories as well.

## License
The software you package will come with a license. You need to be aware which licenses your distribution allows to ship in their repositories.
You can find a list of licenses and their identifier on the [SPDX website](https://spdx.org/licenses/).

## Reproducibility
Since most distributions ship binary packages and attackers could attack the build system, it is useful to have a way to verify these packages.
Various entities could follow the build instructions and verify whether the result is the same.
Packagers can help the [Reproducible builds](https://reproducible-builds.org/) project by ensuring their maintained software can be build reproducibly.

# Technical

## Coredump
A coredump is a file which contains the memory of a process. We can configure our system to create them when a program terminates unexpectedly, for example, when a segmentation fault occurs.
A segfault happens when a program tries to access memory that it should not access.
Read up on how to configure core dumps on your system. And check `ulimit`.

* [Linux core dump analysis](https://sergioprado.blog/linux-core-dump-analysis/) by [sergioprado](https://github.com/sergioprado).
* [How to get a core dump for a segfault on Linux](https://jvns.ca/blog/2018/04/28/debugging-a-segfault-on-linux/) by [jvns](https://github.com/jvns).
* [Debug a crash after it happened – without knowing about it upfront](https://dominique.leuenberger.net/blog/2014/04/debug-a-crash-after-it-happened-without-knowing-about-it-upfront/) by [DimStar77](https://github.com/DimStar77).
* [Per service ulimits](https://nordisch.org/posts/per-service-ulimits/) by [darix](https://github.com/darix).

# Tools

## diff
Diff is a command line tool for comparing two files or directories.

### Example

You have an upstream tarball and want to create a patch with the changes:

```
tar xfv upstream-version.tar.gz
cp -r upstream-version upstream-version.new
vi upstream-version.new/Makefile upstream-version.new/main.c
diff -urEbw upstream-version upstream-version.new > my.patch
```

## patch
Patch is a command line tool that applies a diff output file (patch) to an original file.

One important parameter is `-p` which strips leading path segments, basically ignoring parts of the path.

For patches created by git you will usually use `-p1` to apply them.

### Example

```
patch -p1 < my.patch
```

## quilt
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

### Create a new patch
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

### Edit an existing patch
After initializing of a source tree, use `quilt push X`, where `X` stands for the number of patches you want to apply. Use this command in order to get to the position of the patch you want to alter. Afterwards just edit the file corresponding to the patch and refresh this patch. If you edit a file that isn't contained in the patch, remember to add this file by quilt add.

A shortcut here to make sure that the file is tracked by quilt before editing it, you can use `quilt edit $(file)`.

## wiggle
[wiggle](https://github.com/neilbrown/wiggle/) applies rejected patches and performs word-wise diffs.

1. Use quilt to apply patch
2. If quilt failed, force it to apply as much as possible by the parameter `-f` (force). It applies what it is able to and saves the rest to `*.rej` (rejected) files
3. Use `wiggle --replace file file.rej` to try and apply the changes that quilt could not apply

### Example

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

## meld
[Meld](https://meldmerge.org/) is a GUI tool for comparing two files or directories.
It also let's you easily merge changes.

### Example
We checked out two packages from the [openSUSE OBS](https://build.opensuse.org/).
One is `X11:LXQt/lxqt-about` the other `X11:LXQt:git/lxqt-about`.
The difference is that one will be about the stable release of lxqt-about and the other will always be rebuild with the latest version of upstream git master.

We want to find out the differences and also merge/pull/push changes from the various files.

```
cd ~/obs
osc co X11:LXQt/lxqt-about
osc co X11:LXQt:git/lxqt-about
meld X11:LXQt X11:LXQt:git
```

![alt text](https://github.com/jubalh/awesome-package-maintainer/blob/master/img/meld-1.png?raw=true)

![alt text](https://github.com/jubalh/awesome-package-maintainer/blob/master/img/meld-2.png?raw=true)

## grep, ack, ag
These are tools to search in files. `grep` is the oldest of them.
`ack` is faster since it skips some directories like the `.git` and `.subversion` directories by default.
Useful for developers and maintainers. It can also search only specific file types.
For example `ack --cc close` searches all C files for the word `close`.
`ag` is yet a little faster than ack and ignores files mentioned in `.gitignore`. Additionally it can search compressed files.

HINT: When in a git repository, instead of using `grep`, try `git grep` to quickly search through the indexed files.

# Debugging
The following tools will mostly be about debugging.

* [What is That Process Doing?](http://price.mit.edu/tracing-w2014/#1) by Greg Price.
* [Linux debugging tools I love](https://jvns.ca/blog/2016/07/03/debugging-tools-i-love/) by [jvns](https://github.com/jvns).
* [Recovering files using /proc](https://jvns.ca/blog/2014/03/23/recovering-files-using-slash-proc-and-other-useful-facts/) by [jvns](https://github.com/jvns).
* [Some ways to get better at debugging](https://jvns.ca/blog/2022/08/30/a-way-to-categorize-debugging-skills/) by [jvns](https://github.com/jvns).
* [A debugging manifesto](https://jvns.ca/blog/2022/12/08/a-debugging-manifesto/) by [jvns](https://github.com/jvns).

## gdb
[gdb](https://www.gnu.org/software/gdb) is a debugger.

* [How does gdb work?](https://jvns.ca/blog/2016/08/10/how-does-gdb-work/) by [jvns](https://github.com/jvns).

### Example
Basic example that shows how to use gdb in general.

```
> cat main.c

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

## ltrace
[ltrace](http://www.ltrace.org/) is a debugging program which runs a specified command until the command exits.  While the command is executing, ltrace intercepts and records both the dynamic library calls called by the executed process and the signals received by the executed process. 

* [Using ltrace to debug a memory leak](https://jvns.ca/blog/2016/06/15/using-ltrace-to-debug-a-memory-leak/) by [jvns](https://github.com/jvns).

### Example
Running ltrace on the `plus` example program from above we see that it only calls `printf`:

```
ltrace ./plus
printf('R'Result: 5
)                                                                                                          = 10
+++ exited (status 0) +++
```

## strace
[strace](https://strace.io/) monitors and tampers with interactions between processes and the kernel. Useful for watching system calls and signals.

Try running strace on the `plus` example program and examine its output.

* [What problems do people solve with strace?](https://jvns.ca/blog/2021/04/03/what-problems-do-people-solve-with-strace/) by [jvns](https://github.com/jvns).
* [A workshop on strace & tcpdump](https://jvns.ca/blog/2016/05/06/a-workshop-on-strace-and-tcpdump/) by [jvns](https://github.com/jvns).
* [Debugging shared library problems with strace](https://jvns.ca/blog/2014/03/10/debugging-shared-library-problems-with-strace/) by [jvns](https://github.com/jvns).
* [Debug your programs like they're closed source!](https://jvns.ca/blog/2014/04/20/debug-your-programs-like-theyre-closed-source/) by [jvns](https://github.com/jvns).
* [Understanding how killall works using strace](https://jvns.ca/blog/2013/12/22/fun-with-strace/) by [jvns](https://github.com/jvns).
* [Spying on ssh with strace](https://jvns.ca/blog/2014/02/17/spying-on-ssh-with-strace/) by [jvns](https://github.com/jvns).

## ftrace
[ftrace](https://www.kernel.org/doc/html/latest/trace/index.html) is a tracing framework for the Linux kernel.

* [ftrace: trace your kernel functions!](https://jvns.ca/blog/2017/03/19/getting-started-with-ftrace/) by [jvns](https://github.com/jvns).

## lsof
[lsof](https://people.freebsd.org/~abe/) is a utility that lists open files.

* [lsof Tutorial](https://lsof.readthedocs.io/en/latest/tutorial/)

```
lsof -c vim
vim-nox11 17582 user  cwd    DIR   0,43       82 6826592 /home/user/awesome-package-maintainer
vim-nox11 17582 user    5u   REG   0,43    16384 7061198 /home/user/awesome-package-maintainer/.README.md.swp
```

## ldd
ldd lists the shared libraries required by a program.

```
ldd ./src/myprog
	libcurl.so.4 => /lib64/libcurl.so.4 (0x00007f36e4297000)
```

## readelf and objdump
They display information about object files and can be used to view an executable in assembly.

# Scripts and helpers
* [release-notes-scraper](https://github.com/dcermak/release-notes-scraper) - grab the release notes for projects from GitHub that do not keep a CHANGELOG, but publish their release notes via the releases page.
* [changesgen](https://github.com/dirkmueller/changesgen/blob/main/changesgen.py) automates writing a (openSUSE) changes file.
* [autoup](https://github.com/dirkmueller/changesgen/blob/main/autoup.py) searches on repology and tests whether updating to those would work in OBS

## The packaging process
Even if you are not interested in RPM, we would like to suggest to read the [RPM Packaging Tutorial](http://www.mac-vicar.eu/tutorials/rpm-packaging/index.html) since it gives a good overview and introduction.
For Debian packaging, [Debian Developer's Corner](https://www.debian.org/devel/) has all important details, pay especial attention to the [Debian Policy](https://www.debian.org/doc/debian-policy/)

Then see the following system specific chapters here according to your interest.
Generally we would recommend to skim also over the ones that are not your main focus.

### Buildroot
* [Contribute](https://buildroot.org/downloads/manual/manual.html#_contributing_to_buildroot) to Buildroot

#### Quickstart

Create and send a patch to the mailing list:
```
git clone https://git.busybox.net/buildroot
cd buildroot
vi package/libstrophe/libstrophe.{mk,hash}
git checkout -b libstrophe-update-xxx
git add package/libstrophe/libstrophe.{mk,hash}
git commit #write a nice message. use git log to see message of others
# create a patch
git format-patch -M -n -s -o outgoing origin/master
# send the patch to the ml
git send-email --smtp-debug --smtp-ssl-cert-path "" --to="buildroot@busybox.net" outgoing/0001-mypatchname.patch
```

### Debian
* [Debian Developer's Corner](https://www.debian.org/devel/)
* [Debian Policy](https://www.debian.org/doc/debian-policy/)
* [Debian New Maintainers' Guide](https://www.debian.org/doc/manuals/maint-guide/)
* [Packaging on the Debian wiki](https://wiki.debian.org/Packaging)
* [Debian Developer's Reference](https://www.debian.org/doc/manuals/maint-guide/index.en.html)

### Fedora
* [Fedora Packaging Guidelines](https://docs.fedoraproject.org/en-US/packaging-guidelines/)

### openSUSE
Start by creating an account on:
* the issue tracker: https://bugzilla.opensuse.org/
* the openSUSE Open Build Service (OBS): https://build.opensuse.org/

Then, in order:
* [Build Service Tutorial](https://en.opensuse.org/openSUSE:Build_Service_Tutorial) - follow along style introduction
* [openSUSE Packaging Guidelines](https://en.opensuse.org/openSUSE:Packaging_guidelines)
* [Creating a ChangeLog](https://en.opensuse.org/openSUSE:Creating_a_changes_file_(RPM))
* [Packaging documentation on the openSUSE wiki](https://en.opensuse.org/Category:Packaging_documentation) - bookmark this
* [Build Service Tips and Tricks](https://en.opensuse.org/openSUSE:Build_Service_Tips_and_Tricks) - learn useful tricks
* [How to contribute to Factory](https://en.opensuse.org/openSUSE:How_to_contribute_to_Factory) - learn the workflow and some more tricks
* [RPM Packaging Guide](https://rpm-packaging-guide.github.io/) - about the basics of RPM packaging
* Read the RPM section of this readme as well.

[maintainers.opensuse.org](https://maintainer.opensuse.org/) is a helpful tool listing all the maintainers of a package, comparing the versions in differnt distributions, pointing to the devel project etc.

#### Quickstart
Install
* [grc](https://github.com/garabik/grc) (`zypper in grc`) to have colorized and more readable output when building with `grc osc build`.
* [spec-cleaner](https://github.com/rpm-software-management/spec-cleaner) (`zypper in spec-cleaner`) to clean the spec file according to the style guide.

##### Configure osc
Configure osc `$EDITOR $HOME/.config/osc/oscrc`.

```
[https://api.opensuse.org]
user = <youruser>
pass = <yourpassword>
email = <youremail>
```

Add some useful tools which will be available in the chroot (`osc chroot`):
```
extra-pkgs = vim gdb strace less unzip
```

Some useful aliases for your bashrc/zshrc etc. If you use `grc` you could add this as well.
```
alias oscb="osc build --ccache --cpio-bulk-download --download-api-only"
alias oscsd="osc service localrun download_files"
```

So you don't have to provide a password each time you build a package you should add yourself to the sudoers file:

```
# general file /etc/sudoers
sudo visudo
# or specific file which will be included in /etc/sudoers via `@includedir /etc/sudoers.d`
sudo visudo /etc/sudoers.d/username
```

With following content:

```
yourusername ALL = NOPASSWD: /usr/bin/build
yourusername ALL = NOPASSWD: /usr/bin/osc
```

##### Create a new package
```
# create the package in your OBS home
osc meta pkg -e home:myusername awesomenewpackage
# check it out locally
osc co home:myusername awesomenewpackage
# write the spec file
vi awesomenewpackage.spec
# download the sources
oscsd
# write the changelog
osc vc
# commit it to your home
osc ci
# submit to a devel project
osc sr utilities
```

##### Making changes to an existing package

```
# branch the package. getpac will branch from the actual devel project automatically
# getpac is a combination of `osc branch` and `osc checkout`
osc getpac openSUSE:Factory profanity
# change dir. the corresponding dir will be printing out for you in the last step
cd home:jubalh:branches:network:messaging:xmpp/profanity
# make changes
vi *.spec
# build it
oscb
# describe your change so that others can understand it
osc vc
# commit it to your branched version
osc ci
# submit it to the devel repo
osc sr
```

##### Install all the build requirements of a package

In case you want to develop on the program you might want a quick way to get all the build requirements:

```
zypper si -d profanity
```

##### Find out which packages depend on a certain package
Let's say you maintain the libstrophe library and want to find out which packages depend on in (in Factory on x86_64):

```
osc whatdependson openSUSE:Factory libstrophe standard x86_64
```

If you use this often you could add a function to your .bashrc:

```
whatdependson () {
    osc whatdependson openSUSE:Factory $1 standard x86_64
}
```

Example output:
```
whatdependson libstrophe
libstrophe :
   profanity
   xmppc
```

#### Blog(posts)
* [RPM packaging for beginners](https://dominique.leuenberger.net/blog/2009/05/rpm-packaging-for-beginners/) by [DimStar77](https://github.com/DimStar77).
* [Per service ulimits](https://nordisch.org/posts/per-service-ulimits/) by [darix](https://github.com/darix).
* [Howto patch a rubygem based package?](https://nordisch.org/posts/howto-patch-a-rubygem-based-package/) by [darix](https://github.com/darix).
* [Quilt Quick How-to](https://nordisch.org/posts/quilt-quick-how-to/) by [darix](https://github.com/darix).
* [OSC interactive review](https://nordisch.org/posts/osc-interactive-review/) by [darix](https://github.com/darix).
* [Hooking up instantiated services with RPM](https://nordisch.org/posts/hooking-up-instantiated-services-with-rpm/) by [darix](https://github.com/darix).
* [OSC helpers for lazy packagers](https://nordisch.org/posts/osc-helpers-for-lazy-packagers/) by [darix](https://github.com/darix).
* [Debug a crash after it happened – without knowing about it upfront](https://dominique.leuenberger.net/blog/2014/04/debug-a-crash-after-it-happened-without-knowing-about-it-upfront/) by [DimStar77](https://github.com/DimStar77).
* [How to find, debug and fix reproducibility issues in openSUSE OBS package build results](https://github.com/bmwiedemann/reproducibleopensuse/blob/master/howtodebug) by [bmwiedemann](https://github.com/bmwiedemann).

### RPM
* [RPM Packaging Guide](https://rpm-packaging-guide.github.io/).
* [RPM Packaging Tutorial](http://www.mac-vicar.eu/tutorials/rpm-packaging/index.html) by [dmacvicar](https://github.com/dmacvicar).
* [SUSE documentation on rpm and rpmbuild](https://documentation.suse.com/sles/15-SP1/html/SLES-all/cha-sw-cl.html#sec-rpm).

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

# Credit
Thanks to everybody who contributed to this guide and to the authors of all the linked Websites, blogs, etc.

Special thanks and credit to the whole SUSE Pack team, and to:
* Tomáš Chvátal
* Dominique Leuenberger
* Marcus Rueckert
* Andreas Sturmlechner
