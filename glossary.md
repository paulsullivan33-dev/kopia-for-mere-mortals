# Glossary

**Blob** — one of the files a repository
actually stores: a pack of encrypted
contents with a scrambled name. Blobs are
what storage holds and what `sync-to`
copies.

**Cache** — decrypted working data Kopia
keeps locally to make a repository fast
to read. A speed device only; deleting it
loses nothing and it is rebuilt on the
next run.

**Content** — one addressed piece of your
data inside the repository, identified by
a hash of what it holds. Files are cut
into contents; deduplication works at
this level.

**Deduplication** — storing each distinct
content once, however many files or
snapshots reference it. The reason a
second snapshot of a slightly changed
folder costs almost nothing.

**FUSE** — the mechanism that lets a
program present itself as a folder.
`kopia mount` needs it; machines without
it can still browse and restore snapshots
by command.

**Key derivation** — the deliberately
slow stretching of a password into an
encryption key (the lab's repository used
`scrypt-65536-8-1`), making password
guessing expensive.

**KopiaUI** — the desktop application
form of Kopia: the server and web
interface packaged together, running
scheduled snapshots in the background.

**Maintenance** — Kopia's automatic
housekeeping: garbage collection of
contents no snapshot references, packing
of indexes, trimming of logs. Runs hourly
(quick) and daily (full) from one owner.

**Policy** — the standing rules attached
to the repository, a machine, or a
folder: retention counts, ignores,
compression. The nearest setting wins.

**Repository** — the storage a backup
lives in: a folder, or cloud storage,
full of sealed blobs, plus a small format
file describing how it was made. Useless
without the password, by design.

**Retention** — the part of a policy that
decides how many snapshots are kept at
each age — so many latest, hourly, daily,
weekly, monthly, annual — and expires the
rest.

**Snapshot** — the record of one backup
run: a folder, a machine, a moment, and
the list of contents that rebuild it.
Identified by a root handle beginning
with `k`.

**Splitter** — the rule that decides
where files are cut into contents.
Kopia's is content-defined (`DYNAMIC`), so
edits disturb only the pieces near them.

**sync-to** — the command that copies a
repository, blob for sealed blob, to a
second location. Copies by default;
deletes only when explicitly told.
