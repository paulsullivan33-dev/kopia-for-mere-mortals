# Recipes

Four finished jobs, each built from
commands the earlier chapters verified.
Paths are the lab's shape; substitute
your own folders once, when you adopt a
recipe, and keep the password handling as
written — in the environment or a
protected file, never in the script
itself.

## 1. A nightly backup of a documents folder

A complete script, suitable for cron:

```bash
#!/bin/sh
# Nightly Kopia snapshot of /home/you/Documents
set -e
export KOPIA_PASSWORD='read-from-your-password-manager'
export KOPIA_CHECK_FOR_UPDATES=false
kopia repository connect filesystem --path /home/you/backups/kopia-repo
kopia snapshot create /home/you/Documents
```

Run by hand once and check it the
chapter 6 way — `kopia snapshot list`
should show the new snapshot with today's
date — before trusting a scheduler with
it. Add it to cron with `crontab -e`:

```text
15 2 * * * /home/you/bin/nightly-backup.sh >> /home/you/backups/backup.log 2>&1
```

2:15 AM daily, with output kept in a log
you can read when a run fails. Connecting
at the start of the script makes it work
even after a reboot cleared nothing but
your memory; connecting when already
connected is harmless.

## 2. A second copy on a second disk

With the main repository connected
(chapter 5):

```bash
mkdir -p /mnt/other-disk/kopia-repo-copy
kopia repository sync-to filesystem --path /mnt/other-disk/kopia-repo-copy
```

Then prove the copy the chapter 5 way:
connect to it under a second config file
and list snapshots. Repeat the sync
nightly, after the snapshot, by adding
both lines to the script above. Remember
the standing rule — sync copies, it does
not delete; a copy that must shrink needs
`--delete`, given deliberately, by hand,
not by a cron job.

## 3. Check a backup without restoring it

Before you need a restore is when to
learn whether there is anything to
restore:

```bash
kopia snapshot verify; echo "verify exit: $?"
kopia snapshot list
```

Exit 0 from verify plus a snapshot list
whose latest entry is as recent as you
expect is the whole audit (chapter 9).
For one precious folder, go further once
a season: restore its newest snapshot to
a scratch folder and `diff -r` it against
the live folder, letting only the files
you know changed explain the differences
(chapter 7's exact check).

## 4. Move a repository to a new home

Because a repository is just sealed
blobs, moving it is copying it — the sync
of recipe 2 *is* the move, done safely:

1. `sync-to` the new location (chapter 5).
2. Connect to the new location and check
   it: `repository status`, `snapshot
   list`, one test restore of a single
   file (chapter 7).
3. Only when the new home passes: point
   your scripts at it, and keep the old
   copy untouched for a week before
   deleting it.

At no point is the only copy of anything
in motion. If step 2 fails, nothing was
lost — the original never moved.

## Check it

Each recipe ends at a check written into
it: a listed snapshot, an opened copy, a
zero exit code, a restored file. If a
recipe's check fails, its chapter is the
next stop — the recipes assume the
chapters, and say so rather than repeat
them.
