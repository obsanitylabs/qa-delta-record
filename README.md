# qa-delta-record

QA-published SHA record for the DraftCrypto release line. This repository is the **R4 anchor**
for the `main-tip-guard` workflow: it is the second, independent source of truth for the
QA-signed SHA, so the guard never derives the trusted value from the tag it guards.

## Contract

File: `latest-qa-delta.json` (repo root, branch `main`). Unauthenticated public read:
`https://raw.githubusercontent.com/obsanitylabs/qa-delta-record/main/latest-qa-delta.json`

```json
{
  "sha": "<40-hex commit SHA>",
  "delta": "OBS-<n>",
  "verdict": "PASS",
  "recordedAt": "<ISO-8601 UTC>"
}
```

- `sha` — the exact commit SHA a QA delta named as PASS. Must equal the peel of `v0.2`
  (`refs/tags/v0.2^{}`) at the moment the guard runs.
- `delta` — the id of the QA delta that signed that SHA. The tag message must cite the
  same id: `QA signed (OBS-<n>)`.
- `verdict` — `PASS` only. A failing delta must not be published here.
- `recordedAt` — UTC timestamp of publication.

The guard reads this file with **no token** — it is public on purpose, so reading it grants
the release actor no new privilege.

## Who may write

Only a **QA-side credential** may write here. The release actor must not be able to change
this record: that is the whole point of the R4 anchor. A missing, unreadable or malformed
file is a **fail-closed** condition for the guard (it alerts and refuses to touch `main`;
it never reverts when the tag peel and this record disagree).

## Order

QA delta names the SHA -> QA publishes this record -> QA-side operator cuts `v0.2` on that
SHA -> release actor fast-forwards `main`. The record is published **before** the tag cut.

## Contents

SHAs, delta ids, verdicts and timestamps only. **No secrets, no tokens, no credentials.**
