<!-- markdownlint-disable MD007 -- Unordered list indentation -->
<!-- markdownlint-disable MD010 -- No hard tabs -->
<!-- markdownlint-disable MD041 -- First line in a file should be a top-level heading -->

<!-- TOC ignore:true -->
# Project backlog

<!-- TOC ignore:true -->
## Table of contents

<!-- TOC -->

- [Introduction](#introduction)
- [Issues](#issues)
- [Old format](#old-format)
	- [Bugs](#bugs)
	- [Features and enhancements](#features-and-enhancements)
	- [Done](#done)
		- [Done - Bugs](#done---bugs)
		- [Done - Features and enhancements](#done---features-and-enhancements)
	- [Deferred](#deferred)
	- [Canceled](#canceled)
- [Template](#template)

<!-- /TOC -->

## Introduction

Going forward, new issues in the new template at the bottom of this file, will go in the '## New format' section only. No more status emojis. Refer to '## Reference' for sort order. Issues in the old format (with status emojis) won't be refactored, but will continue to be worked until moved to closed, canceled, or deferred sections, and emojis updated. (Eventually this will all be moved to nano-git-db anyway. This new template is an intermediate effort to make issues going forward more structured and importable.)

## Issues

- CICD reset: a small cicd.bash
	- ID: 2026102416234689
	- Type: Task
	- Status: Queued
	- Priority: High
	- Opened: 2026-10-24 16:23
	- Requirements:
		- There's no pipeline or test suite. Add a small `cicd.bash` per the shared CICD rules.
		- `bash -n` and shellcheck, with lint output kept and a lint `--check` gate.
		- Regression tests from dry runs, which need no root, for each Done item. The IFS tab, `-v`, the 15-char stamp, and `--min-size` are the first ones.
		- Run them under a pinned Bash 3.2 and a pinned Bash 5 in a container, falling back to the host. Run them on the FreeBSD VM too, through the shared host lock and the VM state helper, and SKIP when it's not there.
		- Each test gets a base 32 minute ID and one PASS, FAIL, or SKIP line. Steps use the numbered lines.
		- The shared flags, `--merge-gate` from a pre-push hook on `main` that skips docs-only pushes, low priority, the run temp folder layout, and container cleanup by label.
		- Details in the private notes.
		- Sweeping override, 2026-10-24: this supersedes any earlier decision on this project that conflicts with it.

## Old format

### Bugs

### Features and enhancements

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

#### Done - Features and enhancements

- ✅ Bash 3.2+ cleanup pass: `[[ ]]`/`(( ))`, typed/lowercase locals, `set -euo pipefail` with the needed guards, and tidier marmot-style formatting. No behaviour change beyond the two regressions above; verified by a dry-run fingerprint across 12 modes plus a synthetic prune/destroy test.

- ✅ Restore the two zfsonlinux deltas the refactor had dropped, for 1:1 ancestral parity: `--min-size`/`-m` (skip a snapshot when too little is written since the last) and the Darwin GNU-getopt shim. Darwin stays unsupported; the shim is kept only for parity. `--min-size` is documented in usage and the man page (upstream left it undocumented).

### Deferred

### Canceled

## Template

### Old format

- 🔘 Not started

- 🛠️ Started, and/or partially complete

- 🔬 Testing not started or finished

- ✋ Defer

- ✅ Complete

- 🚫 Canceled

### New format

- Notes:

	- Only use rows that you actually need or expect will be filled in. Always fill in the title, ID, Type, Status, Opened and Created by.

	- The ID is the local time to the hundredth of a second. Opened is when it was written down, which may differ. (Use a keyboard macro and possibly something like project 'zuid' to generate.)

	- Status values meaning: Testing means the fix is in and checks are running or still to run. Waiting on signoff means automated testing passed. Moot means something else changed that made it irrelevant. Canceled means it still applies but was decided against. Waiting for testing means the fix is in and waits on a long CI run or an outside test host. Can't reproduce means a real attempt to reproduce it failed.

	- As issues are worked, and statuses change, place them in correct sorting order within the list:
		- First by status: Waiting for answers, Waiting on signoff, Testing, Waiting for testing, Can't reproduce, Stalled, Started, Queued, Done, Deferred, Canceled, Moot
		- Then by severity|priority: Critical, High, Avg, Low
		- Then by type: Bugs, [not bugs together]

	- Rows marked [Bug] are for bugs only, and rows marked [Feature] for features and enhancements. Priority and Severity share one row and one scale. Priority is for a Feature or Enhancement, and Severity for a Bug. Children are not nested. They sit at the top level and point back with Parent ID.

Template:

- Title
	- ID: YYYYmmDDHHMMSSNN
	- Type: [Bug|Feature|Enhancement|Task]
	- Status: [Queued|Waiting for answers|Waiting on signoff|Waiting for testing|Started|Testing|Stalled|Can't reproduce|Moot|Canceled|Deferred|Done]
	- Needs local test suite run?:
	- Needs external testing:
	- Priority [Feature|Enhancement] | Severity [Bug]: [Critical|High|Avg|Low]
	- Opened:
	- Opened by:
	- Assigned to:
	- Parent ID:
	- Prereq IDs:
	- Related IDs:
	- Target OS:
	- Test environment:
	- Version and build:
	- Requirements  [Feature]:
		- Hierarchical bulleted list.
	- Steps to reproduce [Bug]:
		- …
	- Incorrect behavior [Bug]:
	- Expected behavior [Bug]:
	- Reproduced [Bug]: [No, or when, where and how]
	- Possible cause [Bug]:
	- Actual cause [Bug]:
		- …
	- Estimated effort: [High|Avg|Low]
	- Actual effort: [High|Avg|Low]
	- Progress log:
		- …
	- Decisions:
		- …
	- Actual fix [Bug]:
	- Branch:
	- Commit:
	- Test case: [Reason not applicable, or CI test case #]
	- Acceptance signoff:
	- Superseded by ID:
	- Closed:
