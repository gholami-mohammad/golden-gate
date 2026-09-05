# Memory Index

Routing table. Find the row matching what you are about to do, open that file, and
skip the rest. Reading this whole directory every session is waste.

| If you are about to... | Read |
|---|---|
| Read or write **any** domain term — a product, price, unit, or purity — in code, docs, tests, or conversation | [ubiquitous-language.md](ubiquitous-language.md) — **applies to every task, including this one** |
| Understand why an architectural choice was made | `decisions.md` — *not created yet* |
| Chase a bug that smells familiar | `debugging.md` — *not created yet* |
| Touch pricing, weight, or money arithmetic | `invariants.md` — *not created yet* |

Create a file the first time there is something real to put in it. Do not create
empty stubs.

## Writing to this directory

After finishing any implementation, record what a future session would otherwise
re-derive: decisions made and the alternatives rejected, conventions discovered,
constraints found the hard way. Not a changelog — git already has that.
