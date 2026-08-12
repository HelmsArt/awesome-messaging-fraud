# Contributing

Additions and corrections are welcome. This list is annotated on purpose, so the bar is about
the note, not the link.

## What belongs here

- Research on A2P messaging abuse, from either vantage point — traffic or message content
- Industry guidance, standards, and working-group output
- Datasets, open or on request
- Open-source tooling: detection, signalling security, messaging infrastructure useful for
  building a test bench
- Regulatory material that changes what operators or enterprises must do

Especially wanted:

- **Traffic-side research.** The list has one substantial entry. There are surely more.
- **Non-English resources.** Coverage outside English is close to zero, and the fraud is not.
- **Anything that closes a gap** listed at the end of the README.

## What does not belong

- Vendor pages that describe a product without explaining a mechanism
- Link-only entries with no annotation
- Dead links, or paywalled material presented as though it were readable. If something is
  gated, say so — the README already does this for GSMA FS.11 and FS.19
- Anything derived from operator traffic, customer records or other data you are not free to
  publish. This list will not become a vector for leaking someone's routing tables

## How to submit

1. Open a pull request editing `README.md`.
2. Put the entry in the section it belongs to, keeping alphabetical or chronological order where
   the section already has one.
3. Write **one or two sentences** on what it is and why it earns a place. If it is research, say
   what the vantage point is and whether the data is available — that distinction is the spine
   of this list.
4. Check the link resolves.

Corrections to existing annotations are as valuable as new entries. If a note is wrong or has
gone stale, say so in an issue and it will get fixed.

## Disclosure

The maintainer is building open tooling in this area (`opena2p`). It is listed in the README's
gaps section and marked as such. Any future entry pointing at that work will carry the same
disclosure — competing tools are listed on their merits, and a PR adding one will not be
rejected for competing.
