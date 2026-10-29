# Restoring — One File or Everything

You finish this chapter having restored a
whole snapshot into a new folder and
compared it, file by file, against the
original — and having pulled one single
file out of a snapshot without restoring
anything else. A restore you have
practised is the difference between a
backup and a hope, so this chapter is
deliberately hands-on.

## Browsing before you restore

Every snapshot is a tree you can walk
before committing to a restore. `kopia ls`
lists a snapshot's contents by its root
identifier (from `snapshot list`), and
paths extend the handle:

```bash
kopia ls k939275ac4b700b7dae6c7c84227cb139
kopia ls k939275ac4b700b7dae6c7c84227cb139/notes
```

```text
letters
notes
projects
```

To read one text file without restoring
anything at all, `kopia show` prints it:

```bash
kopia show k939275ac4b700b7dae6c7c84227cb139/projects/reading-list.txt
```

These are the lab's transcripts
(`kopia-lab/transcripts/t05-single-file.txt`);
your identifiers differ, the moves do
not. Browsing first is a habit worth
building: it confirms the snapshot you
are about to trust really holds the
version you want.

## Restoring a whole snapshot

Restore writes the snapshot's tree to a
folder you name. Restore to a **new**
folder, never over the original — if the
original is damaged, overwriting it with
a possibly-wrong snapshot destroys the
evidence and the patient at once:

```bash
kopia restore k939275ac4b700b7dae6c7c84227cb139 /home/you/restored-documents
```

```text
Restored 6 files, 4 directories and 0 symbolic links (269.3 KB).
```

## Check it: the comparison

The lab's test of its restore was a
recursive diff against the source tree,
and it is the check this chapter exists
to teach:

```bash
diff -r /home/you/sample/documents /home/you/restored-documents && echo IDENTICAL
```

It printed `IDENTICAL` (transcript
`kopia-lab/transcripts/t04-restore-and-diff.txt`).
Then the lab restored the **first**
snapshot beside it and diffed again: the
only differences were exactly the two
changes made after that snapshot — the
letter's postscript line, and the copied
file that did not exist yet. That second
diff is the deeper proof: snapshots are
not one blur of "the backup", they are
separate moments, each restorable as it
was.

## Restoring a single file

The path form of the handle works for one
file too. The target is the file path to
write:

```bash
kopia restore \
  k939275ac4b700b7dae6c7c84227cb139/notes/shopping-and-ideas.txt \
  /home/you/recovered/shopping-and-ideas.txt
```

```text
Restored 1 files, 0 directories and 0 symbolic links (164 B).
```

The lab then read the file: the notes,
including the marker sentence planted for
chapter 1's encryption test, came back
whole (transcript t05). Note what the
snapshot path is — root handle, then the
path the file had *inside* the snapshot.
`kopia ls` is how you find exact paths
when memory fails.

There is also an incremental mode,
`kopia snapshot restore`, which can
restore into a folder that already holds
an earlier restore and fetch only what
changed; the plain `restore` above is the
one to learn first.

## Mounting a snapshot

On a machine with FUSE (most desktop
Linux installs), a snapshot can be
mounted as a read-only folder and browsed
with ordinary tools:

```bash
kopia mount k939275ac4b700b7dae6c7c84227cb139 /home/you/mnt
```

**This guide's lab could not run this.**
Its machine has no FUSE device, and the
attempt is kept in transcript
`kopia-lab/transcripts/t06-mount-attempt.txt`
exactly as it failed: `fuse device not
found`. The command above is taught from
the project's documentation, and
`criteria.md` lists mounting as
documented-only. On a normal desktop it
is the pleasantest way to rummage in an
old snapshot; `kopia ls` and `restore`
above remain the ways that work
everywhere, including here.
