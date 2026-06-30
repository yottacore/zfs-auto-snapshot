<!-- markdownlint-disable MD007 -- Unordered list indentation -->
<!-- markdownlint-disable MD010 -- No hard tabs -->
<!-- markdownlint-disable MD033 -- No inline html -->
<!-- markdownlint-disable MD055 -- Table pipe style [Expected: leading_and_trailing; Actual: leading_only; Missing trailing pipe] -->
<!-- markdownlint-disable MD041 -- First line in a file should be a top-level heading -->

<!-- TOC ignore:true -->
# Project backlog

This is a product backlog just for pre-v1.0.0 release. After that, bugs, features, and enhancements will be mananged in Github Issues, and/or [todo.md](../todo.md)

<!-- TOC ignore:true -->
## Table of contents
<!-- TOC -->

- [Conventions](#conventions)
- [First steps](#first-steps)
- [Backlog](#backlog)
	- [Bugs](#bugs)
	- [New features and enhancements](#new-features-and-enhancements)
	- [Deferred](#deferred)
	- [Canceled](#canceled)

<!-- /TOC -->

## Conventions

In each section, items are listed approximately from newest to oldest.

| Icon | Status
| :--: | :--
| 🔘   | Not started
| 🛠️   | Started, and/or partially complete
| ✅   | Complete
| 🚫   | Canceled

## First steps

## Backlog

### Bugs

- ✅ Snapshot names carried a `%z` offset; a `+` offset is invalid in a ZFS name and fails on UTC/east-of-UTC hosts. Dropped the offset (stamp is now `YYYYmmDD-HHMMSS`), resized the prune glob, simplified the `--fast` path.

### New features and enhancements

- ✅ Restore the two zfsonlinux deltas the refactor had dropped, for 1:1 ancestral parity: `--min-size`/`-m` (skip a snapshot when too little is written since the last) and the Darwin GNU-getopt shim. Darwin stays unsupported; the shim is kept only for parity. `--min-size` is documented in usage and the man page (upstream left it undocumented).

### Deferred

### Canceled
