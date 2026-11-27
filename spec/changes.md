# Third-edition draft changes

Changes from the published first edition, at text commit `f5b160698a180481240dbff5c8cc605aaab06121`.
A second edition was approved and not published.
This unsigned draft is published only by the Owner's signed merge.
The list is organized by the sections a reader must revisit, without private findings or identifiers.

## Form, sources and time — sections 1–3, Appendix A

The header ends with a source count, followed by zero to nine references numbered from one. The first
reference takes the former pointer's role, replacing its header line. Citations use #1 through #9,
never #0. Every source is cited in the body or ask, and every citation names a source. Earlier forms
remain readable under their original edition; new filing does not guess an edition from a successful parse.

Sources name a commit, a file, a file's inclusive line range with an optional first-line label,
or the change between two commits, optionally restricted to one file. Plain git object checks
verify the fixed target; labels match bytes, never language constructs. Only the whole-file form
takes sha256, optionally unless settings require it. Detail lives in the work: no file is written
solely for citation. Folders, branches, tags and notes are no sources. Ids and branch names remain
short values in the body. Time
adds UTC Z alongside the two numeric-offset forms already accepted. The body has no hail/1 line budget
or ordinary request/result content requirement; a grammar supplies those. Structured protocol forms remain.
Bot and decider addresses and ids replace the earlier seat-only forms. New conditional headers include
why, clarify, for-team and min-class. The existing sealed-to header now names public recipient encodings
rather than fingerprints. Owner choice labels narrow from A through Z to A through E.
The roster, acting-for and handover headers, grant-change kind and owner-pair sender are retired.

## Receipts and effect — sections 4–7

INFORMATION owes no RECEIVED by default. The explicit receipt request governs RECEIVED; required
question, request and stop answers remain. A source becoming unavailable does not void a filed hail.
The body must be sufficient to decide without opening sources, a writer duty no reader can certify.
Nothing outside a landed hail has effect. Other channels carry only the record reference. Sending
therefore follows filing on the remote default branch, rather than giving an unfiled message effect.

## Settings and routes — section 8 and the extension table

Roster-governed records adopt sealanes/1 edition one for bot ids, people, kinds, grants, places,
pins, keys, session caps and change control. Hail adds extension settings, not core roster keys.
The organisation coordinator and audit auditor are the principals; other teams have leads. The
coordinator is the first Owner gate for bot needs, while the auditor reaches the Owner directly.
Deciders can approach the Owner directly within their declared scope, with for-team choosing
the ask's record. Replies to people use their declared route. The stand-in role is removed.

Record settings are defined once, carried by a governing roster or by root hail.toml without one.
They identify bots and addresses, signing, repository short names and a grammar or none. A source can
name any regular file in a listed repository. Unknown names are refused. No grammar is the default;
the sender cannot select or override it. Across owners, a request remains declinable.

Grants decide routes; the roster writer arranges lead crossings. A carried hail is checked using its
own marker and addressees. A plan matter always names its team. A decider's signed source is published
unchanged by a named bot with existing rights. Owner hails and every STOP are exempt from denial for
a missing reverse grant; that exemption adds no general reply grant.

## Signing — section 9

Signature is not part of what makes bytes a hail. Every tool and view shows the filing step, either
by the forge's access alone or signed. A roster requires signing. Without a roster, an owner can start
without keys; turning signing off after it is on requires that owner's signed commit. Earlier unsigned
hails keep their original step. Present signatures are checked, and signature requirements at filing
cannot be evaded by an earlier send time. Required signing is displayed separately from its
verification result; only verified signing is labeled signed. Owner signing uses the desktop
surface, with no phone-pair signature or companion-app condition.

## Record and operation — sections 10–10b

START, END, CLAIM, RELEASE and FORCED END record session lifecycle and item ownership. START names
waiting work and its initial matter; a claim, not START alone, makes the holder. Sending-session and
first-hail claim checks bind work to the session. A live-claim FORCED END needs an active STOP and
the current holder's full response window, checked at filing; replay keeps that accepted event.
Cross-repository use claims retain the observed sender heads. Replies spanning matters, and free-text
requests or questions, require an explicit matter. Owner-only AWAY and BACK govern coordinator absence;
the first edition's overdue-triggered stand-in and handover mechanism is removed. DECISION cannot change
permissions: roster change control owns grants.

Every session reads what its bot owes at start. A no-roster reader also shows every new addressed hail,
including answers and receipts that owe nothing. The record disables line-end conversion. Its forge
check runs after each push, reports faults and cannot block a push; filing checks run before it.
This edition defines no repository move. Preparation for a necessary move first ends every STOP and
owed REQUEST by the Owner's act; it supplies no migration right or reset of history.

## Checking, examples and entry pages — sections 11–16, appendices

A failed check refuses filing and gives every independent fault in one actionable line each. Dependent
checks with unreadable inputs are reported rather than guessed. A grammar never voids a filed hail.
The examples and introductory pages now show the current form. Old signed and sealed fixture bytes
remain in the pinned compatibility corpus, separate from current-edition tests. References have one list.

## Body grammar companion

hail-grammar/1 is a testable draft with a closed JSON Schema. Grammar files hold line budgets, opener
meanings and per-marker allowed/required openers, plus identification metadata. The five common openers
have fixed meanings; only a batched edition change can change them. Semantic duplicates are refused by
a reader's meaning review, beyond the schema's shape checks.

The two edition-zero starting grammars cover software engineering and brand and communications.
REQUEST begins at three body lines. Negations, restrictions and literal values survive shortening;
STOP and WARNING use complete sentences. A bound stays in the line stating the work, with no Limit or
Do opener. Richer content requirements, word lists, repeated answer values and per-opener lengths wait.
Budgets, a possible Fact split, DONE requirements, allowed-answer sets and element caps remain open;
the data-link proposals wait for their written rules to be read word for word.

The next stage measures entire closed exchanges and newly added files, grammar text read per session,
and correction/repetition hails per hundred. It compares the retained baseline and a plain brevity
control with explicit units. This stage supplies the design and hand-run cases, not implementation or
new shadow-use results. Independent critical review and a second implementation precede publication.
