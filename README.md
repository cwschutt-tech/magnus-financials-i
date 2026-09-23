# Magnus Financials I, public record

A model long/short financials book. $100,000,000 notional. **No real capital is invested.**
This repository exists so anyone can check that the record was not edited after the fact.

## What is here

- `ledger/instructions.jsonl` is the full instruction log. Every record carries the SHA-256 hash of the record before it, so changing any past record breaks the chain from that point on.
- `ledger/entries/seq-NNNNNN.json` holds each record as its own file, with a `.ots` file that anchors its fingerprint in the Bitcoin blockchain through OpenTimestamps. That proves the record existed at that time.
- `daily/<session>/` holds one folder per trading session, published the next morning and never rewritten:
  - `record.json`: every day's NAV, positions, fills, refused instructions, interest, borrow and dividends
  - `book.html` and `book.pdf`: the same record, readable
  - `inputs/`: every price, dividend, split, Fed funds and borrow-rate file the calculation read
  - `code/`: the exact code that ran, including `rebuild.py`
  - `MANIFEST.sha256`: a fingerprint of every file above, plus `MANIFEST.sha256.ots`, its Bitcoin timestamp
- If a later calculation changes a day that was already published (for example, a dividend the data vendor posted late), the change is listed under `restatements` in the next record. Nothing is changed silently.

## How to check it

1. Timestamps: `pip install opentimestamps-client`, then `ots verify daily/<session>/MANIFEST.sha256.ots`
2. Chain and numbers: from inside `daily/<session>/`, run `python3 code/rebuild.py`. It checks every file against the manifest, verifies the hash chain, replays the book with no network access, and compares every day's NAV with the published one to the cent.

## Rules

The rules in force at any moment are in the ledger itself, as dated CONFIG records. The starting rules file (`config.json`, published in each `code/` folder) is pinned by its fingerprint inside the chain. Costs charged:

- slippage on every fill
- interest on cash and margin, at Fed funds plus the configured spreads
- short borrow per name from Schwab's hard-to-borrow data, on end-of-day short value divided by 360
- dividends received on longs and paid on shorts

## What this does not prove

The fills are modeled, not executed. A timestamp shows when a decision was recorded. It does not show that a broker would have filled it at that price.
