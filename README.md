# Magnus Financials I, public proofs

A model long/short financials book. $100,000,000 notional. **No real capital is invested.**

This repository publishes **fingerprints only**. Positions, fills and prices are kept private. What is public is enough to prove, later, that the private record was not edited after the fact.

## What is here

- `proofs/ledger/seq-NNNNNN.txt`: for every instruction or rule record, its SHA-256 hash, the hash of the record before it (the chain), and the hash of the record file. `.ots` is an OpenTimestamps proof anchoring that line in the Bitcoin blockchain.
- `proofs/daily/<session>.txt`: for every trading session, the SHA-256 of that day's private manifest, which fingerprints every private file (record, book PDF, every input price file, the code that ran), plus the ledger head hash. It is stamped the same way.

## How a reviewer checks the record

When shown the private files for a session:

1. `sha256sum MANIFEST.sha256` must equal the `manifest_sha256` in `proofs/daily/<session>.txt`.
2. `ots verify proofs/daily/<session>.txt.ots` shows the Bitcoin block and date the fingerprint existed.
3. `python3 code/rebuild.py`, run inside the private session folder, checks every file against the manifest, verifies the hash chain, replays the book with no network access, and compares every day's NAV to the cent.

A fingerprint cannot be reversed into the positions, and it cannot be matched by a different, edited record.

## What this does not prove

The fills are modeled, not executed. A timestamp shows when a decision was recorded, not that a broker would have filled it at that price.
