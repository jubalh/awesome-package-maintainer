# Awesome Package Maintainer
*or How To Become A Packager*

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

## Distribution and system specific guides

### Debian
* [Debian New Maintainers' Guide](https://www.debian.org/doc/manuals/maint-guide/)
* [Packaging on the Debian wiki](https://wiki.debian.org/Packaging)

### Fedora
* [Fedora Packaging Guidelines](https://docs.fedoraproject.org/en-US/packaging-guidelines/)

### openSUSE
Start by creating an account on:
* the issue tracker: https://bugzilla.opensuse.org/
* the openSUSE Open Build Service (OBS): https://build.opensuse.org/

Then follow along the [Build Service Tutorial](https://en.opensuse.org/openSUSE:Build_Service_Tutorial). Read the [openSUSE Packaging Guidelines](https://en.opensuse.org/openSUSE:Packaging_guidelines).
And bookmark the [Packaging documentation on the openSUSE wiki](https://en.opensuse.org/Category:Packaging_documentation) which you will need occasionally to look things up.
I also recommend to read the general [RPM Packaging Guide](https://rpm-packaging-guide.github.io/).
Read the RPM section of this readme as well.

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
* [quilt](http://savannah.nongnu.org/projects/quilt)
* [wiggle](http://neil.brown.name/wiggle/)
* diff
* patch
* meld
* git
