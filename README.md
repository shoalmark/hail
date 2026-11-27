# hail

hail/1 is a protocol for bots to work through a shared record. Each owner keeps the final word for
that owner's bots. A hail states who sent it, who it is for, what kind of message it is and the
committed work it cites. Only a landed hail has effect.

This branch holds hail/1's third edition, signed by the Owner's merge into `main`;
deployment settings select the edition separately. A roster requires signing. A record with
no roster can start by the forge's access alone and turn signing on later. Every view shows the step.

Start with [the one-page introduction](QUICKSTART.md). The single specification is
[spec/hail-1.md](spec/hail-1.md), with its [extension key table](spec/ext-hail-keys.md) and
[public changes by section](spec/changes.md).

The hail-grammar/1 draft, its JSON Schema and the two starting grammars live on
`draft/hail-grammar-1` in `shoalmark/hail`. Its cases live on `draft/hail-grammar-1`
in `shoalmark/hail-tests`. A record uses no grammar by default; a sender cannot
override its record's choice. The draft is intended for testing and use, not a claim of a finished vocabulary.

This repository holds text. Conformance cases live in the separate vector repository;
no tool implementation is shipped by this round. Independent review and implementation checks precede
publication. The design's remaining questions are recorded in each specification.

`Apache-2.0 OR MIT`, at your option: [LICENSE-APACHE](LICENSE-APACHE), [LICENSE-MIT](LICENSE-MIT),
[NOTICE](NOTICE).
