<!-- markdownlint-disable MD007 -- Unordered list indentation -->
<!-- markdownlint-disable MD010 -- No hard tabs -->
<!-- markdownlint-disable MD033 -- No inline html -->
<!-- markdownlint-disable MD055 -- Table pipe style [Expected: leading_and_trailing; Actual: leading_only; Missing trailing pipe] -->
<!-- markdownlint-disable MD041 -- First line in a file should be a top-level heading -->

<!-- TOC -->

- [Conventions](#conventions)
- [First steps](#first-steps)
- [Backlog](#backlog)
	- [Bugs](#bugs)
	- [New features and enhancements](#new-features-and-enhancements)
	- [Done](#done)
		- [Done - Bugs](#done---bugs)
			- [Done - New features and enhancements](#done---new-features-and-enhancements)
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
| ✋   | Deferred
| 🚫   | Canceled

## First steps

## Backlog

### Bugs

### New features and enhancements

- 🔘 Bash 5+ cleanup pass. Exploratory. Should be non-issues:
	- Bash 5 has been standard in Linux distros since 2020.
	- BSD gets it from native repos.
	- macOS is two commands away via brew
		- I.e. not a stretch as a dependency, esp. for someone monkeying with ZFS. Would drop the 3.2 workarounds (no associative arrays, namerefs, `declare -g`, `[[ -v ]]`, `${var,,}`, `inherit_errexit`).

- 🔘 Flags to explicitly set the time basis for snapshot names: `--utc`, `--local-time`, `--time-offset=nnn`.
	- Whatever these produce must stay a legal ZFS snapshot name - a `+` offset is not, which is why the `%z` stamp was dropped in the first place.
	- Changing the stamp width strands every existing snapshot: pruning is a glob match on `$dataset@$glob`, so a name that no longer matches is never pruned. Either keep the stamp 15 chars, or ship a rename sweep with the change.

- 🔘 A family of user-level ZFS snapshot helper scripts.
	- Take a manual snapshot given a filesystem and a description - script prepends the date/time in the right format, escapes and truncates the description as needed.
	- Destroy snapshots by nestable `--include='regex'` / `--exclude='regex'` expressions, plus `--older-than=n` (date/time, or a 'days'/'hours' suffix).

- 🔘 Work through the relevant high-priority issues and PRs still open on the original project.

### Done

#### Done - Bugs

- ✅ `IFS` had lost its tab (a refactor regression) - this silently broke every multi-dataset and named-dataset run, since `for X in $TARGETS` and the `read NAME PROPERTIES` arg check both need tab in `IFS`. Restored tab+newline (matches upstream).
- ✅ `-v`/`--verbose` never printed info messages (the refactor changed the quiet test to a set-test, `${opt_quiet+x}`). Restored the upstream value-test so `-v` works.
- ✅ Snapshot names carried a `%z` offset; a `+` offset is invalid in a ZFS name and fails on UTC/east-of-UTC hosts. Dropped the offset (stamp is now `YYYYmmDD-HHMMSS`), resized the prune glob, simplified the `--fast` path.

##### Done - New features and enhancements

- ✅ Bash 3.2+ cleanup pass: `[[ ]]`/`(( ))`, typed/lowercase locals, `set -euo pipefail` with the needed guards, and tidier marmot-style formatting. No behaviour change beyond the two regressions above; verified by a dry-run fingerprint across 12 modes plus a synthetic prune/destroy test.

- ✅ Restore the two zfsonlinux deltas the refactor had dropped, for 1:1 ancestral parity: `--min-size`/`-m` (skip a snapshot when too little is written since the last) and the Darwin GNU-getopt shim. Darwin stays unsupported; the shim is kept only for parity. `--min-size` is documented in usage and the man page (upstream left it undocumented).

### Deferred

### Canceled
