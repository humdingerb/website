+++
type = "blog"
title = "Haiku Activity & Contract Report, January 2026"
author = "waddlesplash"
date = "2026-02-12 23:00:00-04:00"
tags = ["contractor", "activity report"]
+++

This report covers hrev59262 through hrev59355.

<!--more-->

### Applications

PulkoMandy added some missing locking to the file panel constructor, fixing broken open/save panel appearances in various applications. He also improved the appearance of drag-bitmaps (for use in drag-and-drop) for files, though there's still more work to be done here.

Jim906 expanded Tracker's use of the MIME sniffer, making Tracker behave much better about application filetypes on volumes and filesystems with no MIME support.

nipos adjusted Installer to make its EULA window more font-size sensitive.

sed4096 cleaned up and finished a patch started years ago to add a "disk image menu" to DriveSetup, allowing disk images to be worked with directly from there (and no longer needing to go to the command line.) humdinger came by later and cleared up some of the UI texts from the new functionality.

waddlesplash fixed a crash in Debugger when trying to debug certain large applications, like Mesa/LLVM.

humdinger implemented Shift+next/previous in Mail, which moves to the next or previous message without changing the status of the current message.

mull adjusted WebPositive to handle large filenames better in the Downloads window. humdinger came by later and tweaked the scrollbar alignment a bit, and later made a similar fix to Expander.

apl refactored how internal message keys are managed in HaikuDepot, to improve maintainability and pave the way for future improvements. He also changed the package view to only allow single packages to be selected at a time, fixing an old bug.

humdinger reworked applications to properly display their own names either in English or localized modes in all their various strings (e.g. "Deskbar preferences") depending on whether translated application names are enabled in Locale preferences. (Previously, only the names themselves on the filesystem and the like were affected by the setting.)

kallisti5 added some basic architecture awareness to the EFI loader installation code in Installer.

waddlesplash fixed a regression that changes to file size or last-modified in queries weren't live-updating in Tracker.

### Command line tools

cmeerw enabled IPv6 support for `telnet`, implemented IPv6 support in `netstat` and fixed its filtering functionality, and imported `traceroute6` from NetBSD.

nipos implemented the POSIX-required flags in `df`, and reworked its formatting in the non-POSIX mode to be more user-friendly.

PulkoMandy fixed the check for the BFS signature in `makebootable` when working with VMDK or similar formats.

nathan242 fixed a crash in `stat` syscalls printing in `strace`.

### Kits

SED4096 implemented system sounds in BAlert, so if you set sounds in the Sounds preferences for the "alert" messages, it should now work.

waddlesplash refactored Package Kit's BRepositoryCache to report package infos via a callback rather than reading them all into a `BPackageInfoSet`. This saves a lot of memory overhead and allocations, and thus also time, for anything that needs to read an entire package repo file.

### Servers

nipos adjusted the colors of close and collapse icons of notifications to be more visible in dark mode.

X512 implemented gradient stroke drawing in BView and app_server. He also reworked PicturePlayer to use a virtual class interface rather than a struct-of-function-pointers, improving type-safety and readability. KapiX then implemented a variety of missing PicturePlayer methods in the BoundingBoxPlayer.

KapiX removed an incorrect special case for opaque layers in the app_server, fixing blending of transparent pixels for them.

X512 adjusted the "get string bounding boxes" app_server API call to have a less idiosyncratic wire format, making it easier to write protocol analyzers.

### Drivers

The remainder of samuelrp84's patchset implementing new touchpad functionality was merged, including two-finger scrolling, edge motion, software button areas, and click finger support; and on the hardware side, driver support for Elantech "version 4" touchpads, with experimental code for versions 1, 2, and 3. (Version 2, at least, seems to be incomplete and had to be disabled for the time being.)

Lt-Henry made a number of fixes to the I2C-HID driver (which is not yet enabled by default), implementing some quirks generally supported by Linux as well as other missing logic.

waddlesplash synchronized the `rtl8125` driver with upstream OpenBSD, adding some support for new devices.

### File systems

nathan242 adjusted `write_overlay` (a filesystem layer used to handle writes when booted to the live environment from read-only media) to handle already-existing directories correctly.

