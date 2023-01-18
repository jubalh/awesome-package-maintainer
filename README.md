# Awesome Package Maintainer
*or How To Become A Packager*

***WIP*** I'm not yet sure how to organize this.
Let's collect all information now. We can still reorganize and restructure later.

This should become a list with awesome tools, tricks, tips, howtos for (Linux) package maintainers.
This guide will (for now) focus on traditional packaging, not so much about containerized applications.

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

For this purpose you should subscribe to your projects mailing list or *watch* (a button on the top right corner) them on GitHub.
You can specify to either watch them for all their activity or at least get notified about new releases. Project with mailing lists usually have a `-announce` list so you don't get overwhelmed.

Some distributions have automated ways to notify maintainers when upstream publishes a new release. For example Debian uses a [watch file](https://wiki.debian.org/debian/watch) for that.

Some projects have RSS feeds on their website. If they use GitHub (and maybe Gitlab etc as well) they automatically publish an RSS feed eg. `https://github.com/profanity-im/profanity/releases.atom`.
I use the newsboat RSS reader and each morning check for new releases via `newsboat -u ~/watchedprojects.rss`.

Some people follow [fresh code](https://freshcode.club/).

Another tool you can use is [nvchecker](https://github.com/lilydjwg/nvchecker).

To get an overview of which distro ships which version of a particular software we like to use [repology](https://repology.org/). For example see this [overview](https://repology.org/project/profanity/versions).

There is also [release monitoring](https://release-monitoring.org/) by RedHat.

You can also watch websites via [urlwatch](https://thp.io/2008/urlwatch/).

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

Then follow along the [Build Service Tutorial](https://en.opensuse.org/openSUSE:Build_Service_Tutorial). Read the [openSUSE Packaging Guidelines](https://en.opensuse.org/openSUSE:Packaging_guidelines). And learn how to write a [ChangeLog](https://en.opensuse.org/openSUSE:Creating_a_changes_file_(RPM)).
And bookmark the [Packaging documentation on the openSUSE wiki](https://en.opensuse.org/Category:Packaging_documentation) which you will need occasionally to look things up. There is also an entry in the openSUSE wiki about [Build Service Tips and Tricks](https://en.opensuse.org/openSUSE:Build_Service_Tips_and_Tricks). [How to contribute to Factory](https://en.opensuse.org/openSUSE:How_to_contribute_to_Factory) has a lot of useful snippets.
I also recommend to read the general [RPM Packaging Guide](https://rpm-packaging-guide.github.io/).
Read the RPM section of this readme as well.

Our [openSUSE cheat sheet](cheatsheet/openSUSE.md).

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

### ltrace
[ltrace](http://www.ltrace.org/) is a debugging program which runs a specified command until the command exits.  While the command is executing, ltrace intercepts and records both the dynamic library calls called by the executed process and the signals received by the executed process. 

### strace
[strace](https://strace.io/) monitors and tampers with interactions between processes and the kernel. Useful for watching system calls and signals.

### gdb
[gdb](https://www.gnu.org/software/gdb) is a debugger.

```
gdb myprog
run
backtrace
```

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

