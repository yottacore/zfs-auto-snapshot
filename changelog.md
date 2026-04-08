<!-- markdownlint-disable MD007 -- Unordered list indentation -->
<!-- markdownlint-disable MD010 -- No hard tabs -->
<!-- markdownlint-disable MD024 -- No duplicate headings [OK with no TOC] -->
<!-- markdownlint-disable MD033 -- No inline html -->
<!-- markdownlint-disable MD041 -- First line in a file should be a top-level heading -->
<!-- markdownlint-disable MD055 -- Table pipe style [Expected: leading_and_trailing; Actual: leading_only; Missing trailing pipe] -->
# Changelog

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## 2.0.0

### Notes

This is a major semantic version bump, because the snapshot file naming convention has fundamentally changed, triggering the requirements of semantic naming.

However the code change was trivial, as noted below.

### Added

- Added more github files to make a fully fleshed-out project.

### Changed

- Changed the value of three variables to support new snapshot naming convention.
- Changed `#!/bin/sh` to `#!/bin/bash` in `zfs-auto-snapshot`. Since the script is not `source`d from anywhere, it affects nothing. All POSIX script is backwards-compatible in `bash`. The change immediately fixes the existing bug of having variables declared with `local`.
- The `.sh` extension is removed from the script, to better align with user expectations of the version installable from distro repos.
- Slight refactor of the non-code portions of the main script, to be (arguably) more readable and in line with other 'jim-collier' github projects.
- Updated ancillary project files, such as `README`, from plain text to fully fleshed-out markdown documents.

### Removed

- For now, the last additions on the zfsonlinux project were reverted. They were just a few lines related to macOS Darwin support, and detecting zero-byte snapshots. The latter will be added back in later.

Ironically, that update didn't fix at least one glaring bug - the use of non-POSIX 'local' variables in a POSIX-only script.

## 1.2.5

### Added

- Jonathan Carter 2019-09-25, 15:24 SAST:
	- Start a changelog
	- Accept PR#94 from aimileus/macos
		- Replace --utc longform option with -u for macos compatibility
	- Accept PR#107 from ArakniD/master
		- Add optional label for snap removals

## NEXT VERSION

### Notes

### Added

### Changed

### Removed

### Other work
