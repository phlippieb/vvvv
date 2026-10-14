# vvvv

## Idea

A simple wrapper around restic to provide dirt-basic version control for large files.

Doesn't care about unstaged/staged status or individual files. Only cares about saving a named snapshot of everything in your folder as it is right now.

## Features - absolute basic

`vvvv init`
- creates a .vvvv folder here
- from now on, vvvv will store snapshots and metadata of this directory to the .vvvv folder.

`vvvv save <message>'
- stores a snapshot of everything except .vvvv/ to the restic backup location at .vvvv/backup
- uses passwordless flags with restic; no encryption needed
- reads the snapshot ID returned by restic, then writes it to a .vvvv/list file along with the date and message, for later reference

`vvvv list`
- lists all snapshots with their messages, using .vvvv/list

`vvvv restore <snapshot-id>`
- restores the full backup at the given snapshot ID
- to prevent destructive overwrites, the restore is written to ./vvvv-<snaptshot-id>

## Features - nice-to-haves, not needed now

- Parent folder awareness: if a parent folder contains a .vvvv, backup there. (Without this, any vvvv command except init must fail unless the current folder is inited.)
- Prune: Remove snapshots from backup file; e.g. to discard old versions

## Stack

Script written in bash for portability?

Requires restic to be installed (checked with `which restic`)

For the list file: use something like yaml? may need a parser and formatter then
