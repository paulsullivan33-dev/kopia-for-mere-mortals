# Troubleshooting

The failures this guide's lab actually
met, plus the ones its commands make
likely — each with the message it
produces, what it means, and the fix.
Every message quoted here appears in the
transcripts in `kopia-lab/transcripts/`.

## "invalid repository password"

```text
failed to open repository: unable to create format manager: invalid repository password
```

You connected with the wrong password
(transcript t09, where the lab did it on
purpose). There is no other cause and no
other fix: supply the right password.
If the *right* password earns this
message, one of two things is true — the
password in your manager is not the one
the repository was created with, or you
are pointing at a different repository
than you think. `kopia repository status`
after a successful connect settles the
second; nothing settles the first except
finding the password. Chapter 12 exists
because of this message.

## "repository is not connected"

```text
open repository: repository is not connected.
```

A snapshot, restore or list ran with no
connection in force — a fresh machine, a
new shell without the config, or after
`repository disconnect`. Connect first
(chapter 5), or run the command with
`--config-file` pointing at the config
that holds the connection, as chapter 5's
two-repository pattern does. The lab met
this while connecting to its synced copy
before the copy existed (transcript t10).

## "no such file or directory" from sync-to

```text
can't connect to storage: cannot access storage path: stat .../kopia-repo-copy: no such file or directory
```

`sync-to` — and the cloud equivalents —
expects the destination to exist.
`mkdir -p` the destination folder first,
then sync (transcript t10). The same
shape of error appears when a
`repository connect filesystem` path is
mistyped: Kopia reports the storage it
cannot reach, not the spelling you got
wrong, so read the path in the message
against the one you intended.

## The mount that cannot mount

```text
fuse device not found, try 'modprobe fuse' first
mount error: mounting error: fusermount exited with code 256
```

Mounting needs FUSE, and this machine
does not have it (transcript t06). On a
machine you administer, installing the
FUSE package or loading the module fixes
it. Where you cannot, nothing is lost:
`kopia ls`, `kopia show` and `kopia
restore` (chapter 7) do everything
mounting does, in more steps. Do not
treat a failed mount as a failed backup —
the snapshot it pointed at was fine.

## The server answers 401, or refuses the API

A `401` from the web address means the
server is up and asking for the login —
use the `--server-username` and
`--server-password` given at start, and
remember these are not the repository
password (chapter 8). A scripted call to
`/api/v1/...` answered with `Invalid or
missing CSRF token` is the server being
correct: the API is not open to bare
scripts. Drive the repository with the
`kopia` commands, or through the web page
as a person (transcript t11).

## The check command says the repository grew

It did, and usually by less than the new
snapshot's nominal size — chapter 6's
arithmetic. Before suspecting a fault,
compare like with like: `kopia snapshot
list` sizes describe the *tree*; `du`
measures the *store*, which also carries
indexes, logs and not-yet-collected
garbage. If the store grows by the full
tree size every snapshot, the usual cause
is data that cannot deduplicate —
archives re-created whole each day, or a
folder of videos — which chapter 12
names as a floor, not a bug.

## Snapshot taken, but the file is stale in it

A snapshot is a moment. If a file changed
while Kopia read it, the snapshot holds
the version it read; if the file changed
just after, the snapshot is simply older
than you think. Check with `kopia ls` and
the snapshot's timestamp in `snapshot
list`, and take a fresh snapshot rather
than debugging an old one. For files that
must be consistent — database dumps,
exports — produce the file first, then
snapshot the folder, in that order.
