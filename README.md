# vvvv, or "versions"

A `restic` wrapper for dirt-basic version control of large binary files.

## Install

1. Install [restic](https://restic.net/)
2. Make `vvvv` executable and place it in your path

## Usage

`vvvv init`: initialise vvvv in the current folder. Note: you must run all vvvv commands from the folder root.

`vvvv save [<message>]`: saves everything in your current folder as a snapshot. Message is optional.

`vvvv list`: list all your snapshots, showing each one's ID, date, and message.

`vvvv restore <snapshot-id>`: restores the given snapshot to a folder named like `vvvv-<snapshot-id>` -- doesn't overwrite your current files.

