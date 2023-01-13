# openSUSE

## Preparations
Create an account on:
* the issue tracker: https://bugzilla.opensuse.org/
* the openSUSE Open Build Service (OBS): https://build.opensuse.org/

Install `osc` on your machine: `zypper in osc`.

### Creating a new package

The following command creates a new package in your OBS home.
```
osc meta pkg -e home:myusername awesomenewpackage
```

Check it out locally:
```
osc co home:myusername awesomenewpackage
```

Create the spec file:
```
vi awesomenewpackage.spec
```

The editor should already open with a fresh spec file template which you need to fill out.
You can also go on OBS and look at the spec files of existing packages.

The spec file is the "recipe" for your package.

Once this is done you also need to create a changes file:
```
osc vc
```

