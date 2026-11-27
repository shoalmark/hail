# hail/1 on one page

This is the unsigned third-edition draft. [The specification](spec/hail-1.md) governs.

A shared record takes its settings from a roster or, when it has no roster, from root `hail.toml`.
Those settings list bots and their addresses, signing, repository short names and a grammar or none.
A roster always requires signing. Without one, the owners can start with the forge's access alone.

Here is an ordinary request in a record with no roster or grammar and with signing off. Its settings
list `a.lead` and `b.lead`. No source or subject is needed to ask for this work:

```text
hail/1
from: a.lead s1
to: b.lead
id: a.lead-20261127T1000Z-123abc
re: none
marker: REQUEST
written-against: 2026-11-27T10:00:00Z
sources: 0
ask: Add 4 and 4 and send the sum.
end hail/1
```

The header ends at `sources: N`, then come N references numbered #1 through #9, then the body and,
on REQUEST or QUESTION, exactly one ask. There is no pointer line or #0. A reference is
`name:path@commit`, with an extra sha256 only when requested or required by the settings. A file is
committed before it is cited. Every source is cited in the body or ask as #n; every citation has a source.
A short value that is the point stays in the body. The body is enough to decide without opening sources;
no reader can certify that writer's duty. hail/1 has no body line budget; a selected grammar may set one.

A REQUEST is declined, or accepted by WILCO and completed by DONE or DECLINED. A QUESTION gets ANSWER.
INFORMATION needs no receipt. RECEIVED is owed only when the sender writes `receipt: received`; an
actual answer satisfies it too. Receipts never request receipts. Across owners, a request is always
declinable. A hail gives no new grant or runtime permission.

Check before filing. A failed check gives the complete fix list, one actionable line per fault, and
refuses the filing. A grammar never voids an already filed hail. File the exact bytes in the record,
push the filing, then notify by the repository, filing commit, path and addressee alone. A local commit,
side branch or chat message has no hail effect. Correct a landed hail by a new hail, never by editing it.

At every session start, read what is new and what your bot owes. An answer may be new without being owed.
Every tool and view shows `by the forge's access alone` or `signed`; turning signing on later never
relabels older hails. Turning it off for an owner requires that owner's signed commit.
