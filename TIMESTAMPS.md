# Cryptographic timestamps

This repository exists so that each version of the B-PoH signed-statement specification can be shown to have existed when it
did. Three independent dates back that up:

1. **The push.** GitHub records when each commit reached this public repository.
2. **OpenTimestamps.** The specification carries a `<name>.md.ots` proof beside it, which commits the file's SHA-256 to the
   Bitcoin blockchain. Anyone can check it without trusting this repository, GitHub or the authors.
3. **RFC 3161.** A weekly manifest of every tracked file is signed by three independent trust authorities, one of them
   eIDAS-qualified. See [`timestamps/README.md`](./timestamps/README.md).

## One file, one stamp

`ots stamp` overwrites an existing proof, so a revised file first has its earlier proof renamed to `<file>.rN.ots` by hand,
keeping the evidence of what the earlier version said, and the revision is logged below. The weekly job adds a proof to any
document that has none and never touches an existing one. A new version is stamped by hand when it is pushed, not left to
the weekly job.

## What a proof does not show

A timestamp proves that this exact text existed no later than a given moment and has not changed since. It says nothing
about who wrote it, whether it is original, or whether it is right.

## Checking a proof

```sh
pip install opentimestamps-client
ots verify b-poh-statements.md.ots
./tsa-verify.sh b-poh-statements.md
```

A full check against Bitcoin uses a local node. Without one, the proof still names a specific block, whose Merkle root can
be compared with any public block explorer.

## Log

*Stamps, rotations and checks are recorded here, newest first.*

### 2026-11-13 — `b-poh-statements.md` published (version 1, draft dated 2026-11-12)

First public version, written from the services as they ran on 2026-11-12: the `b-mail/v1` and `b-name/v1` statements, the
press, the record and the log, the opening and the proof, binding a passkey to an `@name`, the carriers, and the check.
`b-post/v1` is reserved: what it will do is stated, and its format will be defined when it is built. Stamped by hand at the
push.
