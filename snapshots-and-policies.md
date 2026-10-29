# Snapshots and Policies

You finish this chapter understanding why
a second snapshot costs a fraction of the
first, and able to set the **retention
policy** — the rule that decides how many
snapshots are kept, and for how long —
and read it back to confirm it took.

## Why the second snapshot is cheap

A snapshot does not hold your files. It
holds a list of contents — the sealed
pieces — and pieces already in the
repository are not written again. The lab
measured this directly. After the first
snapshot (234.9 KB of documents, 284 KB
of repository), it made two changes:

- appended one line, a postscript, to the
  letter;
- copied a 37 KB file of repeated lines
  to a second name, byte for byte
  identical.

Then it backed up again (transcript
`kopia-lab/transcripts/t03-second-snapshot-dedup.txt`).
The new snapshot describes **269.3 KB** of
files. The repository grew from 284 KB to
**308 KB**. The identical copy cost
nothing at all — its pieces were already
stored. The edited letter cost only its
changed pieces, because the splitter
(chapter 4) cuts where the data tells it
to, so one appended line disturbs little.
Directory records and indexes make up
most of the rest of the 24 KB.

This is the arithmetic that changes
habits: a snapshot you take daily does
not cost a folder a day. It costs the
day's *changes*. Snapshot early,
snapshot often is affordable advice here.

One limit to the magic, named now and
again in chapter 12: deduplication and
compression only shrink data that
repeats or has patterns. A file of pure
random bytes — the lab included one, a
200 KB `random-bytes.bin` — stores at
full size, and so do already-compressed
photos, videos and archives. Kopia will
not make a media library small; it will
make its *history* cheap.

## Scheduling, briefly

Kopia itself has no clock. Snapshots
happen when something runs `snapshot
create`: you, a script, or your system's
scheduler — cron or a systemd timer on
Linux, Task Scheduler on Windows. The
recipes chapter carries a nightly script.
In server mode (chapter 8) the server can
run snapshots on a schedule per source,
which is one of server mode's main uses.

## Retention: the policy that forgets

Left alone, snapshots accumulate forever.
The **retention policy** is the standing
rule for thinning them: keep so many of
the latest, and one per hour, day, week,
month and year going back. The defaults
the lab's repository was created with:

```text
Latest snapshots:    10
Hourly snapshots:    48
Daily snapshots:      7
Weekly snapshots:     4
Monthly snapshots:   24
Annual snapshots:     3
```

Read it as a funnel. Recent history stays
dense — an hourly snapshot to undo this
morning's mistake. Older history thins —
a weekly snapshot reaches back a month, a
monthly one two years — so the cost of
"keep everything forever" becomes the
cost of a sparse, deepening archive.
When a snapshot is taken, Kopia labels
each existing one (`latest-1`, `daily-1`,
and so on, visible in `snapshot list`)
and expires the ones no rule claims.
Their now-unshared contents become
eligible for the garbage collection that
chapter 9 describes.

Policies attach at three levels — the
whole repository, a machine, or one
folder — and the nearest one wins. Read
the effective policy for a folder with:

```bash
kopia policy show /home/you/sample/documents
```

Each line says where its value came from:
`inherited from (global)` or
`defined for this target` (transcript
`kopia-lab/transcripts/t07-policy.txt`).

## Setting a policy

The lab lengthened its daily and weekly
keeping for the sample folder only:

```bash
kopia policy set --keep-daily=14 --keep-weekly=8 /home/you/sample/documents
```

```text
Setting policy for root@machine:/home/you/sample/documents
 - setting "number of weekly backups to keep" to 8.
 - setting "number of daily backups to keep" to 14.
```

The same command family controls the
rest: `--keep-latest`, `--keep-hourly`,
`--keep-monthly`, `--keep-annual`, file
ignore rules, and per-folder compression.
Two settings deserve a sentence each.
`--keep-annual=0`-style zeroing is how
you tell Kopia a folder's snapshots are
short-lived scratch. And **pinning** — a
snapshot can be pinned so retention never
expires it, for the one version you
intend to keep regardless.

## Check it

Set a value and read it back:

```bash
kopia policy show /home/you/sample/documents | grep -A7 Retention
```

The lab's after-state shows
`Daily snapshots: 14   (defined for this target)`
and `Weekly snapshots: 8   (defined for this target)`,
with the rest still inherited — proof the
policy is attached to the folder, not
merely typed at a prompt (transcript
t07). If your changed lines still say
`inherited`, the set was aimed at a
different target than the show; chapter
13 covers the mismatch.
