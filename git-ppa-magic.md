One can test livecd-rootfs changes by way of livefs builds in launchpad mere seconds after a git push, however it requires a little setup.

First, some backstory on why we would want to do livefs builds in launchpad, along with guidance on how to do so:

An interesting corner of how Ubuntu image builds work is livecd-rootfs.  Livecd-rootfs carries critical configuration information about the ISOs and preinstalled images that we build.  This is a package that lives in the archive, so is subject to normal archive rules like uploading to the devel series, (hopefully) passing autopkgtest, and migrating to the release pocket where it is used for daily builds.  Livecd-rootfs is also subject to SRU rules, so when we want to modify the image builds for an LTS we need to follow the SRU process as well.

Testing livecd-rootfs changes can be unexpectedly difficult, however.  It’s not great to just upload some changes untested to livecd-rootfs to the archive, only to find out some time later, after the package migrates, that in fact you’ve broken several daily builds.  The ubuntu-old-fashioned project is one possible answer to the question of testing livecd-rootfs changes. I’ve had some discomfort with old-fashioned due to an inherent mistrust of an alternate set of scripts that would, because they are different, eventually have important differences that affect testing.  Also, I have observed some behavior differences like initrds that are generated differently in old-fashioned versus what happens in livefs builds in launchpad, but that hasn’t been investigated yet.  I might be using old-fashioned incorrectly.  Some people also run livecd-rootfs directly, but that seems like a more manual version of what old-fashioned does.

Another option is to do livefs builds in launchpad.  This can be done, presuming you are in the correct launchpad group, launchpad-livefs-builders.  The topic of membership in that group is out of scope for this post, but if you’re someone who finds themself working on livecd-rootfs you might be a member of that group already. I have previously written up some instructions and hosted scripts written by  on the topic of doing livefs builds in launchpad - see my livecd-rootfs-testing repo for details on that front.  The result of that is that livefs builds can be run, using livecd-rootfs from the archive or a PPA.

Building a livefs from a PPA just means that we do a livecd-rootfs upload to a PPA, wait a bit, then run start-livefs-build with the --ppa option. Doing this is already an enormous improvement over the upload-and-hope plan.  For infrequent livecd-rootfs uploads this may well be enough.  Once you’re doing that though, the iteration of testing of livecd-rootfs changes becomes bound by the time it takes the PPA publisher to complete the job.  This is because you can’t just upload to the PPA and immediately kick off a livefs build, one must wait for the full flow including building the binary debs and waiting for the publisher step.  This publishing step is sometimes quick, but other times it can take hours, and that means iterating on fixes to livecd-rootfs with PPA builds will delay waiting on that.

There is room for further improvement, which is the driving reason for this post.  This started as a suggestion from  and has evolved a bit from there.

The plan here is to create a copy of livecd-rootfs with a postinst that does a git checkout of your branch under test, running the build with that git checkout instead of the actual contents of the package.  This livecd-rootfs with the special postinst is then uploaded to a PPA, and that PPA becomes the target of start-livefs-build --ppa.  Example here of the postinst: https://git.launchpad.net/~dbungert/livecd-rootfs/commit/?id=d120733209032bb8eef48c71a807b3f09ac4097d

This postinst does the following:

checkout a copy of livecd-rootfs from my fork, using the ppa-test branch

report the commit in use, which is helpful to see in livefs build logs later to ensure I’m testing what I think I’m testing

replace the installed livecd-rootfs with the one from the ppa-test branch

One last trick - this copy of livecd-rootfs in the PPA should be dpkg --compare-versions larger than anything that will be uploaded to the archive in the future.  I use an epoch for this, but let’s just be careful and not really upload livecd-rootfs to the archive with the epoch set.

Finally, the PPA needs to be updated at least once per series - a simple changelog update to set the new series and version should be sufficient.

With all this in place, push livecd-rootfs changes to the fork and branch listed in the postinst, and start-livefs-build.
