# B-PoH Signed Statements

The open specification of the signed-statement layer of B-PoH℠, the proof-of-personhood protocol described in
[*B-PoH as Humanity Layer for the AI-Native Internet*](https://thonly.org/publications/defensive-publications/b-poh-humanity-layer-ai-native-internet).

A statement is a short, exact text (an email, a post, or a line binding a passkey to an `@name`) that one person signs with
one fresh passkey press. It travels over ordinary mail and the web, unchanged, and anyone who receives it can check it
themselves against a public, append-only log. An agent may draft the text; only the person's press signs it.

**The specification:** [`b-poh-statements.md`](./b-poh-statements.md). Read its first box before anything else: what the
dedication covers and what it does not, and what a checked statement proves (a press, never a person).

## Where it runs

| | |
|---|---|
| the press | `sign.333.eco` |
| the public log | `log.333.eco/origin` (C2SP tiles and signed checkpoints) |
| the check, in your browser | `explorer.333.eco/b-mail` |
| the passkeys an `@name` shows | `333.eco/api/v1/names/{name}` |
| the reference verifier | [`@333eco/origin-verify`](https://www.npmjs.com/package/@333eco/origin-verify) (CC0) |

## Timestamps

Every version of the specification is timestamped three independent ways: GitHub's record of each push, an OpenTimestamps
proof that anchors the file's SHA-256 in the Bitcoin blockchain, and a weekly manifest of every file signed by three
independent RFC 3161 timestamp authorities. How to check them: [`TIMESTAMPS.md`](./TIMESTAMPS.md).

## License

The text is dedicated to the public domain under [CC0 1.0 Universal](./LICENSE). The dedication covers the text only. It
licenses no name or mark (HeartBank® and the others stay their owners'), and it grants no patent right to or from anyone.

By Thon Ly, with Miss Aquarius, an AI collaborator disclosed by name.
