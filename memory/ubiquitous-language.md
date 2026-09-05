# Ubiquitous Language — lookup protocol

**The dictionary lives at [`docs/ubiquitous-language.csv`](../docs/ubiquitous-language.csv).
Its conventions and open questions are in [`docs/ubiquitous-language.md`](../docs/ubiquitous-language.md).**
This memory file is the *rule*; the CSV is the *data*. When they disagree, the CSV wins.

## The rule

The user speaks to me in this vocabulary. When their message contains a value from
the CSV's `id` column (`market_quote`, `melted_gold`), the `en` column (Market Quote,
Melted Gold), or the `fa` column (مظنه, طلای آبشده), that word carries the meaning in
the CSV row — not its everyday English meaning.

**Before acting on a request that mentions any domain term, read the CSV row.** The
`notes` column carries the implementation constraints, and they are not guessable:
VAT applies only to making charge and margin, نرخ خرید means the *dealer's* buy,
حباب is derived and never stored, مثقال is 4.6083 g. Acting on the everyday meaning
of one of these words produces code that is wrong about money.

Cheap lookup, no need to read the whole file:

```
grep -i '<term>' docs/ubiquitous-language.csv
```

## One concept, one word

- In conversation and docs: the `en` term. Not the `aka` synonyms — those exist so I
  *recognize* market wording, never so I reproduce it.
- In code: the `id`, in that language's casing (`MeltedGold`, `market_quote`).
- Two words for one concept is the failure this file exists to prevent. If I catch
  myself writing "spot rate" for `market_quote` or "wage" for `making_charge`, that is
  the bug.

## When a term is missing

A concept with no row does not exist in the code yet. Add the row **before** writing
the type — propose `id`, `en`, and `notes`, and get the user's confirmation, since the
Persian term is theirs to give. New rows keep the file's category grouping
(Unit, Material, Product, Pricing).

## Unsettled

Three questions in `docs/ubiquitous-language.md` are open — the coin design/denomination
axes, the fineness of آبشده, and whether حواله طلا is in v1. The first one shapes the
pricing model. Do not model coins as though it were settled.
