# The Moving Parts

You finish this chapter able to name the
six parts of a Kopia setup and say which
one holds what — so when something later
behaves oddly, you can point at the part
responsible instead of at "the backup".

## The program

`kopia` is a single program. Everything
in this guide — creating repositories,
taking snapshots, restoring, the web
server of chapter 8 — is that one program
run with different words after its name.
There is also a desktop application,
KopiaUI, which is the same engine wearing
a graphical face (chapter 8).

## The repository

The **repository** is the storage: the
place the sealed pieces live. It is not a
copy of your folders. A filesystem
repository, the kind this guide builds,
is a directory holding files with
scrambled names — the lab's looked like
this:

```text
repo/kopia.repository.f
repo/q/ebc/0e572743cf028d510ec2f3869dc9f-s4202d...
repo/x/n0_/be646f5b48929c164dee3d3d6c6ff15f-s4202d...
```

The small format file, `kopia.repository.f`,
describes how the repository was made —
its hash and encryption settings. The
rest are **blobs**: the packed, encrypted
data itself. Nothing in those names is
yours; your file names are inside the
encryption. A repository can live on
several kinds of storage (chapter 5); the
program treats them all the same, because
a blob is a blob wherever it rests.

Creating a repository and **connecting**
to one are different acts. Creating makes
a new, empty repository. Connecting
points your machine at an existing one
and, after the password checks out,
records a small config file — the
lab's was
`~/.config/kopia/repository.config` — so
later commands know where to go. The
distinction matters: `repository create`
on a path that already holds a repository
is not how you open it; `repository
connect` is (chapter 13 has the error
this confusion earns).

## Contents and blobs

Inside the repository, your data exists
as **contents**: the addressed pieces of
your files. Each content is identified by
a hash of what it holds — that is the
fingerprint deduplication compares, and
why a repeated piece is recognised
wherever it turns up. Contents are
encrypted, then packed together into
**blobs**, the files actually written to
storage, up to about 21 MB each in the
lab's repository. When you read numbers
like the lab's `content stats` output —
11 contents after the first snapshot, 16
after the second — you are looking at
this layer.

## Snapshots

A **snapshot** is the record of one
backup run: which folder, on which
machine, at what time, and the list of
contents that rebuild it. A snapshot is
small — it points at contents rather than
holding them — which is why snapshots
whose files barely changed cost almost
nothing. Each snapshot has an identifier
for its root, starting with `k`, like
`k774ee3ab55fb879bce7f34bb3c854730`. That
identifier is the handle every restore
command takes (chapter 7).

## Policies

A **policy** is the standing instruction
attached to a folder (or to the whole
repository): how many snapshots to keep
by age — so many hourly, daily, weekly,
monthly, annual — which files to skip,
whether to compress, and more. Policies
are inherited: set one on a folder and it
overrides the repository-wide one for
that folder only. Chapter 6 sets one and
reads it back.

## The cache

Reading a repository means decrypting its
indexes, so Kopia keeps a local **cache**
of decrypted working data on the machine
doing the work. The cache is a speed
device, not a second backup: it can be
deleted and will be rebuilt. It is also
the one place decrypted material rests on
disk, which is worth knowing on a shared
machine — the lab pointed its cache at a
lab directory with
`KOPIA_CACHE_DIRECTORY` so everything the
lab touched stayed in one place.

## Check it

With a repository connected, ask for the
contents layer directly:

```bash
kopia content stats
```

The lab's first-snapshot answer began
`Count: 11` and `Total Bytes: 238.1 KB`
(transcript
`kopia-lab/transcripts/t02-first-snapshot.txt`) —
eleven pieces holding a 234.9 KB tree.
If your count surprises you, you now know
which part to ask about: contents, not
files.
