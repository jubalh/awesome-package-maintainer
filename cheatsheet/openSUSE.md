# openSUSE Cheat Sheet

Install
* [grc](https://github.com/garabik/grc) (`zypper in grc`) to have more readable output when building with `grc osc build`.
* [spec-cleaner](https://github.com/rpm-software-management/spec-cleaner) (`zypper in spec-cleaner`) to clean the spec file according to the style guide.

## osc configuration
Configure osc `$EDITOR $HOME/.config/osc/oscrc`.

```
[https://api.opensuse.org]
user = <youruser>
pass = <youremail>
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

## Workflow example

### Create a new package
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

### Making changes to an existing project

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

### Install all the build requirements of a package

In case you want to develop on the program you might want a quick way to get all the build requirements:

```
zypper si -d profanity
```

### Find out which packages depend on a certain package
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

