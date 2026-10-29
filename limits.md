# Limits

You finish this chapter able to state
what Kopia will not do, before one of
these edges surprises you in production.

## The password has no recovery

This is chapter 1's rule, restated where
limits belong. There is no reset, no
support desk, no escrow. The repository
format can *change* a password — you
supply the old one — but nothing can
bypass a forgotten one. Every mitigation
is social, not technical: a password
manager, a printed copy somewhere safe,
a second person who knows where it is.
A backup whose key is lost is not a
backup; it is a sealed box.

## It backs up files, not systems

Kopia snapshots folders of files. It does
not image a disk, does not capture a
running database consistently, and does
not promise a bare-metal restore of a
whole machine. Files open and changing
during a snapshot are read as they are
found. For documents, photos, code and
the ordinary contents of a home or a
project folder, that is the right tool;
for a live database, dump the database
first and snapshot the dump.

## Deduplication has a floor

Only repetition shrinks. Random data,
encrypted files, and already-compressed
media — JPEG photos, videos, zip files —
store at close to full size, as the
lab's 200 KB random file did. And the
first snapshot of anything costs slightly
*more* than the data: format, indexes and
logs are real bytes ( chapter 4's 284 KB
repository for a 234.9 KB tree). The
savings are in the history, and they are
large exactly when your data changes
slowly relative to its size.

## One owner does the housekeeping

Maintenance runs from one designated
owner (chapter 9). If that owner stops
connecting — a laptop retired, a server
rebuilt — the repository keeps taking
snapshots but stops being cleaned, and
slowly bloats until someone takes over
ownership. Several machines *can* snapshot
into one repository, and a Kopia server
(chapter 8) coordinates many clients,
but the tidy-up duty is deliberately
singular: two garbage collectors racing
is a worse failure than one absent one.

## Storage can still lose your blobs

Encryption defeats readers, not loss. A
backend that deletes, corrupts or
withholds blobs leaves holes that
`snapshot verify` will find but cannot
heal. The only answer is the chapter 5
arrangement — a second copy in a second
place — and even `sync-to` carries its
own limit: by default it never deletes,
so the copy is an ever-growing mirror
unless a human occasionally prunes it
deliberately.

## The interface edges

Mounting needs FUSE; the lab's machine
lacked it entirely, and some locked-down
systems will too. The web interface is a
faithful face on the engine, but a few
operations live only in the command line,
and the server's own reach is your
responsibility: run it on plain HTTP
beyond your own machine and you have
undone, at the door, the care the
repository took at rest. TLS is provided;
use it anywhere the network is not wholly
yours.

## Scale and speed, stated plainly

Kopia is used on repositories far larger
than this guide's lab, but this guide
measured a 269 KB tree on a small shared
machine, and makes no speed claims at
all. Initial snapshots of terabytes take
the time the storage takes; there is no
shortcut in the design, only the comfort
that later snapshots are cheap. Readers
with very large, very churny data should
test against their own storage before
committing — `repository
validate-provider` (chapter 9) plus one
timed snapshot of the biggest folder is
an afternoon's honest answer.
