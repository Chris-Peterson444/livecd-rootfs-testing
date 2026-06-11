
livefs build with a ppa
-----------------------

(adapted from instructions written by mwhudson)

1. be a member of [launchpad-livefs-builders](https://launchpad.net/~launchpad-livefs-builders)
2. create a livefs in Launchpad (once-per-series prep)
   See the script `series-setup`.
3. upload livecd-rootfs with your changes to a ppa
4. wait for it to publish
5. build the livefs
   See the script `start-livefs-build`.  Example invocation:
```
./start-livefs-build
  --arch=amd64
  --livefs=~dbungert/+livefs/ubuntu/noble/test  # created with series-setup
  --ppa=~dbungert/ubuntu/livecd-rootfs          # ppa with livecd-rootfs to test
  --metadata 'debug="True"'                     # set -x
  --metadata 'project="ubuntu-server"'
  --metadata 'subproject="live"'
```
