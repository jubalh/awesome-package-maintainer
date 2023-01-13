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
# commit
osc ci
# submit to a devel project
osc sr utilities
```