axeld merged some patches from the "BFS resize" series (though there still remain a bunch more to be merged, and they're not all complete yet.)

AbdullahZulfiqar2005 fixed the BTRFS driver to not panic when encountering unknown compression types, but to just return an error.

waddlesplash implemented a minor optimization in the queries code in BFS. He also fixed the shared queries code to not send notifications for deleted nodes when `B_QUERY_WATCH_ALL` is set (as deletions will already have been reported), and then exposed `B_QUERY_WATCH_ALL` to the public API and added documentation for it.

### libroot & kernel

cmeerw fixed a problem updating the Neighbor Discovery Protocol cache entries for IPv6. He also implemented Path MTU discovery and multicast support for IPv6.

beaglejoe submitted a change to fix an internal bootloader header to be able to be included by C files again.

waddlesplash adjusted what information the internal `kernel_version` system information returns (on BeOS it returned some internal number; on Haiku it now returns `B_HAIKU_VERSION`), and adjusted `uname` to print more sensible information based on this in its `release` field.

waddlesplash fixed a number of minor POSIX compliance issues, such as new declarations in `search.h`, `unistd.h`, `semaphore.h`, cleanups and fixes to `limits.h.`, defining `getlocalename_l`, and more.

waddlesplash fixed and consolidated a number of mode checks in the kernel VFS layer, such as file-directory mode checks, open-mode checks, and more. (This fixed some POSIX compliance issues.) He also fixed some error codes both there and in the process group code.

waddlesplash replaced `strtok_r` with musl's version, and made some cleanups in the process.

korli changed `inet_net_ntop` and `inet_net_pton` to use the OpenBSD (instead of the NetBSD) versions, as these appear to be more standards-compliant.

waddlesplash refactored and consolidated how `ctype` data is stored and managed in libroot, making the implementation more maintainable, fixing some unclear indirections, and fixing a thread-safety issue. He then cleaned up some code in the character-conversion routines, and fixed the `MB_CUR_MAX` value on the default (UTF-8) encoding.

waddlesplash fixed timezone name handling in some internal time functions, fixing some incorrect `strftime` behaviors.

korli reworked TCP reset handling during connection close, to better match other OSes. He also fixed a typo in a comment.

waddlesplash introduced the constant `DEV_BSIZE` (which is mentioned but not required by POSIX) to `sys/param.h` and changed all computations of `stat.st_blocks` to use it, fixing a number of issues where filesystems had incorrectly used `st_blksize` instead.

nathan242 fixed some fallbacks for filesystems that don't support `SEEK_DATA`/`SEEK_HOLE` or `select()` (like the `write_overlay`), fixing `cp` on systems with the `write_overlay` activated.

archeYR fixed the EFI loader to not scan devices with block sizes of 0, fixing crashes on some devices.

korli implemented getresuid(), setresuid(), getresgid(), setresgid() from POSIX-2024.

waddlesplash exposed `posix_spawn_file_actions_add[f]chdir`. (It was implemented a while ago, but never added to the system headers.)

waddlesplash dropped a BeOS-only routine (`exect`) from the public ABI when not building for BeOS-compatible platforms.

### Build system

jscipione fixed a TODO to use `B_DEV_NAME_LENGTH` rather than a hardcoded `128` in a few places. OscarL came by later and fixed even more places.

PulkoMandy removed the build-system dependency on `bc`, and switched to using POSIX shell to do arithmetic calculations instead.

KapiX fixed the build of many unit tests, and changed the unit tests to work with test_app_server (an app_server that starts and runs in a window). He also introduced (based on an old patch by kallisti5) a `haiku_unittests` package target, and an image profile with the package activated by default. (The idea is to eventually be able to run the unit tests automatically in CI.)

PulkoMandy bumped many copyright years in package metadata and menus to 2026.

waddlesplash (finally) synchronized the set of packages used to build Haiku with current HaikuPorts for x86_gcc2h and x86_64, fixing some build issues and updating the tool used to generate the build-packages repository in the process. madmax came by later and fixed the build for the architectures that don't yet have the new packages (like RISC-V).

### Documentation

cafeina wrote documentation for MailAttachment, MailDaemon, and mail_encoding.

PulkoMandy added some documentation about some known POSIX non-comformities in Haiku.

OscarL fixed some typos in the "filesystems overview" documentation.

### Are we beta6 yet?

Getting closer. A number of issues have been fixed or now have fixes pending (including one memory/swap-related one that's among the more critical regressions), but there's still more to be done.

### That's all, folks!

Thanks again to all who contribute to Haiku, and especially those donors who make my contract possible!
