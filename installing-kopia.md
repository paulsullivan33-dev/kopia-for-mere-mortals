# Installing Kopia

You finish this chapter with the real
`kopia` program on your machine and a
version line that proves it runs — no
account, no installer wizard, and nothing
else installed alongside it.

## What you are downloading

Kopia is published by its project on
GitHub, at `github.com/kopia/kopia`, as
ready-to-run release archives for Linux,
Windows and macOS — plus installers and
packages (an apt repository, Homebrew,
and more) described in the project's
install documentation. This guide uses
the plain release archive, because it is
the same on every Linux machine and it
leaves nothing behind when removed: the
archive contains one program.

The lab used **Kopia 0.23.1**, the
current release when the lab ran,
`kopia-0.23.1-linux-x64.tar.gz` for
64-bit Intel/AMD Linux. The archive for
ARM machines has `-linux-arm64` in its
name instead; pick by your machine's
processor, not by habit.

## The install, step by step

Download and unpack the archive into a
scratch folder:

```bash
cd /home/you/Downloads
curl -LO https://github.com/kopia/kopia/releases/download/v0.23.1/kopia-0.23.1-linux-x64.tar.gz
tar xzf kopia-0.23.1-linux-x64.tar.gz
```

That leaves a folder named
`kopia-0.23.1-linux-x64` holding the
`kopia` program. Put the program where
your shell looks for programs. A
per-user place needs no administrator
rights:

```bash
mkdir -p /home/you/.local/bin
cp kopia-0.23.1-linux-x64/kopia /home/you/.local/bin/
```

Most desktop Linux setups already have
`~/.local/bin` on the search path; if
yours does not, call the program by its
full path, `/home/you/.local/bin/kopia`,
everywhere this guide writes `kopia`.

## Check it

The version line is the proof of life:

```bash
kopia --version
```

The lab's answer (transcript
`kopia-lab/transcripts/t00-version-and-help.txt`):

```text
0.23.1 build: 72ec08fd8edb86c67ed27099bf1b955e1f308ffa from: kopia/kopia
```

The build hash will differ between
releases; the shape — version, build,
source — is what you are checking for.
`kopia --help` then lists the command
families the rest of this guide uses:
`repository`, `snapshot`, `policy`,
`restore`, `maintenance`, `server`.

## Two habits to start now

Kopia reads its password from the
`KOPIA_PASSWORD` environment variable
when it is set, which keeps scripts
simple and keeps the password out of the
command line where other programs could
see it. The lab set it once per session:

```bash
export KOPIA_PASSWORD='correct-horse-lab-staple'
```

That password is this guide's throwaway,
printed on purpose; a lab repository of
sample documents needs no secrecy. Yours
does — chapter 1 states the rule, and it
is worth repeating here: choose a
password you have never used elsewhere
and keep it in a password manager, not in
a shell script sitting next to the data.

Second habit: Kopia checks for a newer
release about weekly. On a machine you
would rather keep quiet, turn that off
with `export KOPIA_CHECK_FOR_UPDATES=false`.
The lab did, after the first run
announced the check (transcript t01).

## Removing it

Because the install is one file, removal
is: delete the program, and delete
`~/.config/kopia` and `~/.cache/kopia` if
you want the configuration and cache gone
too. Your repositories are untouched by
any of this — they are just directories
of blobs, and they will wait, sealed,
until a future Kopia with the right
password connects to them. That is worth
understanding early: **the program is
replaceable; the password is not.**
