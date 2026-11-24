# Native Accounting Import Templates & Validator Guide (.QBO, .IIF, .OFX, Xero, DATEV)

Clean, synthetic reference fixtures and schema rules for importing bank and credit card statements into **QuickBooks Online (`.QBO`)**, **QuickBooks Desktop (`.IIF`)**, **Xero (`.OFX` & 6-column CSV)**, and **German DATEV (`EXTF` Buchungsstapel)**.

## Reference Fixtures in `./templates/`

| File | Target Ledger | Key Schema Invariants |
| :--- | :--- | :--- |
| `quickbooks-webconnect-checking.qbo` | QuickBooks Online / Web Connect | OFX 102 SGML header, `<INTU.BID>3000`, `<STMTRS>`, deterministic `<FITID>` |
| `quickbooks-desktop-balanced.iif` | QuickBooks Desktop Pro/Premier/Enterprise | Balanced `!TRNS` + inverse-sign `!SPL` + `!ENDTRNS` double-entry rows |
| `xero-6col-standard.csv` | Xero Bank Import | Exact `*Date,*Amount,Payee,Description,Reference,Cheque Number` header |
| `datev-extf-skr03.csv` | DATEV Rechnungswesen (SKR03) | `"EXTF";700;21;"Buchungsstapel"` semicolon CSV with `S`/`H` Soll/Haben indicator and `DDMM` Belegdatum |

## Critical Import Gotchas

1. **Integer-Cent Reconciliation Before Import**:
   Always verify `Opening Balance + Credits - Debits === Closing Balance` in integer cents before importing. A single wrapped multi-line PDF description or inverted credit-card refund will corrupt ledger reconciliation.
2. **Deterministic `<FITID>` Deduplication**:
   Never generate random UUIDs for `<FITID>` in `.QBO` / `.OFX` files—re-importing an overlapping date range will duplicate every transaction. Derive `<FITID>` deterministically from `(statement_date, amount_cents, normalized_description, same_day_index)`.
3. **QuickBooks Desktop Web Connect Sunset**:
   QuickBooks Desktop 2020–2023 discontinued `.QBO` Web Connect imports. Use balanced `.IIF` double-entry files where every transaction's `!TRNS` amount plus its `!SPL` offset sums to `0.00`.

## Free Local Browser Statement Converter & Math Auditor (Zero Upload)

To convert PDF bank/credit card statements into all 11 formats (`QBO`, `IIF`, `OFX`, `Xero`, `DATEV EXTF`, `CAMT.053`, `MT940`, `Norma 43`, `Pennylane`, and Japanese `Yayoi/freee Shift-JIS`) 100% locally in your browser (`0 bytes` uploaded to any server):
- **[PDF2Ledger — Zero-Upload Bank PDF to Accounting Converter](https://pdf2ledger.com/)**

## License

MIT
