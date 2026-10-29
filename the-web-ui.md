# The Web UI — KopiaUI and Server Mode

You finish this chapter knowing what the
web interface is, having seen the server
started and its page fetched and checked,
and knowing which parts of it this guide
verified and which you will meet first in
your own browser.

## Why a web page for a backup tool

Everything so far was typed. Kopia also
has a server mode: the same program
serves a web interface — the same
interface the **KopiaUI** desktop
application shows — from which you can
browse snapshots, set policies, start
restores, and read the state of the
repository, in a browser. One machine can
also act as a backup server for others:
clients connect to it over HTTPS, and it
takes their snapshots on a schedule.
That multi-machine arrangement is the
project's documented design; this guide's
lab ran the server on one machine, for
itself, which is the shape most home
setups want.

## Starting the server

Start it while connected to a repository:

```bash
kopia server start \
  --address=127.0.0.1:51515 \
  --insecure \
  --server-username=labuser \
  --server-password=a-server-password-of-yours
```

Two flags carry warnings. `--insecure`
serves plain HTTP; it exists for exactly
this — a first look, on your own machine,
at an address only your machine can
reach (`127.0.0.1`). For anything beyond
that, the server should use TLS, which it
sets up with its own certificate options;
the documentation walks it through.
`--server-password` sets the password for
the web login, which is a *different*
password from the repository password:
the repository password unlocks the data,
the server password unlocks the controls.

The server's opening lines (transcript
`kopia-lab/transcripts/t11-server-mode.txt`):

```text
Server will allow connections from users whose accounts are stored in the repository.
User accounts can be added using 'kopia server user add'.

SERVER ADDRESS: http://127.0.0.1:51515
Open the address above in a web browser to use the UI.
```

## What the lab verified

The lab had no browser to click with, so
it checked the server by measurement,
with `curl`:

- Asked for the page **without** logging
  in, the server answered **401** —
  authentication required, nothing shown.
  A backup console that opens to
  strangers would be a finding; this one
  does not.
- Asked **with** the server username and
  password, it answered **200** and
  served the interface page, whose title
  is `KopiaUI v0.23.1`.
- The programmatic API underneath
  (`/api/v1/...`) refuses plain scripted
  calls with `Invalid or missing CSRF
  token` — it is guarded against forged
  requests, and is meant to be driven by
  the interface or the `kopia` client,
  not by bare `curl`.

Clicking through the interface itself —
taking a snapshot from the page, browsing
one visually, setting a policy with the
mouse — is **documented-only** in this
guide and labelled so in `criteria.md`.
The operations are the ones the previous
chapters taught by command; the page is
another face on the same engine, not a
different tool.

## KopiaUI, the desktop form

KopiaUI packages the server and the
interface together as a desktop
application for Windows, macOS and Linux:
install it, and it maintains a repository
connection, runs scheduled snapshots in
the background, and shows the web
interface in its own window. For a single
personal machine whose owner prefers a
window to a terminal, it is the natural
form of Kopia. This guide teaches the
command line because the commands are the
same underneath both faces — and because
a command can be checked, scripted and
kept in a transcript, as these chapters
have done. KopiaUI itself was not run by
the lab and is documented-only here.

## Stopping, and server users

The server runs until stopped (Ctrl-C in
its terminal, or your service manager).
Accounts for the web login are managed
with `kopia server user add` and stored
in the repository itself — which is how
one server can serve several people, each
seeing their own sources. Building that
multi-user server is beyond this guide's
scope; the single-user start above is
enough to use the interface for your own
backups.

## Check it

With the server running, one command
repeats the lab's core check from any
terminal:

```bash
curl -s -u labuser -o /dev/null -w "%{http_code}\n" http://127.0.0.1:51515/
```

(`curl` will ask for the password.) A
`200` here, and a `401` without the
login, are the pair of numbers that say
the console is up and closed to everyone
else (transcript t11).
