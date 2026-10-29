# The Neighbours

Kopia works a crowded street. These are
characterisations from each project's own
documentation, not benchmarks; this
guide's lab ran Kopia only, and two of
the neighbours below have guides of their
own in this series, pointed to where a
reader wants the full treatment.

## Restic: the close sibling

Restic does the same job the same way —
encrypted, deduplicated snapshots into a
repository, with a command line of the
same shape (`init`, `backup`, `restore`,
`snapshots`). A reader choosing between
them is choosing at the margins. Kopia's
distinctives are its web interface and
desktop app as first-class parts of the
project, policies carried inside the
repository, its own server mode for
backing up several machines to one place,
and compression and maintenance designed
in from the start. Restic's are a longer
history, a wider set of storage backends
reached through one `rclone`-friendly
design, and a larger crowd of people who
already know it. Neither choice is a
trap: both seal before they store, both
deduplicate, both restore single files.
The series' [Restic for Mere Mortals](https://paulsullivan33-dev.github.io/restic-for-mere-mortals/)
teaches that tool with the same honesty
rules as this one.

## BorgBackup: the other veteran

Borg (and its fork Borgmatic for
automation) is the elder of the
deduplicated, encrypted backup tools,
with a devoted following, very fast
deduplication, and append-only modes that
suit untrusted backup servers. Its
repositories are traditionally reached
over SSH to a machine running Borg, where
Kopia speaks to storage directly,
including object storage. Readers with a
Unix backup server already in place often
land on Borg; readers putting backups in
a cloud bucket often land on Kopia.

## Plain copies: the Backups guide's way

The series' [Backups for Mere Mortals](https://paulsullivan33-dev.github.io/backups-for-mere-mortals/)
guide teaches the discipline underneath
all of these tools — what to copy, how
many copies, where they should live —
including plain copying tools such as
`rsync`, which leave your files as files:
readable by anything, restorable with no
special program, and protected by
nothing. Kopia is what you reach for when
the plain copy's openness becomes the
problem — when the second disk or the
cloud bucket should hold your data
without being able to *read* your data —
or when fifty full copies of a slowly
changing folder stop fitting. The plain
copy still wins on one axis forever: in
thirty years, a folder of files needs no
resurrection of any program to be read.

## Sync tools: not backups at all

Syncthing, Dropbox and their kin keep
folders *the same* across machines —
which includes making them the same in
the wrong direction, propagating a
deletion or a ransomware encryption
faithfully and at once. They are
wonderful at their job and are named here
only to draw the line chapter 1 implied:
a snapshot history that cannot be
rewritten by the present is what makes a
backup a backup. Sync plus Kopia, pointed
at the same folder for different jobs, is
a common and sensible arrangement.

## Duplicati and the appliance route

Duplicati offers encrypted, deduplicated
backups behind a web interface, driven
entirely from the browser, with a wide
backend list — closer to KopiaUI in
spirit, without the command-line depth
this guide used. Beyond it sit the
appliance answers: a NAS with its own
backup suite, or a drive maker's bundled
software — easy to start, harder to leave,
and worth reading the exit terms of
before the first terabyte. Kopia's exit
terms are this guide's chapter 5: sealed
blobs in storage you control, and an
open-source program to open them.
