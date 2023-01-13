# openSUSE Cheat Sheet

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

Some useful aliases for your bashrc/zshrc etc:
```
alias oscb="osc build --ccache --cpio-bulk-download --download-api-only"
alias oscsd="osc service localrun download_files"
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
