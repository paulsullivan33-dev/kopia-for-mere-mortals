# Your First Backup

You finish this chapter with a repository
created, a snapshot of a real folder
inside it, and the listing that proves
both. This is the whole tool in miniature;
every later chapter refines one of
these steps.

## Before you start: a folder to protect

The lab backed up a small sample tree it
made for the purpose — a `documents`
folder with `letters`, `notes` and
`projects` subfolders, five files, 234.9
KB in all. Build your own equivalent
anywhere; the commands below use the
lab's shape. Substitute your real folder
when you repeat this for keeps:

```bash
mkdir -p /home/you/sample/documents/notes
echo "A first file worth keeping." > /home/you/sample/documents/notes/first-note.txt
```

## Step 1: create the repository

A repository starts empty, in a folder
that should be somewhere *other* than
inside the data it protects — a second
disk is the honest version of this, and
chapter 5 talks about placement. The lab
used a folder set aside for the purpose:

```bash
export KOPIA_PASSWORD='correct-horse-lab-staple'
kopia repository create filesystem --path /home/you/backups/kopia-repo
```

The reply is the repository's birth
certificate, and the lab kept it
(transcript
`kopia-lab/transcripts/t01-repository-create.txt`):

```text
Initializing repository with:
  block hash:          BLAKE2B-256-128
  encryption:          AES256-GCM-HMAC-SHA256
  key derivation:      scrypt-65536-8-1
  splitter:            DYNAMIC-4M-BUZHASH
Connected to repository.
```

Four facts worth reading once. The
**block hash** fingerprints each piece —
it is how deduplication recognises a
repeat. The **encryption** line names the
cipher that seals every piece. The
**key derivation** line is how your
password is stretched into the key —
deliberately slow, so guessing passwords
is expensive. The **splitter** is how
files are cut into pieces: `DYNAMIC`
means the cut points follow the data, so
inserting a paragraph near the start of a
file does not change every piece after
it.

Creating also **connects** you — the last
line says so — and prints the default
retention policy and a note that
maintenance will run automatically
(chapter 9 explains both).

## Step 2: take the snapshot

```bash
kopia snapshot create /home/you/sample/documents
```

```text
Snapshotting root@machine:/home/you/sample/documents ...
Created snapshot with root k774ee3ab55fb879bce7f34bb3c854730 and ID 3e67e762dca844079167f1c9dfa544f3 in 0s
```

(Your machine name, root identifier and
ID will differ; the shape will not.) The
`root k...` value is the snapshot's
handle — chapter 7 restores by it. The
first snapshot also triggers a full
maintenance run; its housekeeping lines
are normal and are explained in chapter 9.

## Step 3: look at what you have

```bash
kopia snapshot list
```

```text
root@machine:/home/you/sample/documents
  2026-10-29 18:03:04 UTC k774ee3ab55fb879bce7f34bb3c854730 234.9 KB drwxrwx--- files:5 dirs:4 (latest-1,hourly-1,daily-1,weekly-1,monthly-1,annual-1)
```

One line per snapshot: when it was taken,
its root handle, the size of the tree it
describes, the file and folder counts,
and the retention labels the policy has
attached to it. Those labels are chapter
6's subject; for now, they are proof the
policy machinery noticed your snapshot.

## Check it

Measure the repository itself:

```bash
du -sh /home/you/backups/kopia-repo
kopia content stats
```

The lab's repository measured 284 KB on
disk for a 234.9 KB tree (transcript t02).
The repository being slightly *larger*
than one small backup is expected at this
scale: the format file, indexes and logs
are fixed costs, and deduplication has
had nothing repeated to save yet. The
saving shows up on the second snapshot —
which is where chapter 6 begins.
