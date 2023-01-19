# buildroot

Create and send a patch to the mailing list:
```
git clone https://git.busybox.net/buildroot
cd buildroot
vi package/libstrophe/libstrophe.{mk,hash}
git checkout -b libstrophe-update-xxx
git add package/libstrophe/libstrophe.{mk,hash}
git commit #write a nice message. use git log to see message of others
# create a patch
git format-patch -M -n -s -o outgoing origin/maste
# send the patch to the ml
git send-email --smtp-debug --smtp-ssl-cert-path "" --to="buildroot@busybox.net" outgoing/0001-mypatchname.patch
```
