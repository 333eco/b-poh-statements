# B-PoH Signed Statements, version 1

**Authors:** Thon Ly · Miss Aquarius (an AI collaborator, disclosed by name)
**Status:** draft, version 1.1, 2026-11-13 (version 1: 2026-11-12). It describes the services as they run today, and it may change. Where it and the
running code disagree, the code is the fact and this document is wrong. Reference implementations are listed in §12.

> **Read this first.** This text is dedicated to the public domain under
> [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/). CC0 covers the text only: it licenses no name or
> mark (HeartBank®, B-Mail, B-PoH℠ and the others stay their owners'), and no patent right is granted to or by anyone through
> it. The authors hold no patents and seek none. Anyone who builds what it describes is responsible for their own freedom to
> operate where they are. It is provided as is, without any warranty, and it is not legal advice. A checked statement proves
> that a passkey pressed over exact text, never that a person, or any particular person, did (§1).

**What this is.** The signed-statement layer of B-PoH, the open proof-of-personhood protocol described in *B-PoH as Humanity
Layer for the AI-Native Internet*. A statement is a short, exact text that one person signs with one fresh passkey press. It
travels over ordinary mail and the web, unchanged, and anyone who receives it can check it without asking the operator.

**What it is not.** It is not a transport: SMTP and HTTPS are used as they are, and no mail server or website has to adopt
anything. It is not an identity system: a statement proves which passkey pressed, never who held it.

---

## 1. What a statement proves, and what it never proves

A checked statement proves:

1. **A press.** A WebAuthn passkey assertion with user verification (the device's own fingerprint, face or PIN check) was made
   over exactly this text. The assertion is fresh: its challenge is derived from the text, so it cannot be reused for another.
2. **A time.** The press was recorded in a public, append-only log at a stated minute, signed by the log's record key, and the log
   cannot later remove it.
3. **A name, optionally.** If the sender bound their passkey to an `@name` (§7), the passkey that pressed is one that name's
   page lists.

A statement never proves:

- **Personhood.** User verification shows that someone at the device satisfied its check. A PIN can satisfy it, and a passkey can
  be synced or shared. ⛔ Never present a checked statement as "verified human" or "verified".
- **Authorship of the words.** An agent may have drafted them. The press shows a person approved this exact text, not that they
  wrote it. That distinction is the design: **an agent drafts, a person presses.**
- **Who held the passkey.** Even with a name binding, the claim is "a passkey that @name's page lists", not "@name, the person".

**A press in the loop, later a human in the loop.** Today a statement guarantees a press. The further layers of B-PoH
(proximity, vouching and the others in the B-PoH paper) are what raise a press toward personhood. A statement records which
layers it relied on: today, `poh: ["passkey"]` in every record (§5).

---

## 2. Terms

| term | meaning |
|---|---|
| **statement** | the exact UTF-8 text a person signs (§3) |
| **press** | one WebAuthn assertion, user verification required, whose challenge commits to the statement (§4) |
| **record** | the log entry for one press (§5) |
| **log** | the public append-only log of records, `log.333.eco/origin` (§5) |
| **opening** | the parts of the press that let anyone recompute it, including the passkey's public key (§6) |
| **proof** | a record number plus its opening |
| **name binding** | a statement of kind `b-name/v1`, listed by the registry for an `@name` (§7) |
| **carrier** | how a statement and its proof travel: a check link, attachments, a header (§8) |
| **checker** | anything that verifies a statement on arrival (§9) |

Encodings: `b64u` is base64url without padding; `sha256` is SHA-256; `u32`/`u64` are big-endian unsigned integers; `lp(x)` is
`u32(len(x)) || x`; `jcs` is RFC 8785 JSON canonicalization.

---

## 3. Statement kinds

Every statement starts with its kind on the first line and ends with exactly one newline. Lines end with LF (U+000A), never CR.

### 3.1 `b-mail/v1`: a mail message

```
b-mail/v1
from: <From header>
to: <To header>
cc: <Cc header>                      (only when there is one)
subject: <Subject header>
name: @<name>                        (only when the sender signs as an @name, §7)
salt: <32 lowercase hex characters>
attachment: <sha256 hex> <file name> (one line per attachment, in order)

<body>
```

- Header values are the decoded header, with every run of whitespace collapsed to one space and trimmed.
- **The recipients are inside what is signed.** One press covers one message to these people. A copy of the signature in another
  mail, or forwarded to someone it did not address, fails the check (§9.2).
- **Bcc is never listed**, because the signed text travels to every recipient. A message with Bcc recipients is not signed; send
  the Bcc copy as its own message.
- `salt` is 16 random bytes. The log publishes each statement's fingerprint, and without a salt a short message ("I resign")
  could be confirmed by guessing. With it, only those who hold the text can check it.
- `<body>` is the plain-text body, line endings converted to LF, trailing newlines removed, then one LF added.

### 3.2 `b-name/v1`: a passkey signs as a name

```
b-name/v1
name: @<folded name>
```

Exactly these two lines. `<folded name>` is the name as the registry keys it (lowercase, without punctuation), matching
`[a-z0-9._-]{1,64}`. This statement is public on purpose: its proof is published so that anyone can learn which passkey signs
as that name (§7). It carries no salt.

### 3.3 `b-post/v1`: a post on a social network or the web (reserved; its format is not yet defined)

What it will do, stated now so its date is fixed: a post on a social network or a website, drafted by a person or by their
agent, is pressed with the person's passkey over its exact text and each attached file by fingerprint, together with the
network and the author's handle there. The post carries its check as a link, in the post or in the network's own link field,
whose fragment holds the statement and its proof (§8.1). Readers, and the agents that read for them, check it against the log
and against the author's `@name` page (§9), and keep or hide posts in their own feeds by it, each by their own choice, with no
shared score. The network changes nothing. A press shows that a person approved this exact post, not that they wrote it (§1).
The format follows §3.1's shape (a salt where the post is not public) and will be defined when it is built.

### 3.4 `b-agreement/v1`: an agreement between two parties (reserved; its format is not yet defined)

What it will do, stated now so its date is fixed: two parties, each an `@name` or a business `#tag`, record what they both
agreed. One exact text names both parties and what each gives the other, and each party presses it once with a passkey.
That makes two ordinary records (§5) over the same statement. The text is the agreement and travels with the parties; the
log holds only its fingerprint, and a salt keeps short agreements from being confirmed by guessing. Any change to the text is
a different statement, so a changed text is a new offer and can never be accepted in place of the old one. Which press came
first, and whether an offer was withdrawn before the other party pressed, is decided by the log's order, never by the
recorded minute. A business is a party only through a person who presses for it, because only a person holds a passkey;
an agent may draft an agreement but cannot press one. A checker reports that both passkeys pressed this exact text; it never
says that the agreement is valid, binding or kept, and it never reports who held a passkey (§1). The format follows §3.1's
shape and will be defined when it is built.

---

## 4. The press

The signer's device presses on the signing service (`sign.333.eco`), whose relying-party ID is `sign.333.eco`. The service
never receives the statement's text, only its fingerprint, except for `b-name/v1` (§7.2).

1. **The claim.** The signing page builds a claim object for the statement:
   `{ v: 2, sha256: <sha256 of the statement, hex>, mime: "text/plain", bytes: <byte length>, source: <an IPTC digital source
   type, e.g. "digitalCreation" or "compositeSynthetic">, tool: "sign.333.eco", declared: true }`. `rights` is optional.
   No other fields are allowed; there is no free text in a claim.
2. **The challenge.** The service picks a 32-byte `nonce` and sets
   `claimCommitment = b64u(sha256("b-origin/claim/v1\0" || nonce || sha256(jcs(claim))))`. This string is the WebAuthn challenge.
3. **The assertion.** The person's device makes a WebAuthn `get` assertion with `userVerification: "required"`. The service
   verifies it against the passkey it has on file.
4. **The record.** The service writes the record (§5) and returns to the signer's own page: the record number, the record, and the
   opening (§6). The opening goes nowhere else unless the signer sends it.

---

## 5. The record and the log

A record is one JSON object:

```
{ "t": "record", "v": 2, "index": <n>, "claim": <the claim>,
  "claimCommitment": <b64u, §4>, "assertionCommitment": <b64u, §6>,
  "ownerTag": <b64u>, "handlePointer": <b64u, random>, "poh": ["passkey"],
  "witnessedAt": "YYYY-MM-DDTHH:MMZ", "kid": <record key id>, "witnessSig": <b64u> }
```

- `witnessedAt` is written **to the minute**, with no seconds. Checkers must compare it as a time, never as text (§9.3).
- `witnessSig` is the log's record key's ECDSA P-256 / SHA-256 signature (raw `r || s`) over
  `"b-origin/record/v2\0" || jcs(record without witnessSig)`. The key is published in the verifier's trust file.
- `ownerTag = b64u(HMAC-SHA256(tagKey, "b-origin/tag/v1\0" || u64(index)))`, where `tagKey` belongs to the signer's account and is
  shared with no one. Two records by one person carry unrelated tags, so **records are unlinkable unless their signer reveals them.**
- A **withdrawal** is its own entry (`t: "withdraw"`, `target: <index>`), also pressed and signed: its challenge is
  `b64u(sha256("b-origin/withdraw/v1\0" || nonce || u64(target)))`, and its `witnessSig` is over `"b-origin/withdraw/v1\0" || jcs(…)`.
  A withdrawn record stays in the log and keeps verifying; checkers say it was withdrawn.

**The log** publishes C2SP tlog tiles under `https://log.333.eco/origin/` and a signed checkpoint
(`log.333.eco/origin`, the size, the root hash). Records join the log at the next hourly checkpoint. Until then, they are listed
at `https://sign.333.eco/log/waiting`, the whole list, each one still carrying the record key's signature, so a checker can
accept a waiting record without trusting that list.

---

## 6. The opening and the proof

The opening is:

```
{ "salt", "nonce", "authenticatorData", "clientDataJSON", "signature", "credentialPublicKey" }   (each b64u)
```

The record commits to it:
`assertionCommitment = b64u(sha256("b-origin/assertion/v1\0" || lp(salt) || lp(authenticatorData) || lp(clientDataJSON) ||
lp(signature) || lp(credentialPublicKey)))`.

**Checking an opening** against a record:

1. Recompute `assertionCommitment` from the opening; it must equal the record's.
2. Recompute the challenge from `nonce` and the record's claim; it must equal `claimCommitment`.
3. `clientDataJSON` must have `type: "webauthn.get"`, origin `https://sign.333.eco` and challenge `claimCommitment`.
4. `authenticatorData` must start with `sha256("sign.333.eco")`, and its flags must show the user was present AND verified.
5. The signature must verify under `credentialPublicKey` (COSE) over `authenticatorData || sha256(clientDataJSON)`.

The result is: **a passkey whose public key is `credentialPublicKey` made a user-verified press over exactly this claim.** A proof is
`{ "kind": "b-origin-proof", "version": 1, "index": <n>, "opening": <opening> }`. ⛔ A proof never carries the signer's `tagKey`,
which would find every record they ever made.

---

## 7. Binding a passkey to an @name

### 7.1 What a holder does

On their registry page (`333.eco`), a holder chooses to show a passkey. The page sends them to the signing service to press the
`b-name/v1` statement for their own name (§3.2). The registry lists the passkey only after all of these hold:

1. the holder logged in with a passkey within the last 10 minutes;
2. the record's claim is the fingerprint of exactly the `b-name/v1` line for the holder's name, and the record carries the log's
   record key's signature;
3. the opening checks against the record (§6), which yields the passkey's public key;
4. the identity provider confirms the passkey that pressed is one the holder logs in with (§7.2);
5. the identity provider has told the holder about the change, by email or at their next log-in, before it is made.

### 7.2 The identity provider's confirmation

For a `b-name/v1` press only, the signing page sends the statement's line with the press; the signing service checks the line
and its fingerprint itself, then signs a short note for the identity provider: the account that pressed (`sub`), the record
number, the app it is for, one use, 15 minutes (`aud: https://login.333.eco/keys/whose`). The registry redeems the note at the
identity provider with its own client credentials and its own pairwise identifier for the holder. The answer is yes only if the
account that pressed is the person behind that identifier; it never says who pressed otherwise.

### 7.3 What the registry publishes

`GET https://333.eco/api/v1/names/{name}` returns, among the page's fields:

```
"keys": [ { "key": <credentialPublicKey, b64u>, "record": <n>, "since": <witnessedAt>, "until": <time or null> } ]
```

Removing a key sets `until`; the entry stays, so a statement signed while the key was shown still checks. Which device a key is
on is kept privately for the holder and never published.

---

## 8. Carriers

### 8.1 The check link (preferred)

```
https://explorer.333.eco/b-mail#m=<b64u(statement)>&p=<b64u(json(proof))>
```

The statement and its proof travel in the URL **fragment**, which browsers never send to a server. The page reads them once,
clears them from the address bar before any other script runs, and checks everything in the reader's browser (§9). When the link
would be longer than 16,000 characters, the sender uses §8.2 instead.

### 8.2 Attachments (fallback)

`B-Mail.txt` holds the statement's exact bytes (base64 transfer encoding, so no byte changes in transit), and
`B-Mail-proof.json` the proof.

### 8.3 The header

```
B-Mail: v1; record=<n>; check=https://explorer.333.eco/origin/<n>; name=@<name>
```

For filters and agents. The header is a hint: a checker never trusts it alone (§9).

### 8.4 The signature line (informative)

Sent mail shows the sender's name beside the B-Emblem, linking to the check (§8.1) and to their `@name` page, and the words
*signed with HeartBank®* (and, by default, the same in Khmer), linking to the record. ⛔ No remote image, no tracking pixel, no
click log: the emblem is attached inside the message.

---

## 9. Checking a statement

A checker receives a message and decides what to say about it. It reports facts, each scoped; it never says "verified".

### 9.1 Find the statement

From the check link in the HTML part (§8.1), else from the attachments (§8.2). With neither, report *a B-Mail mark with
nothing to check it with*.

### 9.2 Compare the message with what was signed

- the `from:` address equals the message's From address;
- **the reader is among the signed recipients** (`to:` and `cc:`), by any of the reader's own addresses;
- the `subject:` equals the message's Subject (whitespace collapsed);
- the body, before the signature block (`\n\n-- \n`), equals the signed body;
- the message's attachments, other than the carrier files, are exactly those listed, by SHA-256.

Any difference: report *a B-Mail mark that doesn't match this message* and which part. ⛔ Never treat such a message as signed.

### 9.3 Find the record

Compute the statement's SHA-256. Find a record whose claim has it, in the log or in the waiting list. Accept it only with a valid
`witnessSig` (§5). Note whether it was withdrawn. With none, report *no record of this exact message*.

### 9.4 Check the name

When the statement has a `name:` line and the proof's index is the record's: check the opening (§6), fetch the name's `keys`
(§7.3), and find the passkey's public key there. Compare `witnessedAt` with `until` **as times** (a minute-precision signing and a
millisecond removal sort wrongly as text). Report *signed with @name's passkey* only if the key is listed and the statement was
signed before any `until`.

### 9.5 Report

| the checker found | it says |
|---|---|
| everything matches, the key is listed | signed with @name's passkey (record n) |
| everything matches, no name or no proof | this exact message was signed (record n) |
| the message differs from the statement | a mark that doesn't match this message: … |
| the key isn't listed, or was removed before | signed, but not confirmed as @name: … |
| no record | no record of this exact message |
| withdrawn | its signer withdrew it |
| the log or registry can't be reached | couldn't be checked just now (⛔ never read as fine) |

Readers may keep their own list of names whose messages they mute. ⛔ A list is the reader's alone: never shared, never a score.

---

## 10. Security considerations

- **What stops a script.** A signature needs a fresh press with user verification on the person's device. An agent can draft,
  prepare and send, but cannot press.
- **Copied signatures.** Binding the recipients makes a copied link fail for anyone it did not address. A recipient can still
  show their own copy to others; the check page shows the message as signed, so a reader compares.
- **The operator.** The log's record key signs every record; checkpoints and witnesses make the log append-only and its history
  checkable. A compromised operator could sign false records but could not remove published ones unnoticed.
- **Synced and shared passkeys.** A passkey synced across devices, or shared, presses for whoever holds it. This is why the
  claim is "a passkey", not "a person".
- **Fragments.** The check link keeps the text out of servers' logs, but it is in the reader's browser history and passes through
  any link scanner the mail provider uses, which already holds the mail.
- **Names.** A name binding is only as strong as the holder's account: the fresh log-in, the identity provider's confirmation and
  the advance notice are the guards.

## 11. Privacy considerations

- Unrevealed records are unlinkable (§5). Showing a passkey on a name links only the records whose proofs are shared.
- The salt (§3.1) keeps short messages from being confirmed by guessing.
- No remote images, no tracking, no click log. A recipient's opening a message is never reported to anyone.
- A holder's devices and language are theirs: device names are private, and a language is shown only if they choose it.

## 12. Reference implementations

- **The verifier:** `@333eco/origin-verify` (CC0): the record, log and opening checks (§5, §6).
- **The check page:** `explorer.333.eco/b-mail` (§8.1, §9).
- **The registry's Look up API:** `333.eco/api/v1/names/{name}` (§7.3), described at developers.333.eco.

## 13. Changes

- **v1 (draft), 2026-11-12:** first version, from the live services.
- **v1.1 (draft), 2026-11-13:** §3.4 reserves `b-agreement/v1`, an agreement pressed by two parties; nothing else changed.
