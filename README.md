# Kopia for Mere Mortals

A backup is only a backup if you can get
your files back — and if nobody else can
read them while they sit in storage.
**Kopia** is a backup program built
around those two tests. You point it at
a folder; it cuts your files into pieces,
**encrypts** every piece with a password
only you know, and stores the pieces in a
**repository** — a folder on a disk you
own, or space in a cloud storage service.
Pieces that appear more than once are
stored only once, which is called
**deduplication**, so keeping many
backups costs far less space than keeping
many copies.

This guide builds the whole thing for
real. The lab behind it downloaded the
official release — **Kopia 0.23.1** —
made a repository on a local disk, and
backed up a small tree of sample
documents: letters, notes and project
files, 234.9 KB in all. Then it changed
one file, copied another one whole, and
backed up again: the tree had grown to
269.3 KB of files, yet the repository
grew by only 24 KB, because the repeated
pieces were already stored. It restored
a whole snapshot into a new folder and
compared it against the original file by
file — identical. It restored one single
file out of the middle of a snapshot. And
it tried, hard, to read the backup
without Kopia: searching the repository
for a sentence known to be inside the
notes found nothing, not even a file
*name* is readable, and connecting with
the wrong password was refused outright.

What the lab could not do, it did not
fake. The snapshot **mount** command
needs a FUSE device the lab machine does
not have, so mounting is taught from the
documentation with the lab's failed
attempt kept in the transcripts. The
cloud storage backends — S3 and its
relatives — are likewise described, not
run; everything in this guide that was
exercised used a repository on a local
disk, and the same commands reach the
cloud ones. The web interface was
started and fetched headlessly: the
login page is real, the clicking through
it is yours to do. `criteria.md` keeps
the full ledger of what was run and what
was only read.

One warning belongs on the first page,
because everything else depends on it:
**the repository password is the only
key there is.** Kopia has no recovery
service, no reset link, no back door —
by design. Lose the password and the
backups are sealed forever, to you as
much as to anyone else. This guide uses
a throwaway lab password, printed in the
open on purpose, and says plainly where
yours should live instead.

## What this guide covers

- [**What Kopia is**](what-kopia-is.md) —
  the mental model: snapshots in,
  restored files out, encryption and
  deduplication doing the quiet work,
  and the password rule stated early
- [**The moving parts**](the-moving-parts.md) —
  the program, the repository, contents
  and blobs, snapshots and policies,
  the cache, and which part holds what
- [**Installing Kopia**](installing-kopia.md) —
  the official release download, what is
  inside it, and the version check that
  proves it runs
- [**Your first backup**](your-first-backup.md) —
  creating a repository, connecting to
  it, and taking a first snapshot you
  can list and measure
- [**Repositories — where they can live**](repositories-where-they-live.md) —
  local disks, network storage and the
  cloud backends, and how to keep a
  second copy with `sync-to`
- [**Snapshots and policies**](snapshots-and-policies.md) —
  how snapshots accumulate, why the
  second one is cheap, and setting the
  retention policy that decides what is
  kept
- [**Restoring — one file or everything**](restoring.md) —
  browsing a snapshot, restoring a whole
  tree and comparing it, pulling out a
  single file, and mounting
- [**The web UI — KopiaUI and server mode**](the-web-ui.md) —
  the same engine behind a web page:
  running `kopia server`, what the lab
  verified about it, and the desktop app
- [**Maintenance — keeping the repository healthy**](maintenance.md) —
  the automatic housekeeping, verifying
  a repository reads back correctly, and
  checking the storage itself
- [**Recipes**](recipes.md) — a nightly
  backup script, a second copy on
  another disk, checking a backup
  without restoring it, and moving a
  repository to a new home
- [**The neighbours**](the-neighbours.md) —
  Restic, the Backups guide's plain
  copies, BorgBackup and the sync tools:
  what each does that Kopia doesn't, and
  vice versa
- [**Limits**](limits.md) — the password
  with no recovery, one writer's
  maintenance, what deduplication cannot
  shrink, and the rest of the honest
  list
- [**Troubleshooting**](troubleshooting.md) —
  the wrong-password refusal, the
  repository that is not connected, the
  sync destination that must exist
  first, the mount that needs FUSE, and
  the traps this guide's lab fell into
- [**Glossary**](glossary.md) — the
  terms, collected in one place

## What it doesn't cover

Backing up a whole running system —
open databases, virtual machine images
and full-disk restore are their own
craft; this guide protects folders of
files, which is what Kopia is for.
Running a shared backup server for a
whole office, with accounts for many
people, is pointed at in the web UI
chapter but not built. And no backup
guide can cover the disaster itself:
the repository chapter explains why a
second copy in a second place matters,
and leaves the planning of your own
arrangement to you.

## How to use this guide

Read chapter 1 for the model and
chapter 2 before installing anything —
knowing which part holds what turns
later confusion into a named problem.
Chapters 3 and 4 get you a program, a
repository and a first snapshot in one
sitting. Chapter 7 is the one to read
slowly even if you never expect to need
it: a restore you have never practised
is a hope, not a backup. The lab's
transcripts live in `kopia-lab/transcripts/`
and are the evidence behind every number
in these chapters.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Restic for Mere Mortals](https://paulsullivan33-dev.github.io/restic-for-mere-mortals/)
- [Backups for Mere Mortals](https://paulsullivan33-dev.github.io/backups-for-mere-mortals/)
- [Syncthing for Mere Mortals](https://paulsullivan33-dev.github.io/syncthing-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/kopia-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/kopia-for-mere-mortals/blob/main/LICENSE-CODE).
