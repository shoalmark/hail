> **A draft, unsigned, and it may change.** This branch, `draft/edition-3`, holds the working text of hail/1's third
> edition. It is not signed and not in force, and any line of it may still change. The signed edition is on `main`.

# hail

**⌜hail/1⌟: agents message each other; the final word stays yours.**

hail/1 is how shoalmark's agent seats message each other, while the final word stays with the person in charge. A seat
hails another seat the way one ship hails another: call first, say who you are, keep it short, and expect a reply. In
shoalmark, that person is the Owner, and the board is where the Owner answers the seats' asks, each answer signed. hail
works without the board, with one exception the spec keeps open: enrolling the Owner's keys, the first time or again
after force ends.

## Status

hail/1, second edition, which replaces the first. Not yet in force. hail/1 takes force in a deployment only when two
things hold: the Owner approves the exact texts of the spec and the roster with two signatures, one on the desktop and
one in the companion app on the phone, given through hail itself or through the board where there is one; and the Owner
then commits the approved roster, signed, on a default branch. Force needs the companion app's signature, by either
route, so while the phone's paths stay open in the spec, no change that needs the phone's signature can be made, through
hail or through a board, the adoption included, and no deployment takes force. Force also needs a signing key for each
of the Owner's three key roles, desktop, phone and recovery, with both device keys able to sign hails, DECISIONs and
commits, so that a lost key can be revoked through hail; and three things the Owner confirms before signing: hail's
tools past the spec's acceptance tests; the forge rule, that on the default branch only the Owner can change the roster
and the Owner's signing-keys file; and, where there is a board, its gate deferring to hail over that file. When no pair
of the Owner's keys can be made, the Owner ends force by an end marker in place of the roster; the deployment's earlier
rules then govern until it adopts hail/1 again. While the spec keeps open how a deployment without a board enrolls the
Owner's keys, the first time or again after force ends, such a deployment can't enroll them either. Its tools come next:
`hail check` first, then `hail send`, and one first release carries both. No seat relies on the Owner-ask relay until
all of hail's tools pass the spec's acceptance tests: `hail check`, `hail send`, the signing tool, the companion app,
the relay's notices, the Owner's digest, the watcher, and the session hooks that keep presence. A seat's hail must be
signed where the roster requires signing of the sender's team, and, across teams, of the receiving team; a missing
signature passes only where neither does. The Owner's DECISIONs and "seen"s count only when signed.

## What this repository holds

New to hail? Start with [hail/1 on one page](QUICKSTART.md).

The spec: [`spec/hail-1.md`](spec/hail-1.md). The edition is written RFC-style, with BCP 14 key words and an informative
ABNF grammar, and its Appendix D holds one reference deployment's own rules.

The article that walks through it, figure by figure: [⌜hail/1⌟: agents message each other; the final word stays
yours](https://shoalmark.tools/blog/hail-1.html).

## How far it reaches

The rules are written per team and per principal; how far they reach is unknown. hail is not for swarms: every message
across teams passes a principal and is filed, because coordination you can't read is lost oversight.

## Licence

`Apache-2.0 OR MIT`, at your option: [LICENSE-APACHE](LICENSE-APACHE), [LICENSE-MIT](LICENSE-MIT), [NOTICE](NOTICE).
