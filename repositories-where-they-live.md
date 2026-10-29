# Repositories — Where They Can Live

You finish this chapter able to choose
where a repository lives, to connect and
disconnect from one at will, and to keep
a second, synced copy of a repository in
a second place — because a backup that
shares a fate with the original is only
half a backup.

## The kinds of storage

A filesystem repository — a plain folder,
chapter 4's kind — is the simplest home,
and it is more capable than it sounds.
The folder can sit on:

- a second internal disk or an external
  USB drive,
- a network share mounted on your
  machine (from a NAS or another
  computer), which to Kopia is still
  just a folder.

Beyond folders, Kopia has built-in
connectors for the storage services:
Amazon S3 and S3-compatible stores,
Google Cloud Storage, Azure Blob
Storage, and — through servers you point
it at — WebDAV, SFTP, and anything
`rclone` can reach. Those backends are
**described here from the project's
documentation, not run by this guide's
lab**, which exercised the filesystem
backend only; `criteria.md` says so in
the ledger. The commands differ only at
`repository create` and `repository
connect` time:

```bash
kopia repository create s3 --bucket my-backup-bucket
kopia repository connect s3 --bucket my-backup-bucket
```

Everything after connecting — snapshots,
policies, restores — is identical, which
is the point of the design: storage is a
detail the rest of the tool never sees.

Wherever it lives, remember chapter 1's
arithmetic: the repository is sealed, so
a backend you do not fully trust is
acceptable for *reading*. What a backend
can still do is lose or withhold your
blobs — encryption protects secrecy, not
existence. That argues for the arrangement
below.

## Connecting and disconnecting

`repository create` connects you as a
side effect. To use an existing
repository — on a new machine, or after
disconnecting — connect explicitly:

```bash
kopia repository connect filesystem --path /home/you/backups/kopia-repo
```

It asks for the password (or reads
`KOPIA_PASSWORD`), checks it against the
repository's format, and writes the small
config file that later commands use.
`kopia repository disconnect` forgets the
connection; it deletes nothing in the
repository. `kopia repository status` is
the standing check — run it whenever you
are unsure which repository a command is
about to touch.

A machine holds one default connection.
To work with a second repository without
disturbing the first, give the commands a
separate config file, as the lab did
when it opened its synced copy:

```bash
kopia --config-file=/home/you/.config/kopia/copy.config \
  repository connect filesystem --path /home/you/backups/kopia-repo-copy
```

## The second copy: `sync-to`

Kopia can copy a repository, blob for
blob, to another repository — on another
disk, or in cloud storage. The blobs move
in their sealed form, so the copy needs
no decryption and learns nothing. The
command, run while connected to the
original:

```bash
mkdir -p /home/you/other-disk/kopia-repo-copy
kopia repository sync-to filesystem --path /home/you/other-disk/kopia-repo-copy
```

The lab's run (transcript
`kopia-lab/transcripts/t10-second-repository-sync.txt`)
made a 328 KB copy of its 324 KB
repository — the small difference is the
copy's own format and log files. Two
honest details from that run. First, the
destination folder must **exist before**
the command will use it; that is what the
`mkdir` is for, and forgetting it earns a
plain `no such file or directory` error
(chapter 13). Second, by default
`sync-to` never *deletes* a blob from the
destination, so a destination copy only
grows unless you pass `--delete` — a
deliberate safety, since a sync that can
delete can propagate a disaster.

## Check it

A copy is only proven by opening it. The
lab connected to its copy under the
second config file and listed snapshots:

```bash
kopia --config-file=/home/you/.config/kopia/copy.config snapshot list
```

Both snapshots were there, with the same
root identifiers as the original —
(transcript t10). Run the same check on
your own copy, with your second disk
unplugged-in spirit: if the copy opens
with your password and shows your
snapshots, you have two repositories. If
the original were destroyed tonight, the
restore chapter would work unchanged
against the copy.
