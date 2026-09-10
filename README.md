# ccm-audit-anchors

Nightly hash anchors for Clear Capital Management forward-test records.

Each line in `anchors.log`:

    <night> owner-<label> hash <night-hash> engine <engine-version>

- **night** — the ET date the hash covers.
- **owner-label** — a pseudonymous chain label (sha-256 of an internal id,
  truncated). One chain per owner; labels carry no client information.
- **night-hash** — the head of that owner's tamper-evident hash chain for
  the night. The same hash appears in the footer of that night's
  DKIM-signed digest email.
- **engine** — the engine version current when the anchor was PUBLISHED:
  anchor-time context only. It is NOT part of the attested night hash —
  the hash covers the record; the engine field just dates the publisher.
- a line ending `(anchored <date>, late)` was published after its night —
  late anchors say so; gaps are disclosed, never backfilled silently.
- chain-block versions: v1 nights (through 2026-08-20) hashed the decision
  `reason` field, which the system legitimately rewrites at resolution —
  those nights rest on these anchors + the signed emails; v2 nights (after)
  exclude `reason` and additionally attest the frozen test configuration
  (configHash), and recompute cleanly from the database forever.

This file is append-only (branch protection blocks force-push and
deletion). GitHub's commit timestamps are the independent clock: an anchor
commit proves the hash existed no later than the commit time.

**Verification** (auditors): the `verify-ft-hashchain.ts` script in the
maintainer's repository, given a read-only database credential and this
repository's URL, recomputes every night hash from the database, checks it
against the retained digest emails, and cross-checks this log and its
commit timestamps — the full independence chain: database → emails →
public log. This file contains hashes only; nothing here is confidential.
