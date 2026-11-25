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

### 2026-11-25 — `b-poh-statements.md` revised (version 1.5)

Attached files and rewritten links, from the services as they now run: the signing page signs files by fingerprint (§8.5), the
`attachment:` line's form and two reserved names (§3.1), one rule for files in every checker (§9.2), rewritten links unwrapped
locally (§9.1), and the limit on *never sent to a server* for a link a security service rewrote (§8.1, §10). The statement format
is unchanged. The version 1.4 proof was renamed to `b-poh-statements.md.r5.ots` and keeps the evidence of what version 1.4 said
(it carried its Bitcoin attestation since the 2026-11-22 upgrade); the new file was stamped by hand at the push (SHA-256
`cad0bb6603334c4954671b5caef184c94a51ca733a219b55c8056b47d58e7c16`). The RFC 3161 manifest rides the weekly run.

### 2026-11-19 — `b-poh-statements.md` revised (version 1.4)

§3.5 reserves `b-ticket/v1`, a seat holder's own record of a film's premiere, stating what it will do so its date is fixed; its
format will be defined when it is built. Nothing else in the specification changed. The version 1.3 proof was renamed to
`b-poh-statements.md.r4.ots` and keeps the evidence of what version 1.3 said; the new file was stamped by hand at the push
(SHA-256 `8074185729912f11ee4fffd874e0ea6b6f98fbb24f180407de19c40eec85bade`).

### 2026-11-14 (later) — `b-poh-statements.md` revised (version 1.3): corrections

An adversarial review before the paper found two things wrong, and both are corrected in place with the old wording named in §13
and in the text: §10 had said an agent "cannot press" (a software authenticator can, with nobody present), and §9.2 compared only
a mail's plain-text part (readers are shown the HTML part, which is now compared too). §10 and §11 also now state the checker's
limits and what the operator's servers see. The statement format is unchanged. The version 1.2 proof was renamed to
`b-poh-statements.md.r3.ots` (⚠️ it was a few hours old; whether it had its Bitcoin attestation when retired is recorded in the
commit message) and the new file was stamped by hand at the push (SHA-256 `e02eff95ff7b1e972f4705aa160de8982102409c65dc439c1d9e41fb31db05a3`).

### 2026-11-14 — `b-poh-statements.md` revised (version 1.2)

Anyone can now sign a mail in the browser (§8.5), so a B-Mail also arrives from ordinary mail apps: §8.4 says how such a mail
carries its signature, §9.1 finds the check link in the mail's text and never in quoted text, and §9.2 compares the body as
words. The statement format is unchanged. The version 1.1 proof was renamed to `b-poh-statements.md.r2.ots` and keeps the
evidence of what version 1.1 said; the new file was stamped by hand at the push (SHA-256 `99f686f5e5e9e9bd7e2ccf9ca12facd773d883b509e1d3f7186e64fa9224b2e9`).

### 2026-11-13 — `b-poh-statements.md` revised (version 1.1)

§3.4 reserves `b-agreement/v1`, an agreement pressed once by each of two parties, stating what it will do so its date is
fixed; its format will be defined when it is built. Nothing else in the specification changed. The version 1 proof was
renamed to `b-poh-statements.md.r1.ots` and keeps the evidence of what version 1 said; the new file was stamped by hand at
the push (SHA-256 `ac55370f79d60587036837115e3e1ccbe1e7991d5672be8bcb7787d42e30b151`).

### 2026-11-13 — `b-poh-statements.md` published (version 1, draft dated 2026-11-12)

First public version, written from the services as they ran on 2026-11-12: the `b-mail/v1` and `b-name/v1` statements, the
press, the record and the log, the opening and the proof, binding a passkey to an `@name`, the carriers, and the check.
`b-post/v1` is reserved: what it will do is stated, and its format will be defined when it is built. Stamped by hand at the
push.
