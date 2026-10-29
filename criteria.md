# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   The lab's documents are stock sample documents written for
   the lab.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 12 names what the tool
   cannot do: a password with no recovery, files rather than
   whole systems, deduplication's floor on data that does not
   repeat, a single maintenance owner, storage that can still
   lose blobs, mounting that needs FUSE, and speed claims this
   guide does not make.
8. **The ethics rule is in the text, early.** Kopia's own line
   is drawn in chapter 1 and the README, before any procedure
   depends on it: a repository is sealed against everyone
   without the password — the owner included, if the password
   is lost — and that secrecy is stated plainly as the feature
   and the risk in one.
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

The build machine is a shared Ubuntu sandbox with no FUSE
device and no way to run containers. Kopia **0.23.1** (build
72ec08fd) was downloaded as the official release archive
`kopia-0.23.1-linux-x64.tar.gz` from the project's GitHub
releases and run unmodified. All lab state — home, cache,
repositories and the sample tree — lived in a lab directory
inside the workspace, never in the system temp area; the
sample repository itself is not shipped in this guide, only
its transcripts, which live in `kopia-lab/transcripts/`
(t00–t12). The lab password,
`correct-horse-lab-staple`, is a throwaway printed in the
guide on purpose; the sample documents it protected were
written for the lab.

Run for real, with transcripts as evidence:

- Version and the top-level help listing (t00).
- Repository creation on the filesystem backend, with the
  create output recorded in full — BLAKE2B-256-128 hashing,
  AES256-GCM-HMAC-SHA256 encryption, scrypt key derivation,
  DYNAMIC splitter — and `repository status` (t01).
- A first snapshot of the five-file sample tree (234.9 KB):
  snapshot listed with its root identifier, repository
  measured at 284 KB on disk, `content stats` counted 11
  contents (t02).
- Deduplication measured: after appending one line to one
  file and adding a byte-identical copy of a 37 KB file, the
  second snapshot describes 269.3 KB while the repository
  grows only 284 KB → 308 KB (t03).
- Restores: the second snapshot restored to a fresh folder
  and `diff -r` against the source — identical; the first
  snapshot restored and diffed — only the two post-snapshot
  changes differ (t04).
- Browsing and single-item recovery: `kopia ls` into a
  snapshot, one file restored by its snapshot path and read
  back whole, one file printed with `kopia show` (t05).
- Policy: defaults read, `--keep-daily=14 --keep-weekly=8`
  set on the sample folder, read back as `defined for this
  target` against the inherited rest (t07).
- Health: `snapshot verify` over the source, exit code 0;
  `maintenance info` showing the hourly quick and daily full
  cycles, the owner, and a history of successful runs; a full
  maintenance cycle also ran automatically after the first
  snapshot (t02, t08). `repository validate-provider` ran
  its write/list/read/delete checks against the filesystem
  storage and reported `All good.`, exit code 0 (t12).
- Encryption checked from outside: a planted marker sentence
  (`MARKER-QUUX-7391-the-velvet-ledger`) greps to nothing
  anywhere in the repository; a file name greps to nothing;
  the repository's files have scrambled names; connecting
  with a wrong password is refused with `invalid repository
  password` (t09).
- A second repository: `repository sync-to` copied the
  repository (324 KB → 328 KB), and the copy, connected under
  a separate config file, listed both snapshots with the
  same root identifiers. The destination-must-exist failure
  was met and kept in the same transcript (t10).
- Server mode: `kopia server start` ran on 127.0.0.1 with
  `--insecure`; the page answered 401 without a login and
  200 with the server credentials, serving a page titled
  `KopiaUI v0.23.1`; a bare API call was refused for a
  missing CSRF token (t11).

Documented only — described from the project's documentation,
not run, and the text does not claim otherwise:

- Mounting a snapshot: the lab machine has no FUSE device;
  the genuine failed attempt, `fuse device not found`, is
  transcript t06, and chapter 7 teaches the command as
  documented-only.
- The cloud and network storage backends (S3 and
  S3-compatible stores, Google Cloud Storage, Azure Blob,
  WebDAV, SFTP, rclone). Only the filesystem backend was
  exercised; the create/connect commands for S3 in chapter 5
  are given from the documentation.
- KopiaUI, the desktop application, and clicking through the
  web interface: the server was fetched headlessly only.
- A multi-machine backup server with per-user accounts;
  chapter 8 starts the single-user server only.
- Scheduling through cron or systemd: chapter 10's script
  and crontab line are assembled from commands each verified
  above, but no scheduler ran them in the lab.
- Repository sizes and speeds beyond the lab's 269 KB tree:
  the guide reports no throughput figures at all.

Fixtures are this guide's own and deliberately generic: a
letter to a friend about a garden, a notes file, a reading
list, a file of repeated pangram lines, and 200 KB of random
bytes — no names, organisations or real events beyond a
fictional gardener's signature. The random file exists to
prove the honest point of chapter 12: data that does not
repeat does not shrink.
