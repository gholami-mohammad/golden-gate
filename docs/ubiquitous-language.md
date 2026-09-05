# Ubiquitous Language

`ubiquitous-language.csv` is the single source of truth for domain vocabulary in Golden Gate.
Every domain type, enum variant, field and API name should trace back to an `id` in that file.

## Columns

| column | meaning |
|---|---|
| `id` | Stable snake_case key. This is the name that appears in code (`MeltedGold`, `market_quote`, ...). Never renamed once used. |
| `fa` | The Persian term as the business says it. |
| `translit` | Romanization, for people who don't read Persian. |
| `en` | The one English term we all use in discussion and docs. |
| `aka` | Other English wordings heard in the market. Recognized, but **not** used in code or docs. |
| `category` | `Product` (a tradable thing), `Material` (a metal spec), `Pricing` (a rate or price concept), `Unit` (a measure), `Actor`, `Process`. |
| `definition` | One sentence. What it *is*, in the business's terms. |
| `notes` | Facts that constrain implementation: purity, weight, unit, derivation formula, disambiguation. |

## Rules

1. One term, one meaning. If a Persian word carries two meanings, it gets two rows with distinct `id`s.
2. If a concept isn't in this file, it doesn't exist in the code yet — add the row first.
3. Rates are never modelled as products, and products never carry a price field directly.

## Open questions

- **The coin family is described on two axes.** `emami_coin` / `old_design_coin` name the *mint design*;
  `full_bahar_azadi_coin` / `half_bahar_azadi_coin` / `quarter_bahar_azadi_coin` / `gram_coin` name the
  *denomination*. The market quotes them as one flat list, so the rows currently overlap. Two options:
  (a) one `bahar_azadi` product with `design` and `denomination` attributes, or
  (b) flat products mirroring the market board. Needs a decision before the pricing model is written.
- **Fineness of آبشده.** Recorded as ~705 (17 karat), the common wholesale standard. Confirm whether
  high-purity melted gold ("بسته", ~995) is also traded and needs its own row.
- **Is `gold_note` (حواله طلا) in scope for v1?** It is a claim rather than metal and pulls in
  counterparty and settlement concerns that no other product has.
