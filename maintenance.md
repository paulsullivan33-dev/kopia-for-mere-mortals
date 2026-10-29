# Maintenance — Keeping the Repository Healthy

You finish this chapter able to explain
why a Kopia repository does not slowly
bloat and rot, to read the maintenance
schedule, and to run the two checks that
prove a repository is sound: verifying
its contents, and validating the storage
underneath it.

## The problem maintenance solves

Snapshots expire (chapter 6). When the
last snapshot pointing at a content goes,
the content is waste — but deleting data
from a shared store the moment it looks
unused is how backups get corrupted by a
race. Indexes also accumulate as small
files, and logs pile up. Somebody has to
do the housekeeping: collect the garbage,
pack the small indexes into fewer bigger
ones, trim the logs — carefully, and on a
schedule. In Kopia that somebody is
**maintenance**, and it is automatic.

## What actually runs

One connected owner — by default the
machine and user that created the
repository — runs maintenance for it:
**quick** maintenance every hour and
**full** maintenance every 24 hours, when
that owner's Kopia connects. The lab saw
the full cycle run at the end of its
first snapshot (transcript
`kopia-lab/transcripts/t02-first-snapshot.txt`):
its lines name the jobs — garbage
collection counting in-use and unused
contents, compaction of the indexes,
cleaning of logs — and end with
`Finished full maintenance.`

Ask about the arrangement any time:

```bash
kopia maintenance info
```

The lab's answer (transcript
`kopia-lab/transcripts/t08-verify-and-maintenance.txt`)
shows the owner, the quick cycle due in
an hour, the full cycle due in a day, and
a history of recent runs, each marked
`SUCCESS` with a one-line account of what
it did. A repository whose maintenance
history is empty and stale is a
repository nobody's Kopia is tending —
usually because the creating machine no
longer connects to it. `kopia maintenance
set --owner=...` hands the duty over;
`kopia maintenance run` runs a cycle now.

The safety detail worth knowing: garbage
collection works in two passes. Unused
contents are first *marked*, and only
deleted after a waiting period (the
snapshot run reported contents "too
recent to delete" being kept). A snapshot
taken at an awkward moment cannot have
its pieces swept from under it.

## Verifying the contents

Maintenance keeps a repository tidy; it
does not by itself prove the data still
reads. For that, ask Kopia to verify:
walk the snapshots, and check that every
content they name is present and intact:

```bash
kopia snapshot verify
```

The lab ran it against its source
(transcript t08):

```text
Listed 17 blobs.
Finished processing 13 objects (235.1 KB). Read 0 files (0 B).
```

and the command's exit code was 0 — the
success signal a script should test. Run
with more depth (`--verify-files-percent`)
it also downloads and re-checks file
contents themselves. A verify that fails
is the early warning the whole system
exists to give you: it names the snapshot
and content, while other copies (chapter
5's synced twin) may still be whole.

## Validating the storage

A repository can be sound while the disk
or service under it quietly misbehaves —
truncating writes, losing listings.
Kopia's own answer is a provider test:

```bash
kopia repository validate-provider
```

It writes, lists, reads back and deletes
test blobs through the same storage
connection your repository uses, checking
each behaviour, then reports `All good.`
— the lab's verdict, exit code 0
(transcript
`kopia-lab/transcripts/t12-validate-provider.txt`).
Running it once when you place a
repository on new storage — a new
external drive, a new cloud bucket — is
the cheapest insurance in this guide.

## Check it

The chapter's state in two lines:

```bash
kopia maintenance info | head -8
kopia snapshot verify; echo "exit code: $?"
```

An owner named, both cycles scheduled,
recent runs marked `SUCCESS`, and a
verify exit code of 0: that is a healthy
repository, stated in its own words.
