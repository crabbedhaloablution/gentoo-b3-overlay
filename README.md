# gentoo-b3-overlay
Gentoo overlay for the Excito B3 miniserver.

## List of ebuilds

<img src="https://raw.githubusercontent.com/sakaki-/resources/master/excito/b3/Excito_b3.jpg" alt="Excito B3" width="250px" align="right"/>

* **app-portage/b3-check-porthash** [source](https://github.com/sakaki-/gentoo-b3-overlay/tree/master/app-portage/b3-check-porthash/files)
  * This provides a repo post-sync hook, `/etc/portage/repo.postsync.d/b3-check-porthash`, which will be activated iff the main gentoo repo is rsync'd to the `rsync://isshoni.org/gentoo-portage-b3 host`; in this case, the repo's integrity will be checked against the signed `repo.hash` "master" hash, and the sync aborted with an error if it differs. This provides assurance that the repo has not been tampered with, despite the use of an unauthenticated transport (`rsync`).
* **dev-libs/lzo** [upstream](http://www.oberhumer.com/opensource/lzo/download/)
  * At the time of writing, the Gentoo tree contains only version <= 2.08 of dev-libs/lzo. Unfortunately, there is a serious alignment bug ([#757037 on Debian](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=757037#32)) with this library on armv5tel, preventing it working correctly (and inhibiting the use of certain client apps, such as openvpn). The problem is fixed in version 2.09 of the library, for which a placeholder ebuild is provided here.
* **sys-kernel/buildkernel-b3** [source](https://github.com/sakaki-/buildkernel-b3)
  * Provides a script (**buildkernel-b3**(8)) to build a bootable Gentoo Linux kernel for the Excito B3, targeting either HDD or USB deployment. Can be used on the B3 directly, or on a Gentoo PC (when cross-compiling via `crossdev`). A manpage is included.
* **sys-kernel/gentoo-b3-kernel-bin** [binary](https://github.com/sakaki-/gentoo-b3-kernel)
  * Provides a binary kernel package for the B3 miniserver; a new ebuild is automatically created to mirror each release of [gentoo-b3-kernel](https://github.com/sakaki-/gentoo-b3-kernel).
* **sys-apps/b3-init-scripts** [source](https://github.com/sakaki-/gentoo-b3-overlay/tree/master/sys-apps/b3-init-scripts/files)
  * Provides a set of simple init scripts for the B3 (to turn on the LED on boot, copy across network settings when booting etc.).
* **net-wireless/hostapd** [upstream](http://hostap.epitest.fi)
  * Provides `hostapd-2.0-r1.ebuild`; this has been removed from the Gentoo tree, but newer versions require (patent-protected) EC from the `openssl` library, which is incompatible with the `bindist` USE flag.

## Installation

As of version >= 2.2.16 of Portage, **gentoo-b3-overlay** is best installed (on Gentoo) via the [new plug-in sync system](https://wiki.gentoo.org/wiki/Project:Portage/Sync). It will supply a repository named **gentoo-b3**

The following are short form instructions. If you haven't already installed **git**(1), do so first:

    # emerge --ask --verbose dev-vcs/git 

Next, create a custom `/etc/portage/repos.conf` entry for the **gentoo-b3** overlay, so Portage knows what to do. Make sure that `/etc/portage/repos.conf` exists, and is a directory. Then, fire up your favourite editor:

    # nano -w /etc/portage/repos.conf/gentoo-b3.conf

and put the following text in the file:
```
[gentoo-b3]

# Gentoo overlay for the Excito B3 miniserver
# Maintainer: sakaki (sakaki@deciban.com)
 
location = /usr/local/portage/gentoo-b3
sync-type = git
sync-uri = https://github.com/sakaki-/gentoo-b3-overlay.git
priority = 50
auto-sync = yes
```

Then run:

    # emaint sync --repo gentoo-b3

If you are running on the stable branch by default, allow **~arm** keyword files from this repository. Make sure that `/etc/portage/package.accept_keywords` exists, and is a directory. Then issue:

    # echo "*/*::gentoo-b3 ~arm" >> /etc/portage/package.accept_keywords/gentoo-b3-repo
    
Now you can install packages from the overlay. For example:

    # emerge --ask --verbose sys-kernel/buildkernel-b3

## Maintainers

* [sakaki](mailto:sakaki@deciban.com)
