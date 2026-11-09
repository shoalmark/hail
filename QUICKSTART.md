# hail/1 on one page

hail/1 is a protocol by which agent seats message each other, under one person, the Owner, who keeps the final word.
This page is the short way in. [The spec](spec/hail-1.md) governs.

**Status:** first edition, not yet in force. Nothing to install yet: `hail check` comes first, then `hail send`. Until
they exist, hails are written by hand and go unsigned.

## A hail

```text
hail/1
from: a.planner ‹session›
to: a.builder-1
id: a.planner-20261108T0812Z-k8chqr
re: none
marker: REQUEST
written-against: repo@‹commit› · 2026-11-08T09:12:40+01:00
pointer: workbench:briefs/lane-4.md sha256 ‹hex›
Build lane 4 from the brief behind the pointer.
ask: WILCO or DECLINED, then DONE with the commit.
end hail/1
```

- **The frame:** `hail/1` to `end hail/1`. The header is `key: value` lines and ends with `pointer`. The body is at
  most five lines, followed by one `ask:` line, on a REQUEST or a QUESTION only.
- **Pointers, not copies:** the content lives in git, or in a file named by its sha256.
- **Seven messages:** INFORMATION, ADVICE, WARNING, QUESTION, ANSWER, REQUEST, STOP.
- **Four receipts:** RECEIVED, WILCO (will comply), DECLINED (will not), DONE.
- **Once the act check enforces,** a REQUEST also carries `brief` and `subject` (§8a).

## The rules that matter most

1. **A seat's hail carries no authority.** The reader computes authority from a signed roster of grants. The Owner's
   words count only as the Owner's signed answers.
2. **The roster** says who may hail, answer and stop whom. An unlisted pair is refused.
3. **Across teams,** every hail goes from a principal to a principal.
4. **A filed hail is never amended:** a correction is a new hail with `supersedes`.
5. **The nearest transport that reaches the reader:** the bytes are the same on every transport, which decides how
   fast a hail arrives, never whether it counts.
6. **A need only the Owner can meet** goes hop by hop to the principal, which relays it as an Owner-ask, answers it
   from standing evidence, or rejects it. The principal hands the Owner's signed answer back to the seat. This relay
   is specified, not built.

No board is needed: hail works without one.

**Read next:** the spec's §2 to §4 (the frame, the header, the markers), §6 (authority) and §8 (the roster). Appendix C
has one hail per marker. [The article](https://shoalmark.tools/blog/hail-1.html) walks through it figure by figure.
