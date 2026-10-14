# vvvv

## Idea

A simple wrapper around restic to provide dirt-basic version control for large files.

Doesn't care about unstaged/staged status or individual files. Only cares about saving a named snapshot of everything in your folder as it is right now.

## Stack

POSIX `sh` script for portability.

Requires restic to be installed (checked with `which restic`). No minimum version enforced.

Restic is chosen over git-lfs for better space efficiency with binary files.

## Decisions

- **Password**: Restic requires a password. vvvv uses a hardcoded empty string. No encryption.
- **Restic repo location**: `.vvvv/backup` is the restic repository itself (created with `restic init`).
- **Snapshot list**: Stored in `.vvvv/list` as a flat file, one entry per line: `<snapshot-id>\t<ISO8601-date>\t<message>`. Messages are kept here rather than in restic tags because restic tags are not a good vehicle for duplicate messages.
- **Restore target**: `./vvvv-<snapshot-id>` — always relative to the vvvv root directory. A clean checkout to a new subdirectory; the existing working tree is never touched.
- **Scope**: All commands must be run from the directory containing `.vvvv/`. No subdirectory support, no `--path` args.
- **Concurrency**: Not handled. Restic's own repo locking prevents simultaneous writes.

## Commands

### `vvvv`

Prints available commands.

### `vvvv init`

- Creates `.vvvv/` and initializes a restic repository at `.vvvv/backup`.
- If `.vvvv/` already exists, prints a warning and exits non-zero.

### `vvvv save <message>`

- Creates a snapshot of everything in the current directory except `.vvvv/` (backup guts) and `vvvv-*` (restored versions)
- Uses `restic backup --exclude .vvvv .` (etc).
- The message must be handled like a git commit message — quoting, spaces, and special characters must work.
- Appends `<snapshot-id>\t<ISO8601-date>\t<message>` to `.vvvv/list`.
- Empty snapshots (nothing changed) are allowed and proceed normally.
- If the process dies between the restic snapshot and the list write, the snapshot is orphaned. This is accepted as a known limitation; no reconciliation is performed.

### `vvvv list`

- Prints all snapshots from `.vvvv/list` in reverse chronological order (most recent first).

### `vvvv restore <snapshot-id>`

- Restores the full snapshot at the given ID into `./vvvv-<snapshot-id>-<date>-<message>/`, preserving original relative paths inside that directory.
- The working tree is never modified — this is a clean checkout to a new subdirectory.
- If `./vvvv-<snapshot-id>-<etc>/` already exists, refuses to proceed and exits non-zero.

## Known limitations

- No parent folder awareness. Any vvvv command except `init` fails outside the vvvv root with "not a vvvv repo".
- No prune support.
- Orphaned snapshots (restic snapshot succeeded but list write failed) are not reconciled.
- Concurrency is unhandled.
