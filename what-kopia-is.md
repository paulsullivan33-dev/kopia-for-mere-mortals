# What Kopia Is

You have a folder that matters — letters,
notes, the documents of a project — and
you want a copy of it that survives the
original being deleted, corrupted or
encrypted by ransomware. The familiar way
to get that copy is to drag the folder
somewhere else. It works, until you do it
every week and the copies pile up, each
one full-size, none of them protected
from anyone who can open the folder they
sit in.

**Kopia is a backup program that stores
smarter and seals everything.** You point
it at a folder and it takes a
**snapshot**: a record of every file in
that folder at that moment. The snapshot
does not sit in the storage as files.
Before anything is stored, Kopia cuts
each file into pieces, works out a
fingerprint for each piece, and encrypts
the piece with a key derived from your
repository password. Only then do the
pieces — called **contents**, packed into
**blobs** — travel to the **repository**,
which is wherever you keep them: a folder
on an external disk, a network share, or
a bucket in a cloud storage service.

Two consequences follow, and they are the
reason the tool exists.

The first is **deduplication**. If a
piece's fingerprint is already in the
repository, it is not stored again — no
matter whether the repeat is inside one
big file, in a second copy of that file,
or in next week's snapshot of the same
folder. Chapter 6 measures this: the
lab's second snapshot described 269.3 KB
of files, and the repository grew by
24 KB. Keeping a snapshot a day stops
meaning a folder's weight a day.

The second is that **the repository is
unreadable without the password** — not
as a courtesy setting, but as arithmetic.
The lab searched its repository for a
sentence it had deliberately planted in a
notes file and found nothing; it searched
for a file *name* and found nothing; it
connected with the wrong password and was
refused with `invalid repository
password`. That is what makes it safe to
put a repository somewhere you do not
fully control, such as rented cloud
storage. Chapter 7 covers the restore
side, and chapter 5 the storage side.

## The password rule

This belongs early, before any procedure
depends on it: **the repository password
is the only key, and there is no way
back if you lose it.** No company holds a
copy. There is no recovery email. This is
the same property that keeps a stranger
out, applied to a forgetful owner. So a
real password for a real repository
belongs in a password manager or on paper
somewhere safe — never only in your head,
and never only in a file inside the very
folder being backed up. The password in
this guide, `correct-horse-lab-staple`,
is a throwaway printed on purpose for a
lab repository holding sample documents.
Yours must not be.

## A first taste

After installing (chapter 3), the shape
of the work is three lines:

```bash
kopia repository create filesystem --path /home/you/backups/kopia-repo
kopia snapshot create /home/you/Documents
kopia snapshot list
```

Create the place, take the snapshot, see
it listed. Everything else in this guide
— policies, restores, the web interface,
maintenance — is refinement of those
three moves.

## Check it

If you have followed the taste above, one
command shows the state of play:

```bash
kopia repository status
```

It names the repository you are connected
to, where it lives, and the encryption it
uses — `AES256-GCM-HMAC-SHA256` in the
lab's run (transcript
`kopia-lab/transcripts/t01-repository-create.txt`).
If it answers instead that the repository
is not connected, chapter 4 shows the
create and connect steps in full.
