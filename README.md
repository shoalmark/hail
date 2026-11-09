# hail

**⌜hail/1⌟: agents message each other; the final word stays yours.**

hail/1 is how shoalmark's agent seats message each other, while the final word stays with the person in charge. A seat hails another seat the way one ship hails another: call first, say who you are, keep it short, and expect a reply. In shoalmark, that person is the Owner, and the board is where the Owner answers the seats' asks, each answer signed. hail works without the board.

## Status

hail/1, first edition. Not yet in force. hail/1 takes force in a deployment only when two things hold: the Owner approves exact versions of the spec and the roster with two signatures, one on the desktop and one in the companion app on the phone, given through hail itself or through the board where there is one; and the Owner then commits the approved roster, signed, on a default branch. Its tools come next: `hail check` first, then `hail send`. Until `hail send` signs, seats' hails go unsigned; the Owner's DECISIONs and "seen"s are always signed.

## What this repository holds

New to hail? Start with [hail/1 on one page](QUICKSTART.md).

The spec: [`spec/hail-1.md`](spec/hail-1.md). The edition is written RFC-style, with BCP 14 key words and an informative ABNF grammar, and its Appendix D holds one reference deployment's own rules.

The article that walks through it, figure by figure: [⌜hail/1⌟: agents message each other; the final word stays yours](https://shoalmark.tools/blog/hail-1.html).

## How far it reaches

The rules are written per team and per principal; how far they reach is unknown. hail is not for swarms: every message across teams passes a principal and is filed, because coordination you can't read is lost oversight.

## Licence

`Apache-2.0 OR MIT`, at your option: [LICENSE-APACHE](LICENSE-APACHE), [LICENSE-MIT](LICENSE-MIT), [NOTICE](NOTICE).
