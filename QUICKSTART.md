# hail/1 on one page

hail/1 is a protocol by which agent seats message each other, under one person, the Owner, who keeps the final word.
This page is the short way in. [The spec](spec/hail-1.md) governs.

**Status:** second edition, which replaces the first; not yet in force. Its tools come next: `hail check` first, then
`hail send` (§11). A seat's hail must be signed where the roster requires signing of the sender's team, and, across
teams, of the receiving team; a missing signature passes only where neither does. The Owner's DECISIONs and "seen"s
count only when signed (§9).

## A hail

```text
hail/1
from: a.planner s3
to: a.builder-1
id: a.planner-20261108T0805Z-dtg94c
re: none
marker: REQUEST
written-against: shoalmark@ae79c0d · 2026-11-08T09:05:40+01:00
pointer: workbench:out/to-a.builder-1/lane-4.md sha256 3bc0c99597050c57a3b30da8fde91091f529adfe31b1bf7b8e94bd4c1069fa71
Build lane 4 from the brief behind the pointer.
ask: Either DECLINED, which ends it, or WILCO followed by DONE with the commit.
end hail/1
```

- **The frame:** `hail/1` to `end hail/1`. The header is `key: value` lines, each key once and in lower case, and ends
  with `pointer`. The body is at most five lines, followed by one `ask:` line, exactly once on a REQUEST or a QUESTION,
  and on no other marker.
- **Pointers, not copies:** the content lives in git, or in a file named by its sha256.
- **Seven messages:** INFORMATION, ADVICE, WARNING, QUESTION, ANSWER, REQUEST, STOP.
- **Four receipts:** RECEIVED, WILCO (will comply), DECLINED (will not), DONE.
- **Once a team's act check enforces,** its seats' REQUESTs also carry `brief` and `subject` (§8a).

## The rules that matter most

1. **A seat's hail carries no authority.** The reader computes authority from a signed roster of grants. The Owner's
   words count only as the Owner's signed answers.
2. **The roster** says who may hail, answer and stop whom. An unlisted pair is refused.
3. **Across teams,** every hail goes from a principal to a principal.
4. **A filed hail is never amended:** a correction is a new hail with `supersedes`.
5. **The nearest transport that reaches the reader:** the bytes are the same on every transport, which decides how
   fast a hail arrives, never whether it counts.
6. **A need only the Owner can meet** goes hop by hop to the principal, which relays it as an Owner-ask, answers it from
   standing evidence, or rejects it. The principal hands the Owner's signed answer back to the seat. No seat relies on
   this relay until all of hail's tools, not only `hail check` and `hail send`, pass the spec's acceptance tests (§10b,
   point 15).

No board is needed: hail works without one, with one exception the spec keeps open (§13, item 7): enrolling the Owner's
keys, the first time or again after force ends.

**Read next:** the spec's §2 to §4 (the frame, the header, the markers), §6 (authority) and §8 (the roster). Appendix C
has one hail per marker, and one of each kind the relay uses. [The article](https://shoalmark.tools/blog/hail-1.html)
walks through it figure by figure.
