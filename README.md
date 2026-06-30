<!-- markdownlint-disable MD007 -- Unordered list indentation -->
<!-- markdownlint-disable MD010 -- No hard tabs -->
<!-- markdownlint-disable MD033 -- No inline html -->
<!-- markdownlint-disable MD041 -- First line in a file should be a top-level heading -->
<!-- markdownlint-disable MD055 -- Table pipe style [Expected: leading_and_trailing; Actual: leading_only; Missing trailing pipe] -->
<div align="center">

[![!#/bin/bash](https://img.shields.io/badge/-%23!%2Fbin%2Fbash-1f425f.svg?logo=gnu-bash)](https://www.gnu.org/software/bash/)
![License: GPL v2](https://img.shields.io/badge/License-GPLv2-blue.svg)
![Lifecycle](https://img.shields.io/badge/Lifecycle-RC-blue)
![Support](https://img.shields.io/badge/Support-Maintained-brightgreen)
![Status: Passing](https://img.shields.io/badge/Status-Passing-brightgreen)

</div>
<!--
![License: GPL v2](https://img.shields.io/badge/License-GPLv2-blue.svg)
![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Lifecycle: Alpha](https://img.shields.io/badge/Lifecycle-Alpha-orange)
![Lifecycle: Beta](https://img.shields.io/badge/Lifecycle-Beta-yellow)
![Lifecycle: RC](https://img.shields.io/badge/Lifecycle-RC-blue)
![Lifecycle: Stable](https://img.shields.io/badge/Lifecycle-Stable-brightgreen)
![Lifecycle: Deprecated](https://img.shields.io/badge/Lifecycle-Deprecated-red)
![Status: Deprecated](https://img.shields.io/badge/Status-Deprecated-orange)
![Status: Archived](https://img.shields.io/badge/Status-Archived-lightgrey)
![Lifecycle: EOL](https://img.shields.io/badge/Lifecycle-EOL-lightgrey)
![Coverage](https://img.shields.io/badge/Coverage-25%25-red)
![Coverage](https://img.shields.io/badge/Coverage-50%25-orange)
![Coverage](https://img.shields.io/badge/Coverage-75%25-yellow)
![Coverage](https://img.shields.io/badge/Coverage-90%25-brightgreen)
![Status: Passing](https://img.shields.io/badge/Status-Passing-brightgreen)
![Status: Failing](https://img.shields.io/badge/Status-Failing-red)
-->

<!-- TOC ignore:true -->
# zfs-auto-snapshot

<!-- TOC ignore:true -->
## Table of ontents

<!-- TOC -->

- [Introduction](#introduction)
- [Roadmap](#roadmap)
	- [Done](#done)
		- [Better naming convention](#better-naming-convention)
		- [Shebang changed from #!/bin/sh to #!/bin/bash.](#shebang-changed-from-binsh-to-binbash)
	- [Future](#future)
		- [Add the minor deltas of zfsonlinux version back in, over debian apt version](#add-the-minor-deltas-of-zfsonlinux-version-back-in-over-debian-apt-version)
		- [Bash 4.4+ code cleanup and simplification for better readability, maintainability](#bash-44-code-cleanup-and-simplification-for-better-readability-maintainability)
		- [Allow flags to explicitly set UTC, local, or specified time offset](#allow-flags-to-explicitly-set-utc-local-or-specified-time-offset)
		- [Include a family of user-level ZFS snapshot helper scripts](#include-a-family-of-user-level-zfs-snapshot-helper-scripts)
		- [Address relevant high-priority issues and PRs from original project](#address-relevant-high-priority-issues-and-prs-from-original-project)
- [Installation](#installation)
- [Uninstallation](#uninstallation)
- [Copyright and license](#copyright-and-license)

<!-- /TOC -->

## Introduction

Automatically create, rotate, and destroy periodic ZFS snapshots.

The original project this was forked from - via [zfsonlinux](https://github.com/zfsonlinux/zfs-auto-snapshot) page - [is officially dead](https://github.com/zfsonlinux/zfs-auto-snapshot/issues/117), unless it's revived somtime (now six+ years later). The last commit was 2019-09-25, as of the time this sentence was originally written on 2026-04-08.

The original project has a few trivial enhancements over the version installable via (e.g.) `apt install zfs-auto-snapshot`, but the script is mostly the same. If you have `zfs-auto-snapshot` installed via `apt install`, both OG github version and this one are newer and better - you should uninstall the one via `apt`:

This, in spite of the original script having some obvious minor bugs that even linters pick up on. (E.g. non-POSIX bashisms in a `#!/bin/sh` script.)

It's also time for an update to bring some freshness to the project. (See roadmap below.)

___Note__: While some attempt may be made to address original open [issues](https://github.com/zfsonlinux/zfs-auto-snapshot/issues) and [PRs](https://github.com/zfsonlinux/zfs-auto-snapshot/pulls), the focus will remain on ZFS-On-Linux. And only the Kernel module. In other words, not on BSD, Darwin, or the legacy FUSE version. (Although this will likely continue to work fine with those as long as `bash` is installed on BSD, and/or upgraded from v3 to 4.4+ e.g. on Darwin.)_

## Roadmap

### Done

#### Better naming convention

- Instead of e.g.:
	- `filesys@zfs-auto-snap_frequent_2026-04-08-0259`, in UTC time, you get:
	- `filesys@20260408-195930_zfs-auto-snap_frequent` in local time.

- Benefits:
	- More natural sorting by date/time of snapshots, when listed sorted by name.
	- UTC time, with the same updated format, can still be specified in the script with existing constant.
	- Manual snapshots with same convention will also be sorted correctly, e.g. when named something like:
		- `filesys@20260408-195945_my-manual-important-snapshot`

#### Shebang changed from `#!/bin/sh` to `#!/bin/bash`

- This is to immediately fix a bug in the original script, where the `sh` shebang is incompatible with variables declared with `local`.
- Ongoing, will allow for cleanup of unnecessarily convoluted POSIX syntax, and potentially more powerful features in the future.

### Future

#### Add the minor deltas of the zfsonlinux version back in, over debian apt version

The current version, while it enhances features and compatibility on Linux as noted above, goes slightly backwards by just a few lines of code, when diff-compared to the zfsonlinux github version. The latter detects zero-size snapshots, and adds Darwin compatibility. (Though the latter is explicitly not a goal of this fork, in part since ZFS development for Darwin lags significantly behind Linux.) But those lines will be added back in so that the main script is is a 1:1 ancestral fork.

#### Bash 3.2+ code cleanup and simplification for better readability, maintainability

Bash 3.2+ allows for idiomatic language improvements, more compact syntactic sugar, and safety - compared to legacy POSIX-only.

#### Allow flags to explicitly set UTC, local, or specified time offset

E.g. `--utc`, `--local-time`, or `--time-offset=nnn`

#### Include a family of user-level ZFS snapshot helper scripts

Examples:

- Create a manual snapshot with a filesystem and description. Script will prepend date/time in correct format, and properly escape and shorten description if necessary.
- Destroy snapshots based on a series of nestable --include='regex' and/or --exclude='regex' expressions as arguments, as well as --older-than=n arg (in date/time format or 'days' 'hours' etc. suffix).

#### Address relevant high-priority issues and PRs from original project

## Installation

~~~bash
## Remove package-installed version if installed - or via rpm, pacman, etc.
sudo apt purge zfs-auto-snapshot
[[ -d /sbin/zfs-auto-snapshot ]] && sudo rm /sbin/zfs-auto-snapshot

## Install this improved version
cd $(mktemp -d)
git clone git@github.com:jim-collier/zfs-auto-snapshot.git
cd zfs-auto-snapshot
make install
~~~

## Uninstallation

~~~bash
## Remove this version
[[ -d /sbin/zfs-auto-snapshot ]] && sudo rm /sbin/zfs-auto-snapshot

## Reinstall package version - or via rpm, pacman, etc.
sudo apt install --reinstall zfs-auto-snapshot
~~~

## Copyright and license

> Copyright © 2011 Darik Horn <dajhorn@vanadac.com><br>
> Copyright © 2026 Jim Collier \[-wneGcMGaw-1HuTBMeQagUP2zmBhL18HZh1KRYH5eis=\]<br>
> → Changes documented above, and in changlog.md<br>
> Licensed under the [GNU GPL v2](https://www.gnu.org/licenses/gpl-2.0.html). No warranty.
