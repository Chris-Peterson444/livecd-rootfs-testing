
Methods for testing livecd-rootfs changes
-----------------------------------------

The purpose of this repo is to host scripts and tricks for testing
livecd-rootfs changes ahead of those changes landing in a release pocket
somewhere.

In some ways the simplest answer is
[ubuntu-old-fashioned](https://github.com/ubuntu-bartenders/ubuntu-old-fashioned),
which allows for construction of a build environment suitable for running
locally and produces similar results to the launchpad builders.

It's also possible to build a livefs in launchpad using some lp scripting.  See
[livefs-build-with-a-ppa](livefs-build-with-a-ppa.md) for details on that
process.

The output will either be an ISO file on newer series, or a bunch of squashfs
files and related on Noble and earlier.  If it's a bunch of files, see
`dl-livefs-build` to simplify downloading them all.  You can then run
ubuntu-cdimage on it - `lp:ubuntu-cdimage README.local.` - or "frankenstein"
repack it - see the `iso-repack` script for details.

PPA builds require waiting for publishing - it's possible to bypass that with a
bit of [git magic](git-ppa-magic.md)
