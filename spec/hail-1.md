# ⌜hail/1⌟

**Status:** hail/1, third-edition draft, unsigned. Publication requires the Owner's signed merge.
A deployment selects this edition in its record's settings (§8). Where a roster governs, the counted
state names `spec = "hail/1"`, `edition = 3`, and `extends = { spec = "sealanes/1", edition = 1 }`
in `ext.hail`; sealanes/1's discovery and change rules apply. No DECISION adopts a roster.
- **The decisions are chosen** (§0) and written into the sections they govern.
- **What stays open** is in §13.
- **Informative:** A six-day trial on two teams measures what hail/1 costs and where it fails.

**Abstract (informative).** hail/1 is a protocol by which bots and the people named in their roster send hails through a shared record. Each owner keeps the final word for that owner’s bots. It fixes a message's frame, header, markers and receipts. Sources name committed files. A bot's hail carries no authority: the reader reads rights from sealanes/1 and checks signed answers. A
DECISION approves no act the bot does not already hold. Hail notifications take the nearest transport that reaches the reader. Hails are
signed when §9 requires it, may be sealed to their readers and the Owner's oversight keys, and are filed and never
amended. The record tracks sessions and their claims, one session of each bot to a matter. The rules have not been shown
to work at every scale; hail/1 is not for swarms.

**Conventions.** MUST requires an action, MUST NOT forbids it, SHOULD recommends it, SHOULD NOT advises
against it, and MAY permits it. Plain rules also bind. These meanings are stated here in this text’s own words. §11 gives conformance tests. Appendices A, B and C are informative: where they
and the body differ, the body governs. Appendix D is normative for the reference deployment only. [The extension key
table](ext-hail-keys.md) is part of this edition's normative text.

**Terminology:**
- **Bot:** a lasting teammate listed in record settings, addressed as `<team>.<handle>` with a roster or
  `<owner>.<bot>` without one. A bot outlives its sessions.
- **Session:** one run of a bot, with that bot's identities, keys and grants. Its opaque id is never roster data.
- **Team:** a group of bots with one principal or lead, its places and, where declared, its record folder.
- **Principal:** the organisation team's coordinator or the audit team's auditor, as sealanes/1 §5.5 defines them.
- **Lead:** the bot that speaks for any other team and orders its bots. A lead is not a principal.
- **Owner:** the one person whose own signed commits change the roster and whose signed records are authority.
- **Decider:** a person named under `people` in the roster, whose signed word counts within its grants and scope.
- **Person:** the Owner or a decider. A sender outside the roster is neither a bot nor a person in this sense.
- **Roster:** `[roster]` under sealanes/1, edition one; the state in force is computed from the default branch's chain.
- **Hail:** a span from `hail/1` through `end hail/1`, or its sealed form (§2).
- **Receipt:** RECEIVED, WILCO, DECLINED or DONE (§4).
- **Source:** one numbered reference to a file (§3). The first source takes the former pointer’s place.
- **Matter:** a tracker, an ask, a hail thread or the team's plan; the claim unit of §10.
- **Transport:** how bytes travel. It affects the overdue window, never authority (§7).
- **Watcher:** a helper that pulls and rings the addressed bot (§10a).
- **Act check:** the check inside `hail send` that the bot's hail serves the brief it names (§8a).
- **Scope:** a brief's scope block (§8a), a DECISION's `scope` (§10b), or a decider's teams (sealanes/1 §5.7).
- **Digest:** a list read later in place of a bell; it carries no authority (§10a).
- **Filer:** the team's bot named by `filer`, holding `records.file` for its record folder (sealanes/1 §4.2).
- **Local socket:** a channel whose notification stays on one machine (§7).
- **Ask:** the `ask:` line on a REQUEST or QUESTION; an Owner-ask; or a tracker's question on its Board.
- **Owner-ask:** a QUESTION `to: owner`, from a sender holding `hail.owner`, under §10b.
- **Raising bot:** the bot whose need or concern a hail relays, named by `raised-by`.
- **Gate:** the coordinator for a bot's `for: owner` QUESTION; while it is away, that bot's own lead (§10b).
- **DECISION:** the Owner's signed answer to an Owner-ask, filed as a hail (§10b).
- **Ring, urgent:** an alert with sound for a matter marked `urgent`, under §10b's night rule and cap.
- **Concern:** bad news raised by WARNING; it need not ask for a decision and is never a STOP.
- **Surface:** a session or an Owner signing surface that shows an Owner-ask as data (§10b).
- **Handover:** a session's list of work and responses still owed. It transfers no claim by itself (§10).
- **Delivery:** the signing tool's signed INFORMATION to a filer, carrying a DECISION (§10b).
- **Board:** where a tracker keeps signed answers to its asks. Hail's asks and tracker asks stay distinct (§6.2).
- **Workbench:** a bot session's own working folder. **Runtime:** the software that runs the session and holds its
  permissions. **Forge:** the service that hosts git. A bot's definition is its kind's text; its brief is its task (§1).

## 0. Decisions

| Decision | Chosen | Where |
|---|---|---|
| Roster and force | sealanes/1 edition one, its acts and change control, one Owner device; `ext.hail` names this edition | §8 |
| Bot and session | Bots outlive sessions. START, END, CLAIM and RELEASE are records; at most one session of each bot holds a matter | §10 |
| Stop and cap | STOP holds an item; FORCED END ends a session. A lowered cap ends the latest excess sessions where that roster counts | §4, §10 |
| The coordinator away | Only the Owner's AWAY and BACK, by filing order; unknown or stale means present | §10b.12 |
| People | Every person's hail is signed. Deciders keep the full reach sealanes/1 §5.7 gives them | §8, §9 |
| Oversight keys | K2 and K3 stay in chips; K1 only where no chip exists. At least two oversight keys, one surviving loss of the main machine; R is an offline reading key | §9a |
| Minimum class | The sender may raise the class required for the record, never lower it. No eligible oversight key means refusal | §9a |
| Post-quantum | P-256 for chip classes now; hybrid `age1tagpq` at the next rotation once supported. K1 and R use X25519 | §9a |
| Sealing | Opt-in, with sealing the default for channels whose host is not the forge; git and the local socket stay plain by default | §9a |
| Signed words | The Owner's signed answers count; a bot's summary or bare quote never does. A DECISION never adds an act | §6 |
| Replies | Bot return grants remain explicit; replies to people bind to a received hail; controls have their stated sender rules | §8, §10 |
| Act check | Each brief has a scope block; the per-team mode defaults to shadow. Enforce waits for §13's six cases | §8a |
| Owner route | Both principals and every decider have `hail.owner`; leads have it while the coordinator is away, or always without an organisation team | §10b |

## 1. What hail is, and is not

- **A protocol, not a pipe.** It fixes a message's shape, its receipts, and what it may and may not claim. How bytes
  travel is §7's business.
- **A bot's hail carries no authority.** Authority comes from the roster and the signed records its rules accept; a
  bot's hail can only point at them (§6). The Owner's signed DECISION is that record, filed as a hail (§10b).
- **Sources, not copies.** Sources name committed files; short values stay in the body. A hail cannot drift from the record.
- **Body content belongs to a grammar.** hail/1 sets no body line budget, opener, required reason,
  required result or subject for an ordinary REQUEST. A record with no grammar can ask to add 4 and 4
  without a source or subject. A DONE needs no result source. A grammar may tighten hail/1, never loosen
  it. Protocol form, including one ask on REQUEST or QUESTION and structured Owner choice labels,
  remains hail/1's; the prose a body must say is solely a grammar's to define.
- **No secrets.** A hail MUST NOT carry a secret, such as a credential, a private key, a token or a password: not in its
  header, its body or the content its source names. A source keeps content off the channel that carries the hail; it
  hides nothing from whoever can read the file it names (§9a).
- **A bot's definition and its brief.** A bot's **definition** is the text of its kind, named by hash in sealanes/1. It
  is not a session's own file. A bot's **brief** is the task document the bot is given. Its scope block, at its top, is
  what the act check reads (§8a).

**The body is enough to decide without opening a source.** This is a rule for writers. A source
supports the body and does not carry a missing instruction or decisive value. No reader can check
this sufficiency: passing form, source or grammar checks does not certify it. A reader missing what
it needs asks for it in a new hail instead of guessing.

## 2. The frame

```text
hail/1
<header lines ending with sources: N>
<N source lines, numbered from #1>
<body: zero or more lines>
ask: <one line>            (required exactly once on REQUEST and QUESTION)
end hail/1
<signature block, if signed (§9)>
```

- **A hail is each span** from a line that is exactly `hail/1` through the next line that is exactly `end hail/1`, or
  from a line that is exactly `hail/1 sealed` through the next line that is exactly `end hail/1 sealed`.
  - A sealed span is opened under §9a. Its contents then follow these plain-frame rules.
  - One signature block, when present, starts on the line straight after `end hail/1` and belongs to that hail. A second
    signature block is not part of this edition's form (§9).
  - Everything outside a recognized span is ignored, for example a platform's wrapper or a greeting. Platforms may wrap
    a message in text of their own.
- **Several frames in one message are several hails.**
- **The form is the frame's line.** "/1" names hail's one form, the same for all bots and teams; it is not a version.
  - Before any roster first adopts an edition of hail/1, an edition may change forms, including DECISION keys. From the
    first such adoption, hail/1 grows only by keys and markers its readers can safely skip. A key that carries authority
    is never one a reader skips (§3). Status alone proves no global fact about adoption.
  - A new marker must be safe to treat as INFORMATION, with no act. Reserved
    markers are not unknown markers. Changes lists the forms this edition changes.
  - A revision of this text is named by its edition.
  - A change hail/1's readers can't read becomes hail/2, the next form.
  - A line of the form `hail/<n>`, or `hail/<n> sealed`, with `<n>` other than 1, opens a span the reader can't read. It
    replies only by the readable-header rule below.

A reader rebuilds the canonical form (§9) before finding frame lines, including a line with trailing spaces. For an
unknown form or a malformed span it sends DECLINED only if the header, read under §3's line grammar, yields a listed
`from` and a well-formed `id`, and §8 permits that reply. Otherwise it records the span locally and sends
nothing. This rule adds no return grant.

## 3. The header

**Grammar** (Appendix A gives it in ABNF, informatively):
- **A header line** is `key: value`: the key, a colon and exactly one space, then the value. A key is lower-case ASCII
  letters and hyphens. A key given twice is malformed on every hail. Keys, values that are keywords, markers and the
  frame's own lines are case-sensitive, written exactly as this document writes them.
- **A line MAY hold several pairs,** separated by ` · ` (space, U+00B7, space). A segment that does not begin with a key
  continues the value before it, as in `written-against: a@1 · b@2 · <time>`.
- **Any component MAY end in one remark in parentheses:** a value, or a ` · ` continuation segment, such as `(verified
  on team B's host)`. The reader removes the remark from that component before validating it. A remark holds no ` · `
  and no further parentheses.
- **The header ends with a standalone `sources: N` line**, where N is one digit, 0 through 9. Exactly N
  source lines follow it, numbered consecutively from `#1`. The body begins after those lines and ends at
  `ask:` or `end hail/1`. A body line such as `note: …` is never a header. No other pair shares the sources line.
- **A reader ignores a header key it does not know,** except on a DECISION: a key on the Owner's signed record carries
  authority, so a reader MUST refuse a DECISION that carries a key it doesn't know (§2). A new key on a DECISION follows
  §2's growth rule. The keys defined here for control records are checked, never skipped.

| Key | Required | Value |
|---|---|---|
| `from` | yes | `<team>.<handle> <session>` for a bot; `person.<id>` for a decider, with no session; `owner desktop` for the Owner. A person's address is not a bot pattern |
| `to` | yes | one or more bot addresses, optionally with a session, separated by `, `; a person's address for a reply (§8); or `owner` alone. An explicit session never permits a second holder or narrows a STOP's broadcast (§10) |
| `id` | yes | `<bot>-<yyyymmdd>T<hhmm>Z-<key>`; for a decider, `person.<id>-<yyyymmdd>T<hhmm>Z-<key>`; for the Owner, `owner-<yyyymmdd>T<hhmm>Z-<key>`. The key is six random lower-case base32 characters (`0`–`9`, `a`–`z` without `i`, `l`, `o`, `u`). No shared counter; no reuse except identical bytes on a retry |
| `re` | yes | `none`, one id, or ids separated by `, `. Free text only when the thing answered has no id. On a DECISION, exactly its ask's id; on a FORCED END with a live claim, the STOP's id |
| `supersedes` | no | the sender's own earlier hail withdrawn by this one; on a DECISION, the decision in force it replaces |
| `marker` | yes | one of §4; unknown markers follow §2 |
| `written-against` | yes | zero or more `<repo>@<commit>` and forge states, separated by ` · `, and exactly one fresh time: `YYYY-MM-DDTHH:MM:SS+HH:MM` or a negative offset; `+HHMM` and `-HHMM` also pass. `Z` also passes and means UTC; fractions do not. A valid calendar date, hour 00–23, minute and second 00–59. The offset is bounded as the time is, hour 00–23 and minute 00–59; a time whose offset is outside that is refused. `hail send` takes the time afresh; a retry keeps it. `hail@<commit>` may name the text used to write, never the text in force |
| `model` | no | two non-space words: the writing session's model and effort; no roster grant is inferred from them |
| `decide-by` | on an Owner-ask only | the expiry time, in the time form above. A DONE time in an ordinary ask is prose; no tool times it (§5) |
| `for` | on a bot's QUESTION needing the Owner | `owner`; forbidden on other hails |
| `for-team` | on a decider's direct Owner-ask only | exact team key within that decider's scope; its record holds the ask and DECISION (§10) |
| `disposition` | on an ANSWER to a `for: owner` QUESTION | `relayed`, `answered` or `rejected` |
| `urgent` | no | one-line reason on a `for: owner` QUESTION, Owner-ask or concern |
| `harm-by` | no | only beside `urgent`: a time in the form above |
| `kind` | on an Owner-ask | `allow-once`, `owner-act` or `decide` |
| `why` | on an Owner-ask | one line saying why the Owner's answer is needed |
| `clarify` | on an Owner-ask | addresses separated by `, `: the raising bot first, then the gate; only the sender when it raises its own need. A decider's own ask names `person.<id>` |
| `raised-by` | on a relayed bot need or concern | exact bot id |
| `answer` | on a DECISION | `yes`, `no`, `option A` through `option E`, `words`, `later <time>` or `talk`; constrained by the ask's kind (§10b) |
| `scope` | no | on a DECISION only: `once` (default), or `standing`, the latter only for a `decide` ask |
| `outcome` | no | `unknown`, on a WARNING whose `re` names a DECISION with an unknown act outcome |
| `brief` | on a bot's non-control hail when enforcing | `<id> <sha256>` of the brief; a principal uses its kind's definition (§8a) |
| `subject` | on STOP used by FORCED END; on STOP and REQUEST when enforcing | work items separated by `, `, as §8a; optional on other hails. A STOP always names one item, even in shadow mode; for FORCED END it is the exact matter reference (§10) |
| `decision` | on a use the Owner approves once | `<id> <sha256>` of the DECISION's canonical bytes, without its signature block; beside `brief` where a brief is required. This is not a file source and adds no act |
| `sealed-to` | on every sealed hail, and no plain one | every recipient's public key in its age recipient encoding, separated by `, `, inside the opened canonical bytes |
| `min-class` | on every sealed hail, and no plain one | `K1`, `K2` or `K3`, in that order of strength; never `R` |
| `bot` | on START, END, CLAIM, RELEASE and FORCED END only | exact subject bot id |
| `session` | on those five markers only | the subject session's opaque id; distinct from the sender's session on a START or FORCED END |
| `matter` | START, CLAIM, RELEASE; replies as §10 requires | one matter reference; routing and binding follow §10 |
| `waiting` | on START only | one line naming the waiting work and its evidence, or the starter's logged judgement where the record and trackers cannot tell |
| `receipt` | optional on messages expecting no receipt by default | `received`, requesting RECEIVED; allowed on INFORMATION, ADVICE, WARNING, QUESTION, REQUEST, STOP and unknown markers treated as INFORMATION; forbidden on ANSWER, receipts and control records |
| `sources` | yes, last header line | one digit, `0` through `9`; the count of source lines immediately following |
| `ask` | REQUEST and QUESTION only, exactly once | one non-empty line after the body; forbidden on all other markers |

Every key whose Required column says `yes` is required. `ask` is a body-ending line, never a header pair. Keys scoped to
one kind or marker MUST NOT appear on another, except where the table allows them. `pointer`, `grammar`, `roster`, `keys`, `acting-for` and
`handover` are refused header keys: this edition refuses them, and refuses `kind: grant-change` and `from: owner pair`.
A session handover is content, not a `handover` key. START's `matter` names its initial matter but claims nothing.
Other hails may carry `matter` for routing; §10 requires it for ambiguous id lists, and for a REQUEST or QUESTION
with free-text `re`.

**Sources.** A source line is `#n <value>`, with one space after its number. The count is zero to nine,
consecutively from #1, in order. There is no pointer line and no #0 in this edition. Source #1 takes every
protocol role assigned to the pointer by an earlier edition: for example the ask file on a DECISION and the
STOP file on a FORCED END. An ordinary hail may have no sources. Every source must be cited at least once
in the body or ask, and every citation must name a source in this hail; a header reference alone is not a citation.

A citation is `#` followed by a maximal run of ASCII digits, bounded on both sides by the start or end of a
line or a character other than an ASCII letter, digit, underscore or `#`. It is valid only when the digits
are one of 1 through 9 and that source exists. Thus #0, #01 and #10 are invalid citations, not shorter
citations; write a work item's number as `item 7` when no source is meant. Sources and citations are checked
by hail/1 even when no body grammar is selected. The ask is part of citation checking.

**Earlier editions.** A record reader selects the edition from the settings at the filing event, never by
trying whichever parser accepts. It continues to read earlier-edition hails with their pointer line and
original signature bytes. A legacy frame does not become a new-edition filing by omission of sources.
A form-only reader must be told the edition; it does not silently fall back. The earlier texts remain
available at their edition pins; there is one current text.

**Names and bytes.** Bot, team and person ids are the listed lower-case ASCII ids. Session names use ASCII
letters of either case, digits, `.`, `_`, `-`, with at least one character. Ids are read from the right:
the stamp and random key have fixed forms, so a hyphen in a name is never cut. Each id's sender prefix
matches `from`. Hash and commit hex is lower case. A source path has non-empty `/`-separated segments of
ASCII letters, digits, `.`, `_`, `-`, with no `.` or `..` segment and no leading or trailing `/`. It holds
no `@`, `:` or space. Every filed hail has such a path; the filing check refuses another.

**Source values.** Each value is `name:path@commit`, optionally followed by ` sha256 <hex>`.
The commit is 7 to 64 lower-case hex digits and must resolve uniquely in the named repository; no branch,
head, tag or workbench name substitutes for a commit. The commit fixes the file's bytes. A source is
committed before a hail names it, and available on the shared remote before the hail is sent. A short
value that is the point, such as 8, a verdict or a short commit id, stays in the body.

The optional sha256 is 64 lower-case hex digits and, when present, must equal the hash of the committed
file bytes, including any signature block; checkout conversion is irrelevant. It is required only
where the record's settings require it. Protocol references to checking a source's hash mean checking
its pinned bytes, plus the sha256 if present or required; they add no second mandatory hash. The
`decision:` hash has a different purpose and still covers only canonical decision bytes.

A reader checks that the repository name is listed in the record's settings, the commit exists and
resolves uniquely, and the path names a regular file there. No path component may be a link or submodule.
Missing lookup inputs are reported as not checked; a known missing file or commit refuses filing.
An unlisted name is refused, never interpreted as a local workbench. The file can be anywhere in a
listed repository. A `SHA256SUMS` file is an ordinary source and is not expanded into a second list.
A forge state is text without ` · `; a mailbox id uses ASCII letters, digits, `_` and `-`.

`re` is an id list if any `, `-separated part has id form; then every part must have it. A free description contains no
such part, and never a spelling of `none` in another case. An id's minute that differs from its written time in UTC
passes with a report; `hail send` writes both from one clock reading.

**Source hashes cover files.** A plain filed hail's file is exactly its canonical frame and signature
block as received, if any, with nothing else. A sealed file is exactly its sealed span. DECISION,
delivery, hand-back and forwarded originals name the complete filed file. Only `decision:` hashes
canonical bytes without the signature block.

**The written-against warning light:** if a repository it names has moved past the commit it names, the reader is told
what moved before it acts. This is the case of two hails that cross, such as a hold and a publish.

## 4. Markers

**Messages:**

| Marker | Means | The reader answers |
|---|---|---|
| INFORMATION | information for the record | nothing unless `receipt: received` requests RECEIVED |
| ADVICE | a recommendation; the decision stays with the reader | nothing unless `receipt: received` requests RECEIVED |
| WARNING | a risk to weigh now | nothing unless `receipt: received` requests RECEIVED; an urgent Owner concern uses that request for signed seen (§10b) |
| QUESTION | needs an answer | ANSWER; a `for: owner` QUESTION: the gate's ANSWER with `disposition` (§10b); an Owner-ask: the Owner's DECISION (§10b). RECEIVED comes first only if requested and the answer must wait |
| ANSWER | answers a QUESTION | nothing |
| REQUEST | asks the reader to act within its own grants | either DECLINED, which ends it, or WILCO followed by DONE or DECLINED "can't act". RECEIVED comes first only if requested and the decision must wait |
| STOP | stop one named item, and wait (§6) | DONE when stopped, or DECLINED "already done". RECEIVED comes first only if requested and stopping takes time |

**Receipts:**
- **RECEIVED:** the hail was read in full, and its sources checked against their pinned bytes where the reader could. It says nothing
  about having read what the source names. A RECEIVED `from: owner`, signed, whose `re` names a concern, is the Owner's
  "seen" (§10b).
- **WILCO:** will comply.
- **DECLINED:** will not. A writer may give a reason, for example:
  - `source differs: <hex>`;
  - `commit not found: <sha>`, after one pull;
  - `malformed: <complete fix list>`;
  - `not in the roster`;
  - `reserved marker`;
  - `unknown form`;
  - `id reused` (point 5, below);
  - `already done at <time>`;
  - `can't act`, from the bot that would act on a DECISION (§10b).

  An act the reader's runtime refuses is DECLINED, and MUST NOT be passed to another bot.
- **DONE:** finished. No body text or result source is required by hail/1. An
  accepted REQUEST ends with DONE, or DECLINED "can't act"; a declined one ends with DECLINED. An ANSWER
  MUST be used only for a QUESTION.

**Rules:**
1. **Receipts aren't receipted.** An INFORMATION needs no RECEIVED. RECEIVED is owed only where
   the sender's hail asks for one with `receipt: received`. The key makes the request machine-readable
   without adding an `ask` line to a marker that forbids it. A real answer can satisfy the requested
   receipt too; no redundant receipt is then owed.
2. **A receipt quotes the id it answers in `re`.**
3. **Every answer counts,** for token efficiency.
   - Any accepted hail from an addressed bot whose `re` names the id counts as that bot's RECEIVED for its clock, and
     stops it (§5). It says the hail was read, not that its source was checked. For a FORCED END trigger, only
     a response whose `from` names the holding session answers the STOP (§10).
   - For a QUESTION, a REQUEST or a STOP, RECEIVED is sent only when requested and the real answer must wait. A reader that can answer
     at once skips it.
   - For an INFORMATION, an ADVICE or a WARNING with a requested receipt, RECEIVED is a full answer.
     With none requested, no receipt or first-response clock is owed.
4. **A source target is no condition of a filed hail's effect.** Filing checks source form, availability
   and any required hash. A later reader reports an unavailable or differing source, but does not void
   the hail or suspend its ordinary effect on that ground. Grants, STOPs, session state and the explicit
   binding checks for an Owner decision still apply. This rule supplies no missing authority evidence.
5. **Duplicates:** a duplicate is the same id with the same canonical bytes (§9), however its transport wrapped it. A
   reader knows the ids it has seen from its team's filed record and from its own history (§10), never from one
   session's memory alone, so a new session of a bot sees what earlier sessions saw. A reader that can't read its
   history doesn't act on the hail, except to obey a valid STOP (§6.3). It answers only where §4 asks for an answer and
   §8 permits one, and then only with RECEIVED, except DONE or DECLINED "already done" for a STOP; it sends
   nothing for a receipt or an ANSWER (rule 1). It tells its team once, by one WARNING (§8), for as long as that failure
   lasts: no acknowledgment, other hail or later session raises another, and a re-send of it follows §5. Once its
   history is readable again, a new failure is warned of anew. A reader that gets a duplicate it has already answered
   sends its receipts again, unchanged, and doesn't act again. For a QUESTION, it sends its original ANSWER again. A
   hail that reuses an id the reader has already seen with other canonical bytes MUST NOT be acted on: the reader
   answers DECLINED, "id reused", and the sender sends it again under a new id.

**Control records:** these use the same frame, have no `ask`, and need no protocol reply. They have the sender checks of
§10; they are not briefs and pass the act check as controls. Filing still needs the filer's existing right.

| Marker | Meaning | Sender |
|---|---|---|
| START | a bot's session begins; states initial matter and waiting work | a starter whose `bot.wake` reaches the bot, or the Owner |
| END | the session ends and releases all its matters | that session |
| CLAIM | the session holds one matter | that session, after its START |
| RELEASE | the session lets one matter go | that session, while it holds that matter |
| FORCED END | a session ends without its own END | the Owner, or a sender whose `hail.stop` reaches that bot; §10's trigger is required |
| AWAY | the coordinator is away | only the Owner's own signed filing into the organisation team's record |
| BACK | the coordinator is present | only the Owner's own signed filing into the organisation team's record |

**Reserved:** DECISION, INSTRUCTION, AWAY and BACK. DECISION is only the Owner's signed answer to an Owner-ask. AWAY and
BACK are only the Owner's records of §10b.12, need no ask, and must also be signed as hails. INSTRUCTION has no act in
this edition. An unauthorised use is DECLINED, "reserved marker", under §2's reply rule. An unsigned person's hail is
DECLINED, "not signed" (§9).

A STOP to a bot reaches every running session of it. The session holding the named item stops it and answers DONE; if it
is already done, DECLINED "already done". A STOP ends no session and releases no claim. Other sessions
MUST NOT take the item over merely because it has stopped. An unheld stopped item stays stopped when next claimed. A
FORCED END, in contrast, releases claims and capacity from its filing (§10).

## 5. Overdue

**Clocks run only for a required answer or an explicitly requested receipt, per addressed bot:** one for each pair of hail id and addressed bot. A response from one bot stops only
that bot's clock. A retry, handover or escalation keeps every other addressee outstanding. The clock starts on the first transport notification after landing, never before landing or at the written time. The sender logs that send and the route; a checker
uses that log as an explicit input (§11). FORCED END has its separate filing-time trigger in §10; it does not
change this per-bot clock.

| Transport | Window until the first response |
|---|---|
| a runtime's own cross-session message on one machine | 15 minutes |
| a hosted service's own bot message | the addressed bot's `wake-interval` plus 15 minutes; 0 where the message wakes it |
| through git, across teams or on one team | the addressed bot's `watcher-interval` plus 15 minutes; 0 where none is declared |

The intervals are integer seconds under `ext.hail.bots` ([key table](ext-hail-keys.md)); each is the longest gap, not an
average. A poll each minute gives 16 minutes. A routine every 15 minutes that may run 7 minutes early or late has a
longest gap of 29 minutes and gives 44 minutes. Sender and checker read the settings in force for the send. A bot asleep
within its declared interval is not overdue, a principal included.

- When a response is overdue, the sender re-sends once to that bot, with the same id and bytes, then tells its own lead,
  or its principal in an organisation or audit team. It MUST NOT go round the reader through another bot.
- A lead whose coordinator is overdue re-sends and waits. Silence gives no `hail.owner`; only §10b.12's AWAY does. A
  principal may tell the Owner through the digest and rings only for an urgent matter (§10b).
- DONE is due by the time the ask names, if any, but no tool computes that time, marks DONE overdue or rings for it. The
  sender MAY follow up with a QUESTION on status whose `re` names the REQUEST, or withdraw its own REQUEST by
  `supersedes`. That QUESTION has its own first-response clock. A prose time SHOULD use §3's time form.
- Until a watcher exists, the sender checks at its next turn (Appendix D, D.2).
- People have no bot wake or watcher interval. An Owner-ask follows §10b's expiry and notices. No overdue route to a
  decider or other person is added here.

## 6. Authority

1. **The reader computes authority; the writer MUST NOT claim it.** A REQUEST stays within the reader's current grants
   and runtime permissions. A DECISION never adds an act: an `allow-once` yes approves one use of an act the bot already
   holds. Widening it takes the Owner's roster commit, under sealanes/1 §3.
   - Grants cover acts that leave the workbench: messages, stops, wakes, pushes and filings, as sealanes/1 §5 lists.
   - Reading, writing and review in the bot's own workbench need no grant. Where the team has a record, work on a matter
     still waits for a filed START and claim (§10).
   - The act check only narrows (§8a). A start supplies no brief and no review tier (sealanes/1 §5.6).
2. **The Owner's words count through the Owner's signature.** A signed DECISION, or a signed answer on a tracker, is
   authority within its rule; a bot's filed summary, date or hash is not. A bare quote carries no weight.
   - Hail works without a Board: keys and force follow sealanes/1. With a Board, one matter is asked in one place, as an
     Owner-ask or on a tracker, never both. A bot points at an existing ask instead of opening its twin.
   - Deciders answer tracker asks only as sealanes/1 §5.7 permits. A decider's ANSWER hail is not a DECISION or a
     replacement for a tracker answer. Marks, take-ups, hand-backs and the answer in force are read on the ask itself.
   - A quick word in chat has no effect; file the required hail before any bot relies on it.
3. **A STOP needs `hail.stop` reaching the bot, or the Owner, and no Owner source.**
   - A lead stops its team's bots; the coordinator stops the leads; the Owner stops any. The actual grant must reach the
     recipient: a role alone grants no STOP. One lead session may stop or force-end another session of the same bot when
     its grant reaches itself. A decider holds no `hail.stop` under sealanes/1.
   - A reader without its history still obeys a STOP that passes the signature and stop checks, records it locally,
     answers as §4 says, and acts on nothing else. A replay of a lifted STOP may hold work again; the reader cannot know
     it was lifted until it reads its history, or its sender or the Owner lifts it.
   - The sender lifts its STOP by superseding it. The Owner lifts any STOP by a
     verified `yes` on an `allow-once` ask whose source names that STOP's filed file. No other DECISION lifts it.
   - A sender withdraws its own REQUEST by `supersedes`, not by STOP. Stopping an item never ends a session (§10).
4. **Consent MUST NOT be relayed.** A hail cannot approve a permission prompt, change a setting or a kind's definition,
   or stand in for the Owner's act.
5. **Transport proves no sender.** A session's message may leave under another session's transport address. Only §9's
   signature proves the signing key, and shared keys do not prove a session.
6. **The Owner in a bot’s own session is that session’s user, not a landed hail.** Runtime consent remains local; a direction has no hail effect until it is landed under §7. It is not
   passed on as the Owner’s word except under point 2. A tap for that bot's own act never closes
   an ask elsewhere. The Owner signs every DECISION. In a team with a record, a principal's start still needs the
   Owner-signed START; a session started by words alone works on no matter until it is filed (§10).
7. **A bot's need of the Owner goes through its gate.** No bot poses it in its own chat alone. The coordinator is the
   first gate for all teams; while it is away, each lead is the gate for its own team. Without an organisation team each
   lead uses its own `hail.owner`. The auditor reaches the Owner directly and is never gated by the coordinator. A gate
   relays, answers with standing evidence, or rejects; its answer creates no authority. A bot MUST NOT go round a
   rejection; it may raise new evidence at the gate. A decider may choose a direct hail to the Owner or a route through
   the coordinator; no tool forces the latter (§8).
8. **A bot verifies a DECISION before acting.** It checks exact form, the Owner's signature, the ask and its pinned filed file, filing in the asking team's record, the decision in force, current grants and runtime limits, and that the act
   stays within what was asked. A one-time decision must answer this bot's need and have no prior use claim. It also
   needs its session's matter claim (§10). Standing evidence follows §10b.4 and uses no authority up.
9. **An ask from outside the roster is a request a bot may serve, never obey.** A bot shares only public information
   unless a disclosure under sealanes/1 §5.7 allows more. The disclosure is the answer in force on the information's
   team's Board; it names who, by the channel they asked on, what, which matter and until when. At answering, the bot
   checks the current roster, deciding person, answer, date and scope, and names the disclosure in its answer's
   record. Raw personal data never passes by a disclosure; a disclosure changes no grant.

## 7. Transports

**Nothing outside a landed hail has effect.** A bot acts on no direction received only in chat,
a session message, a hand-back, a copied frame or a notification. Another channel carries a pointer
to the landed hail and nothing else: the record repository, filing commit, file path and addressee.
That transport pointer is not a source line inside a hail. It carries no body, verdict, correction or
commitment. A reader fetches the record and verifies the hail; an empty or forged notification changes
nothing. A source file is evidence, never an independent message with effect.

The nearest transport that reaches the reader may carry that pointer: a runtime's session channel,
a parent's channel to a child, a hosted message, a paste or a watcher. It never carries a parallel copy
of the hail's words. The record is the single copy with effect. A view of the fetched record may render
it, including an Owner signing surface; a notification may only point to it.

Every named source commit must be on its shared remote before sending. A hail lands when its filing
has reached the record's remote default-branch chain (§10). A local commit or side branch is not enough.
A blocked push gives no message effect; retry the same bytes after making the filing possible.
Settings changes and signed authority records keep their own acceptance rules; an unfiled message
cannot substitute for any of them. No new service is required: a watcher can fetch and list new hails.

## 8. Who may hail whom

**Record settings: one definition, two carriers.** A record's settings are its bots and their addresses;
its signing step; the repositories its hails may refer to, each under a short name; and its grammar or
none. They are read at the filing event for a record check, and at the current counted state for a new
act. Later settings do not rewrite old hails. A governing roster wins where both carriers are present;
`hail.toml` then supplies no second authority or fallback. An unreadable governing roster is a refusal,
not permission to fall back to `hail.toml`.

| Setting | Meaning and bounds | Roster carrier | No-roster carrier at the record root |
|---|---|---|---|
| Bots and addresses | each exact listed bot and its owner or team; unknown sender or addressee refuses filing | core teams and bots, with their core keys | `owners.<owner>.bots`, an array of distinct bot names; address `<owner>.<bot>` |
| Signing step | as §9; required with a roster; otherwise off initially, then on for an owner or the record | the roster itself turns signing on; core signing keys | `signing`, `"off"` or `"on"`, default `"off"`; optional `owners.<owner>.signing` may turn that owner on, never override record-wide on; `owners.<owner>.signing-key` and `owners.<owner>.keys` supply the public keys described in §9 |
| Repositories | map of short names to repository origins; empty by default; names match `[a-z][a-z0-9-]*`; each name has one origin, distinct names may alias an origin | `ext.hail.records.<team>.repositories`, each origin already declared by core places or records | `repositories` with the same map |
| Source hashes | boolean `source-hashes`, default false; when true each source carries sha256 | `ext.hail.records.<team>.source-hashes` | `source-hashes` |
| Grammar | `"none"`, or `{ source = "name:path@commit", edition = N }`, N a nonnegative integer; grammar source follows §3, and its loaded edition must equal N | `ext.hail.records.<team>.grammar` | `grammar` |

The grammar defaults to `"none"`. A record naming none is bound by hail/1 alone. A sender chooses no
grammar: it uses the destination record's settings, with no per-hail selector or fallback. An unknown,
unreadable or invalid selected grammar refuses new filing, rather than silently becoming none. A
loaded grammar is pinned by source commit and its own edition; earlier files remain readable at their
pins. The draft grammar procedure lives in [hail-grammar/1](../hail-grammar/hail-grammar-1.md).

These values define the settings once; carriers do not change their meaning. Source names need only
be listed here: any regular file in a listed repository may be cited, not just a place or record folder.
The roster mapping assigns short names to existing origins and creates no push, file or disclosure right.
`written-against` uses the same short names; the reserved text label `hail` may identify the specification.
A source named `hail:...` still needs a listed repository, and naming it grants nothing.

Without a roster, `hail.toml` is UTF-8 TOML with `hail = 1`, `edition = 3`, a nonempty `name`, a `host`
owner name and the settings above. Owner and bot names use the roster's lower-case name form. The host
can be neutral with an empty bot list. The record's owners are the keys of `owners`; the host must be
one of them. Unknown keys, duplicate addresses, malformed origins or wrong value types refuse the
settings. Bots default to no intervals; their polling settings are operational, not authority. The
host manages shared settings and forge access; each owner's tables are that owner's to change. With
signing on, changes to an owner's tables need that owner's verified signed commit, and shared changes
need the host's verified signed commit; when signing is off, forge access is the guard. A guest keeps
a full clone. No forge access or unsigned name proves who wrote a hail.

```toml
hail = 1
edition = 3
name = "shared-work"
host = "a"
signing = "off"
grammar = "none"
repositories = { work = "https://forge.example/work" }

[owners.a]
bots = ["lead"]
[owners.b]
bots = ["lead"]
```

Without a roster all listed bots may hail one another. A request across owners is always declinable;
a STOP from another owner has no authority over a bot. It may stop only its own owner's work with that
owner's authorization. Roster grants, principals, deciders, session claims, caps, Owner-ask authority and
sealing policies are available only where their governing roster supplies them. They are not inferred
from a `hail.toml`. The common form, sources, filing, catch-up and signing-step display still bind.
A record begun without a roster stays readable after adopting one under its earlier settings.

**Roster adoption.** A record governed by a roster adopts sealanes/1, edition one. Hail's tools enforce `hail.message`, `hail.answer`,
`hail.brief`, `hail.stop`, `hail.warn` and `hail.owner`, exactly the acts of its §5.1. No extension gives a grant. The
roster holds the Owner, people, teams, kinds, bots, identities, public keys, grants, places, record folders, filers and
session caps. Only hail settings live in `ext.hail`; every key, scope, type, default and refusal is in [the one key
table](ext-hail-keys.md). This edition adds no core roster key.

**Which roster.** Readers follow sealanes/1's discovery, state in force, chain, trust and pin rules. A live check at
sending, arrival and before acting reads the state in force at that reader's default-branch head. A record check reads
the state that counted at the hail's filing event. It does not rewrite that verdict after a key or grant changes; a new
act still needs current rights. A `hail@<commit>` names the writer's text and changes no rule; a difference from the
adopted edition is reported. A reader that cannot read the chain to a counted state refuses every right. Removing a
roster by a counted Owner commit refuses all its rights; putting it back follows sealanes/1, never an `ended:` marker,
fresh-key rule or an interval that restores old grants.

**Slices.** A bot reader never resolves. It reads its lock and accepts what its pin holds until that pin moves, even
where another reader reports it behind. A resolving reader follows sealanes/1 §2.3's refusal rules, including a lowered
cap, removed keys or rights; a change in this extension's restrictions refuses hail acts at a pin behind. Hails across
teams are checked and acted on only where the whole roster is read: in the roster's repository or by a reader that
resolves. A slice is never enough to infer a missing team's rights. Such hails are filed only in record folders read
that way. A decider's act on a team absent from a slice is not checked or acted on there.

**Act selection.** The sender checks before sending; the reader before acting. Refusal is DECLINED, "not in the roster",
if §8's reply rules allow it, otherwise a local record. These checks use grants in effect, with `until`, `after`,
unknowns and patterns resolved by sealanes/1; a dropped grant grants nothing.

| Hail or act | Right checked |
|---|---|
| an ordinary message with no act of its own | `hail.message` to each bot; its `only`, if given, must include the marker |
| a response under §4 | `hail.answer` to a bot sender; for a person sender, the received-hail-bound reply rule below; never an unsolicited message |
| a brief that counts | `hail.brief`; a signed delivery is also required by §8a |
| STOP, or FORCED END | `hail.stop` reaching the bot, unless the Owner sends it; FORCED END also needs §10's trigger |
| WARNING | `hail.warn`: every bot's whole team by rule, plus the bots a grant's `to` names |
| any hail `to: owner` | `hail.owner` by rule only, except a reply to the Owner's own hail under the rule below; response and reserved-marker rules still apply |
| START, END, CLAIM, RELEASE | §10's sender rule; no further hail grant |
| AWAY, BACK | the Owner's signed record and own commit, §10b.12 |

A `hail.message` grant restricted to WARNING sends no WARNING: WARNING has its own act. A receipt creates no reply
right. An ordinary bot hail that requires a reply needs the corresponding reverse `hail.answer` grant.
The Owner's hails and every STOP are exempt from this reverse-grant denial. A missing return grant
elsewhere in the roster cannot deny either of them. This exception gives no bot a new reply grant;
a STOP still needs its sender's stop right, and a reader files its required response through the
existing record route. Return grants
name exact bots or sealanes/1's bot patterns, never a session. Control records need no return grant. A control record
does not require its sender to claim the subject bot's work; the sender still needs a live START where its team has a
record, except the stated startup filing exception.

**Roles and routes.** Grants alone decide a hail's permitted route and effect. Team work crossing its
lead is a rule for whoever writes the roster, not an extra denial a hail reader invents. A permitted
direct grant therefore works. In a shared tracker folder the core tracker-number range still decides
which team holds the tracker. A forwarded hail keeps the original file unchanged; the forwarder's
summary is its own text. WARNING uses `hail.warn`, and a reply uses its return grant or the person-reply
rule below. No inferred lead route gates the auditor's findings or narrows a decider's granted reach.

**Carrying a hail.** A REQUEST to carry, publish or file another hail does not turn that hail into a
REQUEST. The reader checks the carried hail's own `from`, `marker` and `to` under its own applicable
rights and signature rules, and also checks the carrier's request and filing right. A permitted outer
REQUEST cannot smuggle a STOP or a message to an otherwise unreachable bot. The source stays unchanged.

**Deciders.** `from: person.<id>` is signed by that decider's `keys.signing` (§9). Its `hail.message` and `hail.answer`
grants reach the bots of the grant's teams, leads included, and the coordinator; `hail.brief` reaches those teams' bots.
The grant's `in` narrows its scope, and no `to` is added to a decider's grant. It holds `hail.owner` by rule, direct or
through the coordinator at its choice. No tool narrows this reach. A decider sends no STOP and never directs the
auditor. A hail may carry a take-up or hand-back, but the ask's own signed record decides it. Tools keep decider acts
where the Owner and auditor can read them; a decider's hail is not sealed (§13), so its `seen-by` overseers read it
whole.

**Publishing a decider's source.** The record settings name one publisher bot for each decider at
`ext.hail.publishers.<person-id>`. It is an exact bot of a team in that decider's scope and must already
hold the needed file right. The bot publishes the decider's signed source unchanged and passes its
committed reference to the destination filer. No person holds a push right through this protocol;
a decider's signing right creates none. Missing publisher, wrong team, changed bytes or missing file
right refuses this route. A named publisher does not replace the destination record's filer.

**Replies to people.** A bot may answer a hail it received from a person with RECEIVED, ANSWER, WILCO, DECLINED or
DONE, as §4 permits, with `re` naming that hail and `to` naming its sender, `person.<id>` or `owner`. No `to` grant
naming the person is needed. A bot answering the Owner's own hail needs no `hail.owner`. The checker verifies the
received hail, its sender and addressed bot, and the reply's marker and `re`; this rule gives no unsolicited message
right. The bot's team's filer files the reply in that team's record, where the person reads it. The Owner's replies
and deliveries are authenticated directly, without roster pairs. A decider's own reply to a bot uses its `hail.answer`
reach above.

**Bots outlive sessions.** A bot's hail reaches its sessions through §10: the holder of the matter takes an order or
question, and a STOP reaches all of them. A handover lists each unanswered hail and each accepted REQUEST still owed
DONE, by id; DONE, DECLINED "can't act", or the sender's withdrawal removes a REQUEST from that owed list.

## 8a. The act check

A bot acts within narrow grants, yet can be steered off its task by injected text, a misread brief or drift. The act
check tests its hail against the brief it names, at sending. It never widens a grant, replaces a person's signature, or
judges whether a brief is wise. This is hail's half only; it does not enforce another namespace.

Pushes and filings have a separate, open act check (§13); their sealanes/1 checks still bind. A start is not a brief and
has no tier. `bot.wake` is checked by the starter's hook, or logged, under sealanes/1 §5.6; no scope block creates a
wake right. A person's hail is no bot's act and needs no brief.

**1. The scope block.** A brief begins with this block, before its prose:

```text
scope:
  brief: <id>
  bot: <team>.<handle>
  acts: hail, stop, push, file
  subject: <item>, <item>
  targets: <branch>, <bot>
  until: <time>
  supersedes: <brief id>
```

`brief` is the brief's id; `bot` the one bot it is for; `acts` lists `hail` for ordinary hails and `stop` for STOP, with
`push` and `file` reserved for the separate git half. `subject` lists trackers, hail ids or branches, and accepts §10's
matter reference forms for a STOP used by FORCED END. `targets` lists branches and bot ids. `until` is a time in §3's
form; a condition stays in prose. `supersedes`, optional, names an
older brief from the same issuer for the same subject. Every other field is required. These are scope names, not new
roster acts. The issuer writes the block within its own grants; a bot MUST NOT write its own scope.

**2. Who may brief, and when it counts.** A lead briefs its team's bots, the coordinator briefs the leads, and a decider
briefs the bots its grant reaches. Each needs `hail.brief` in effect for the recipient. A brief counts only when a
signed hail by such an issuer delivered its exact hash, `until` has not passed, and no newer brief of that issuer for
that subject has replaced it. The ordering and overlap cases in §13 remain open.

A principal's own scope is its kind's definition, named by hash in the roster. The Owner's verified roster commit stands
for the issuer and signed delivery; the principal never briefs itself. Its definition's scope has no `until` or
`supersedes` and counts while the state in force names its hash. A new definition needs the Owner's commit under
sealanes/1. Without readable matching bytes and a usable scope the act check cannot establish it; shadow logs that
failure. Other bots do not gain this exception merely by sharing the kind.

**3. Every act names its brief.** A non-control bot hail carries `brief: <id> <sha256>` when enforcing. A STOP or
REQUEST also carries `subject`, and each item must be within the brief. These are authorization
fields for enforce mode, not required content of an ordinary request. A use the Owner approves once carries `decision:
<id> <sha256>` as well: the sha256 covers the DECISION's canonical bytes, without its signature block. The signature is
checked separately. This hash is not a source hash (§3); the approval adds no grant and replaces no brief limit on the
subject or targets.

**4. The check.** It runs inside `hail send` on the final frame and real destination, in one step with signing and
sending. It reads a fixed snapshot of the brief, roster in force and clock. A non-control hail passes only if:
- the brief counts and its bytes match its hash;
- its `bot` is the acting bot, proven by the bot's own signing key;
- its act is in `acts`, each recipient in `targets`, and each required subject in `subject`;
- any once-approved use names and verifies the DECISION under §6.8;
- the snapshot's clock precedes `until`, except for a principal's root scope.

Unknown, missing or ambiguous means refused in enforce and logged in shadow. A team key, or keys other bots can use,
cannot prove the acting bot. Keys shared among sessions of the same bot prove no session (§10). The six open cases of
§13 block deployment of enforce mode, even where a particular brief looks clear.

**5. Control acts without a brief.** These pass regardless of a brief's targets, and with no brief:
- a receipt or ANSWER to the sender of a hail addressed to this bot, under §8's reply rules;
- the one team WARNING of a reader unable to read its history (§4);
- a `for: owner` QUESTION to its next permitted hop;
- one WARNING "act refused" to the issuer or the bot's lead or principal per refused act, and an urgent concern;
- a WILCO claiming a verified DECISION, to the bot whose INFORMATION delivered it under the return grant, or `to: owner`
  under §8's reply rule or its own sender's `hail.owner` (§10b.9); a standing-evidence WILCO to its gate (§10b.4);
- START, END, CLAIM, RELEASE and FORCED END, after §10's checks; AWAY and BACK are the Owner's records.

This lifts only the brief check. Grants, signatures, filing, session state and one use still apply. It permits no
ordinary REQUEST or STOP and never permits the refused act. A refused act is not sent; the waiting sender's overdue
clock keeps running.

**6. The mode.** `ext.hail.teams.<team>.mode` is `shadow` by default, or `enforce`, per team. Only the Owner's roster
commit sets it. Shadow logs the act, brief, refusal it would make and reason; it refuses nothing on scope grounds, while
every other check still binds. It starts with briefs carrying scope blocks, not older ones. The record-wide shadow check
of §11 is separate.

A deployment MUST NOT set `enforce` until §13's six cases are settled and the Owner has reviewed and fixed false
refusals; the hail reader refuses that setting meanwhile. Test fixtures may use enforce to test the settled rules. Once
it is allowed, a non-control hail without `brief` is refused. Enforcement is at the sender; an older reader may ignore
`brief`. The check runs once `hail send` exists.

## 9. Signing: which key wrote it

Signing and sealing use separate keys. A signing key MUST NOT encrypt. Signing gives integrity and the identity of the
key against any host. It proves a bot only where no other bot can read its private half or make it sign: through
separate system users, a chip that gates use, or a private store isolated from other bots. A shared team key proves the
team. History that another bot can change or roll back gives no duplicate or replay defence against that bot. All
sessions of a bot share its keys; a signature never proves which session used one (sealanes/1 §12).

**When.** A hail needs no signature to be a hail; signing is a step the owners of a record turn on.
The following conditions apply word for word:

1. Every hail's step is shown by every tool and view, 'by the forge's access alone' or 'signed', and the first is never shown as the second.
2. Where a roster governs a record, the roster turns signing on and it is required there.
3. Signing, once on for an owner, is turned off only by that owner's signed commit.

Without a roster, signing starts off under §8. A git committer name is not proof of authorship. With
signing on, each hail of that owner must be introduced by a verified signed commit whose signer is
listed for its `from` bot; the complete hail bytes are covered by that commit. A present detached hail
signature must also verify. A record using a roster additionally requires the detached hail signature
and its namespace below. A missing required signature refuses filing; it does not change the fact
that the bytes have hail form.

A roster cannot turn its signing off while it governs the record. The former team flag is now fixed
true; omission means true, and false or a non-boolean is refused. Without a roster, an owner's `signing-key` is that owner's public key for settings changes, and
`keys` binds the owner's bots to their public signing keys. The transition that turns signing on
includes these keys. Another owner's key, a bot's key or an unsigned commit cannot turn that owner off,
including by removing its table, removing a key, setting record-wide signing off or replacing the
settings carrier. Verify such a transition under the prior state's owner key before counting it.
The host's signed commit is needed as well for a shared change; it cannot replace the affected owner's
signature. If the commit format cannot carry both required authorizations, keep signing on and change
only an owner-specific table with its owner's signature. Adoption of a roster never downgrades signing.

The required signing step is shown separately from its verification result. Only successful
verification is displayed as `signed`. An unchecked signature is shown as `not checked`; a missing
or failed required signature is shown as a refusal, never as verified signing. Use `signing required`
for the policy requirement, alongside `signed`, `not checked` or `refused` for its result. This does
not label a failed or unchecked required step `by the forge's access alone`.

A form-only view with supplied filing settings shows the required step and `not checked` when it
has not verified signatures. With no settings it says `step not checked`, never guesses either
step. An earlier unsigned hail retains `by the forge's access alone` after the owner turns signing
on. A later required signature that is missing is a fault, not a downgrade to the earlier step.

Every person's roster hail remains signed: the Owner by an enrolled signing key and a decider by its
own signing key. DECISION, START, AWAY, BACK, STOP, FORCED END, seen and delivery retain their applicable
signature rules. A signature present on any hail must verify even where signing is off.

**Keys and state.** Bot keys are sealanes/1's `keys.signing`, including `keys = "team"`, with its allowed key types. A
decider has its own `keys.signing`. Hail derives allowed-signers data in memory on each run; no check writes a file. A
change of a bot's or person's key, or a signing flag, counts at the commit whose state counts under sealanes/1. A hail
filed before that event verifies against the old state; one filed after meets the new one or is refused and sent again
signed. Sending unsigned before the flag and filing after it does not avoid it. A new act on an old hail always needs
current rights.

**The Owner's keys** are the trusted allowed-signers file named by `owners.<id>.signing-keys`, or its hash-checked bytes
in a lock. Sealanes/1 §3.5 governs the whole file: its UTF-8, key types, device comment, enrollment, revocation,
rotation and recovery. A missing device comment means `desktop`; `# device: desktop` is the sole explicit device. A
`phone` or other device line refuses that file. A change counts only by the Owner's own signed commit, read with Table
1, ordinarily together with its roster hash. No DECISION matches or applies that change. With no enrolled key, first
enrollment and recovery follow sealanes/1's in-person rule; they require no Board where there is none.

**Formats and namespaces.** Hail uses the SSH signature format, not one program's implementation. An ordinary hail,
including every control record and the Owner's delivery or "seen", is signed in `hail`; a DECISION in `hail-decision`;
an Owner commit in `git`. A signature never counts outside its namespace. A key's allowed-signers `namespaces`
restriction, where present, is honoured. This edition adds no device-role labels or mandatory namespace list to the
Owner's core keys file: a missing restriction is read as the allowed-signers format defines it. The `from` of a person's
hail must match the roster key and, for the Owner, its `desktop` device data. A bot's key is matched to its bot, or
reported as team-shared where that is all it proves.

**Canonical bytes:** UTF-8 in NFC as Unicode 13.0.0 defines it, the version sealanes/1 pins; LF line endings, no
trailing spaces, from `hail/1` through `end hail/1`, with exactly one LF after it. A reader rebuilds that form before
verifying. The signature block follows at once and is not part of those bytes. A plain filed file holds this frame, then
the received signature block, if any, and nothing else (§3). The block's format is the armored SSH signature format. For
example `ssh-keygen -Y sign -n hail` writes it, and `ssh-keygen -Y verify` reads it; native implementations may read the
formats in memory.

The signed bytes hold `from`, `to` and `id`; for a sealed hail, also `sealed-to` and `min-class`. Signing remains needed
when sealing: each recipient holds the per-message key, so sealing alone proves no sender.

## 9a. Sealing: who may read it

- **What it gives:** confidentiality from every host that carries or stores the hail and never handles its plain text.
  It says nothing about the sender: that is §9's job. A platform that is also an endpoint, where a bot composes or reads
  its hails inside hosted prompts and tool calls, sees the plain text whatever is sealed in between. Sealing doesn't
  hide a hail from it; keep sensitive plain text out of such a platform: there, a bot MUST put sensitive content behind
  a source. The record can hold a plain hail where its sealing policy permits; notifications still carry only its reference.
- **When:** opt-in, per hail or per channel. Any channel whose host is not git's forge seals by default, by the roster's
  policy. A hail in git or on the local socket MUST NOT be sealed unless it opts in.
  - **The reference deployment's own exception** for one channel is in Appendix D, D.3.
  - **A channel whose sealing can't be done yet** holds its hail or refuses it. It MUST NOT send the hail unsealed in
    silence.
- **Levels:** S0, plain (signed when §9 requires it); S1, the hail is sealed. Whether an S2 that also seals the file
  behind the source is needed is open (§13).
- **The design:** multi-recipient key encapsulation, with the Owner as a standing, visible recipient. Not key escrow: a
  bot's private key MUST NOT be deposited.
  - A random key per hail encrypts the signed hail (AEAD), including its `sealed-to` line.
  - It is wrapped once per reader and once per Owner oversight key.
  - **The format:** age v1 with the named stanza types, with no custom cryptography. The `age` program is one
    implementation. It uses a random file key, a ChaCha20-Poly1305 payload, and one recipient stanza per recipient: an
    X25519 stanza for a bot's encryption key, and a `p256tag` stanza for each `age1tag` oversight key; K1 and R use
    X25519 stanzas. Hybrid chip recipients use the format for `age1tagpq` once the Owner rotates to them. Senders need
    plain `age`; only the reader of an oversight key needs its hardware plugin.
- **Keys:**
  - one X25519 encryption key per bot, apart from its signing key;
  - **the Owner's oversight keys,** by class:
    - **K1:** an age X25519 key behind a biometric or PIN gate (an OS keychain, a password manager, a passphrase file).
      It can be copied once unlocked, so it is the weakest class, and it MUST NOT be used where a chip exists;
    - **K2:** a key made inside the machine's own chip and tied to it, as an `age1tag` key, through that chip's `age`
      plugin, and gated by the chip's own biometric or PIN check;
    - **K3:** a hardware key you carry, an `age1tag` recipient now, or `age1tagpq` after rotation;
    - **R:** an age X25519 offline reading key, printed and kept away from any machine; not a signing key;
  - **every sealed hail goes to all of the Owner's oversight keys at or above its minimum class, and always to R where R
    exists,** so no class ever rests on a single key. A sealed hail is only as strong as the weakest key other than R
    that it goes to, so the setup's class is that of its weakest key other than R. The board, where there is one, shows
    it, and whether R exists. Without a Board, `hail send` prints both on every sealing and the Owner's digest lists
    them;
  - **at least two oversight keys,** one of which survives the loss of the main machine: a K3, another machine's K2, or
    R. Setup is not complete without them. R counts for every class;
  - **a minimum class per hail:** `hail send` applies the rule for the hail's class of record, for example "K3 only" or
    "K2 or better". The sender MAY raise it and MUST NOT lower it, and `hail send` MUST refuse when no key of that class
    exists. A minimum class MUST NOT be enabled unless at least two keys are eligible for it (keys at or above the
    class, plus R), at least one of which survives the loss of the main machine. A setup of one K2 and one K3, with no
    R, can't send "K3 only" until a second K3 or an R exists. Where a bot's key is not that bot's alone, a minimum class
    protects only hails with no bot among their readers;
  - **P-256 for chip classes now;** hybrid `age1tagpq` at the next rotation once the plugins support it;
  - keys rotate through the roster. Oversight recipients and their classes, the channel policy and each record class's
    minimum are in [the extension key table](ext-hail-keys.md). Sealing a decider's hail is open (§13): until it is
    settled, a decider's hail MUST NOT be sealed; it is sent and filed plain, and an overseer named by `seen-by` reads
    it whole. No new core roster key is added.
- **Order: sign, then seal** (§9). The signature is sealed with the hail, so the record host cannot read its sender from the span. A decider's plain record header below names its sender. This edition’s sealing policy is roster-governed and requires signing; sealing alone authenticates
  neither its sender nor its `sealed-to` line.
- **The filed sealed form:**

```text
hail/1 sealed
to-mailbox: <opaque id>
<age armor>
end hail/1 sealed
```

- **What the record host sees:** the opaque mailbox id, the size and the time.
- **Keeping the Owner a recipient:**
  - `hail send` MUST add the Owner's oversight keys;
  - the canonical bytes carry `sealed-to`, every recipient's age public recipient encoding, and `min-class` (§3). They
    are signed whenever §9 requires signing;
  - each reader's team filer files the sealed hail as received, as ciphertext. Filing creates no hail and no response:
    the protocol's responses are only those §4 requires. The
    Owner checks access by opening the filed ciphertext and reading the original hail, verifying its signature where
    signing is required or a signature is
    present, and otherwise recording it as unsigned. If that fails, the Owner looks at recipient inclusion, the
    identity's availability and the ciphertext's integrity before blaming the sender;
  - a reader also checks each `p256tag` stanza's 4-byte tag against the Owner's public keys in the roster. A tag is a
    consistency check, not proof that the stanza can be opened for that key.

- **No forward secrecy, by choice.** The record MUST stay readable by the Owner. That is why a leaked oversight key
  exposes everything sealed to it, why the classes above exist, and why oversight keys rotate.
- **Plaintext stays within the recipients.**
  - A sealed hail's plaintext MUST NOT be filed where anyone
    outside its recipients and the Owner can read it: not in the shared private repository, and not on a host.
  - The Owner reads the filed ciphertext with an oversight key.
- **The limit:** sealing hides a hail from the host and from other teams. It hides a hail from another bot only where
  that bot can't use a reader's key.

**Checking a sealed hail.** The reader's opening tool authenticates the exact sealed span and returns its opened bytes
as one result. It alone holds the private key. `hail check` holds no private key and opens nothing: given the span and
that trusted result, it checks the opened frame, keys and signature as a plain hail, then checks the count of recipient
stanzas against `sealed-to`, each tagged stanza against the named oversight key, and inclusion of the reader's key,
every oversight key at or above `min-class`, and R where present. These are consistency checks; bytes supplied outside
that opening step prove no binding to the span. Given the span alone it checks only its form, reports "contents not
checked", and no reader acts on it.

A `min-class` outside K1–K3 is refused, as is one with no eligible non-R oversight key. The minimum must also have at
least two eligible keys, with a surviving key as above. `hail check` cannot judge whether the sender chose the right
record class; `hail send` checks the selected record class and refuses an unknown one. No file behind a source is
sealed by this edition; sensitive content that must not sit readable on the forge stays outside the hail and its sources (§13).

## 10. The record

**Filing.** Every effective hail has a record. With no roster it is a regular file under
`hails/<owner>/`, one hail added per commit. With a roster, where a team declares `record`, every hail and receipt of that team is filed there by its `filer`, under its
existing `records.file` grant and sealanes/1 §3.6's checks. A bot's filing is a checked fast-forward of single-parent
commits confined to its allowed regular files. An Owner merge can also bring records onto the chain; that never grants a
bot a merge. A signed hail gives no push or file right. The protocol-named home decides its event: a byte-for-byte copy
elsewhere is evidence and creates no second DECISION, use claim or session event. A DECISION and its WILCO use claim are
filed in the asking team's record; a decider sends its hail to the addressed team's filer, and makes no filing of its
own. A decider's direct Owner-ask MUST carry `for-team`, naming one team within that decider's scope. That team's
filer files the ask and the Owner's DECISION in that team's declared record. A missing or out-of-scope team, or a team
without a record, refuses this route. A cross-team matter is read with the whole roster.

A bot without a file grant keeps each outgoing hail's exact bytes at `out/hail/<id>.txt`; the filer files it. Content
stays in `out/to-<bot>/`. A fully isolated workbench hands its bytes to its filer; its hand-back has no effect until filed. Outboxes
are not a second authority store. Every bot also keeps ids, canonical bytes and responses in history that later sessions
can read, before it answers or acts; filing first meets this. A filed hail MUST NOT be amended. A correction is a new
hail with `supersedes`. A sealed hail is filed as its sealed span.

**Record operation without a roster.** The record disables line-end conversion on every platform:
its `.gitattributes` contains `* -text`, and clients preserve committed hail bytes. Canonicalization
for checking does not authorize rewriting a filed file. The forge runs a check after each push,
reporting a malformed hail, a changed filed hail or a wrong filing path. Because it runs after the
push, this check reports and cannot block it; the sender's pre-filing check is what refuses filing.
The record can also hold its settings, attributes, explanatory README and that forge workflow; these
are operational files, not work products and not hails. Bots never execute a file merely because it
is in the record.

A reader shows an owner every newly landed hail addressed to that owner's bots, whatever its marker,
including ANSWER, DONE, RECEIVED and WILCO. New and owed are distinct lists; displaying an item marks
only its local display cursor, never its protocol obligation. Catch-up reads both. A clone starts
with no display cursor and shows the existing addressed record, or an explicitly chosen initial
boundary. A quiet watcher fetches without starting a model; every view shows the hail's signing step.

**Order.** A hail enters at the first commit on the remote default branch’s first-parent chain whose tree holds its file. A
workbench or side-branch copy is not filed. Order never comes from the written time or the side branch's age. At a
commit, first apply the state that counts there and any cap reduction. Then order its hails by their full
repository-relative paths as UTF-8 bytes. A record counts from that place in the order. Paths obey §3. Records in
separate repositories have no invented common order; §10b.12 gives the cross-repository absence observation and §10b.9
the use-claim observation. This edition gives no record-home handoff.

The earlier tie rule stays for distinct DECISIONs competing to end or replace the same ask, and distinct WILCO use
claims on one DECISION or standing-evidence ANSWER: if they enter in one commit, neither wins; both are refused and the
senders are told to answer or claim again. The same id and bytes remains a duplicate, not a competing claim. Path order
settles session claims and the relation of a lead's hail to AWAY/BACK, not these decision or use ties. `hail check`
reads the entering commit's committer time for expiry, future-time and use-age checks. That trusts the filer's clock:
git records no time at which a commit joined a branch. Filers push each filing at once.

**Moving a record home.** This edition defines no move of the hail repository. A proposed change of
home gives no new filing or replay right, even when the old home is quiet. An organisation that must
move first ends every STOP and owed REQUEST by the Owner's act in the old record. That preparation is
not a migration procedure: the old record and its original event identities remain readable, and a
later edition must define the move. No empty repository resets claims, duplicate ids or consumed uses.

**Session catch-up.** At its start, every session reads what its bot owes from the landed record:
unanswered questions and requests, accepted requests awaiting DONE or DECLINED, STOPs awaiting response,
and any explicitly requested receipt. It does this before matter work, including when no watcher has
rung. A missing or unreadable record is reported, never treated as an empty owed list. This duty applies
with either settings carrier; it creates no session claim or grant by itself.

**Matters and waiting work.** A matter has one stable reference, not a copy of its text:
- `tracker:<repository-name>:<tracker-id>` for a tracker, with the sealanes/1 tracker id; in a tracker folder that
  teams share, which team holds it is sealanes/1 §4.2's, where its number's `ids` range decides;
- `ask:<repository-name>:<ask-id>` for an ask; the tracker tool supplies its stable ask id;
- `thread:<hail-id>` for a hail thread, using its first hail's id;
- `plan:<team>` for the named team's plan, held by its lead. The exact team key is required;
  bare `plan:` and an inferred sender team are refused. The same team-qualified form applies in `subject`.

A thread's matter is the `matter` its first hail names; with no `matter`, it is `thread:<first-hail-id>`. That filed
first hail fixes the binding. A CLAIM on a tracker covers the threads whose first hail names that tracker. A reference
to such a thread uses its bound matter for claim checks. For a non-opening hail, resolve each id in `re` to that hail's
bound matter. If all name one matter, the hail uses it; an explicit `matter` MUST keep that binding. If they name
more than one, the hail MUST carry `matter`, naming one of those bound matters. It continues only the referenced threads
with that binding; the other references are context. Its resolved matter also binds later references to this hail.
No thread is rebound or merged. Otherwise references are distinct: shared prose or a source creates no alias between
trackers, asks, threads or plans. A bare workbench copy creates no new matter.

Free-text `re` names no existing thread and infers no binding. A REQUEST or QUESTION with free-text `re` MUST carry
`matter`, any one matter reference, which routes it. Any other hail with free-text `re` may carry it; without it, the
hail binds `thread:<its id>` for later references and needs only START. None of them creates a thread claim. To open a
new thread, use `re: none` and the first-hail rule below. `hail send` and filing refuse a missing required matter, an
explicit matter outside the bindings an id list names, or an unreadable binding. They report "matter required",
"matter differs" or "binding unreadable", respectively.

Work waits where a matter is open for that bot and none of its sessions holds it, or a hail to it is unanswered. The
reader reads that from the record and trackers. Where those cannot decide, the starter judges and logs the waiting work
and running count with the START, as sealanes/1 §5.6 says. No waiting work means no START, including a first one. An
unreadable declared record is not a licence to guess STARTs or claims: the reader counts none and permits no work on a
matter. It may obey STOP and make §4's failure warning, or file the narrow startup exception below.

**Session records.** START, END, CLAIM, RELEASE and FORCED END carry `bot` and `session`. START also carries `matter`
and `waiting`; CLAIM and RELEASE carry `matter`. The sender's `from` identifies who made the record:
- START is from a starter whose `bot.wake` reaches the subject bot, or the Owner. This includes a decider's granted
  start; principals' starts are the Owner's alone. Role, kind limits, waiting work, cap, kind definition and skills
  follow sealanes/1 §5.6. The start log names the skills actually supplied, read at the organisation's state in force.
- END, CLAIM and RELEASE are from that exact bot and subject session with an accepted live START. RELEASE also requires
  its live claim on the named matter. They need no other hail grant; an exact retry is a duplicate.
- A session id is fresh for that bot; an id whose accepted START has ended MUST NOT be reused. A second START for a
  running id is refused; re-sending the same hail is only a duplicate.
- START does not claim its initial matter. In a recorded team a session works on no matter until its START and its
  claim have been filed and accepted, in that order. A claim is a CLAIM record or the first hail of a new thread under
  the sending rule below. START can be filed before the process is ready; it then consumes a slot until ended. It
  supplies no brief, no act and no tier beyond the bot's own.
- At each claim, including a new thread's first hail, at most one session of each bot holds the matter. A second
  session's claim is refused at its place in the order. In one commit the first by path wins. Two different bots may
  each hold the same matter.
- RELEASE ends only that session's claim on that matter. END releases all of its claims and stops its running count.
  Both take effect when filed. A later session may take over only after RELEASE, END or FORCED END has counted.

A starter or session sends its record to the team's filer. When the filer is itself the subject, its session files its
own START and CLAIM, even before it is counted; an uncounted session may file those two and nothing else. Its START
still has to come from a lawful starter, not itself, and filing still needs its bot's `records.file` grant. The Owner's
start in person is a START signed by the Owner's key and given to the session it starts for filing, on the same terms as
a filer's own START. The launcher may sign it, so no hand signing is needed. This does not grant another bot
`records.file`: where the started bot is not the filer, it hands the record to that filer to land. A record no session
can file, including a FORCED END of the filer's last running session, is filed by the Owner's own commit.

**Sending a hail.** In a team with a record, every bot hail needs the sending session's live, filed START, except
the narrow startup filing rule above. A non-control hail carrying `matter` also needs its live claim on that bound
matter, except a new thread's first hail (below). A receipt needs only START, even if it carries `matter`; a hail with
no `matter`, where the binding rule above permits its omission, also needs only START. The control markers keep their
own sender and claim checks above; CLAIM cannot need the claim it is about to make.

A new thread's first non-control, non-receipt hail (`re: none`) needs only START. Its filing claims its matter for the
sender's session, using the first-hail binding above, with the same one-holder and path-order checks as CLAIM. If that
session already holds the matter it keeps its claim; if another session of that bot holds it, filing is refused. Sending
alone claims nothing, and no further matter work precedes that filing. An ordinary response carrying `matter` needs
the sender's live claim; calling it a reply is no exception. These checks apply in shadow as well as enforce mode.

**Receiving a hail.** An order or question to a bot goes to the session that holds its resolved matter above, including
the matter selected from a multi-thread `re`. Where none holds it, the
hail waits in the record and the next session that claims the matter takes it. A named target session does not transfer
a claim. A STOP is made available to every running session, and the holder stops and answers (§4); another session may
record receipt but does not act on or take over the held work. The session's handover lists pending hails and owed work,
but only a filed RELEASE or end frees its matter.

**Forced end.** The sender needs `hail.stop` reaching the subject bot, or is the Owner. It names a session with an
accepted live START. With a live claim, it rests on a STOP addressed to that bot and filed in the team's record like
any hail. Its `re` names that STOP's id and its source names the filed STOP's file. The STOP MUST still be in force at
the FORCED END's filing: neither lifted under §6.3 nor superseded. The STOP's single `subject` item MUST equal the
claimed matter's reference in one of the forms above; shared prose or an inferred alias is no match.

The filer checks the trigger when it files the FORCED END. The window is the subject bot's `watcher-interval` in
`ext.hail` plus 900 seconds, the git window of §5, using the state at the STOP's filing. Use the STOP's filing in the
subject bot's team's record. The window starts at the later event in that record's order: that STOP's filing or the
filing that began the subject session's current uninterrupted claim on the stopped matter. A release and reclaim, or a
successor's claim after a new START, uses the new claim's filing; retaining a claim or retrying it changes no start.
Measure from that starting commit to the FORCED END's filing commit by their committer times, as the filer stamps them.
The full window
MUST have elapsed with no accepted response to that STOP from the holding session. Only a response whose `from` names
that exact bot and session and whose `re` names the STOP answers it for this trigger. Another session's receipt does
not keep a silent holder alive. A response filed or known to the filer before filing blocks the end even if late.
A retry resets no clock; urgency does not shorten the window. An unfiled retry is checked against the STOP and current
claim at its filing; a lifted or superseded STOP refuses it. An accepted end's identical retry is only a duplicate.
This leaves §5's ordinary per-bot clock unchanged.

Acceptance at filing is final. A later record check uses the STOP's status and the subject session's current claim at
that event: the STOP was in force and its item matched that claim's matter. It does not reopen the filer's timing or
known-response check or need a sender's send log. The filer is trusted for that check and its commit times. A response
discovered later does not undo the end.
With no live claim, a session may be force-ended at once: `re: none`, with no STOP prerequisite, since it has no item
to stop. Any requirement for a body reason belongs to the record’s grammar. From filing, all its claims are released, its slot is free, and
all its later hails fail the live-START check, "session ended". A recorded response to STOP is not proof that the item
stopped. A new status QUESTION or STOP follows the ordinary rules (§5).

**Cap.** A START that would exceed the bot's `sessions` at its ordered event is refused; that session may not start. On
a counted lowering of the cap below the running count, the latest STARTs first end in effect at that roster event: they
lose their claims and authority at once, as by FORCED END. No later action of them counts. The team's next filing
records those ends in an INFORMATION pointing at the counted roster event and a list of the ended sessions; this report
needs no second end event and does not delay their effect. The Owner files it if no lawful session can. No new START
counts until the running count is below the cap. In a repository that points, when a cap counts follows sealanes/1's pin
rules; no hail rule cures a stale lock or gives a bot reader resolving.

**No record.** A team that declares no `record` has none of these session records and cannot give an
unfiled hail effect. It must name a record before using hails for directions. Until then, each of its bots runs one session at
a time even if its roster cap is higher. Its starter judges running state and waiting work and logs each start,
including starter, bot, session, time, work and supplied skills, under sealanes/1 §5.6. The claim rules do not bind that
team. The required filing of a DECISION and its use claim still binds: without a record, that team cannot use the Owner
relay as an authority route. A tracker ask may use its own record instead (§6.2).

**Limit.** Sessions share their bot's identities and keys. No roster reader or forge can tell which session made a push,
filing or unsigned act; even a bot signature proves no session. Hail judges its records. A session acting with no filed
START breaks this rule, and its acts cannot be told from its bot's. Claims and caps are recorded authority rules, not
proof that an uncooperative process stopped.

**Outboxes and intake.** The intake line is the bot's own `for: owner` QUESTION in `out/hail/`; nothing extra is sent.
The digest sees only outboxes it can read. No readable line means no line seen, never no need raised (§10b.13).
Deployments use the record folders sealanes/1 declares. A user's Board view of hails remains open (§13); the reference
deployment keeps its own hails private (Appendix D, D.5).

## 10a. The watcher

- **What it is:** a small helper that pulls, and rings the addressed bot.
- **What it rings for:**
  - every hail to a local bot that §4 expects an answer to: QUESTION, REQUEST and STOP, and an
    INFORMATION, ADVICE or WARNING requesting a receipt;
  - an ANSWER, a DECLINED or a DONE to one of that bot's own open hails;
  - a DECISION to one of that bot's own Owner-asks, and the INFORMATION that hands one back (§10b, point 11), when it
    reaches the raising bot;
  - an overdue response.
- **What it doesn't ring for:** RECEIVED and WILCO, which ask nothing of the bot they reach. They go to a digest, so the
  bell isn't ignored.
- **It is stoppable by its owner's own means:**
  - a local process MUST record its process id and start time, refuse to start while a process with both lives, stop on
    its system's request to end, have a time limit, and never be stopped by name. A reused id alone blocks nothing;
  - a hosted platform's routine is stopped in that platform's settings.
- **Who needs one:** a bot needs a watcher only where hails reach it through git (§7). On one machine or one hosted
  service, a team’s bots notify by the nearest transport, then read the landed record. Its lead or principal watches its cross-team record; any other
  bot that receives by git needs a watcher too (§8).
- **Where it runs:** inside the bot it rings, as a background task of that bot's own session, so the bot is woken
  directly. It MUST NOT write anywhere but its own clone and its own state folder, MUST NOT fetch into another bot's
  repository, and MUST have a time limit. A local watcher stops by its own PID; a platform's routine, which has no local
  PID, stops in that platform's settings.
- **It rings the bot, and MUST NOT ring the Owner,** with one named exception: the Owner-ask relay (§10b), where a
  sender holding `hail.owner` rings the Owner for an urgent matter. The bell itself still rings bots only. Apart from
  the relay, the Owner gets a digest at most. Together with the return grants (§8), a lead or principal answers the
  other team without waiting for the Owner.
- **The Owner's digest** lists, from the relay (§10b), each sender shown as a surface shows it (§10b, point 6):
  - every matter that doesn't ring;
  - each sender's rings, which the Owner can mark "not urgent" with one tap; nothing changes a sender's rights
    automatically;
  - the asks, counted by `kind` and by sender, with each gate's answers and rejections and their originals;
  - once a week, a section listing every rejection, and every answer from standing evidence, each with its original: all
    of them, not a sample (§10b, point 2);
  - every intake line, and "not disposed" for one left without a relay, answer or rejection (§10b, point 13);
  - the acting bot's WILCO, DONE and DECLINED "can't act";
  - "answered, not yet reached the bot";
  - each surface whose "shown" for an Owner-ask didn't come back within its window, as "unconfirmed" (§10b, point 6);
  - an urgent concern that rang twice with no "seen", first.
- **An example:** a fetch loop on the bot's own clone, which ends itself at its time limit.

## 10b. The Owner-ask relay

A need that only the Owner can meet travels out, from the bot that raises it to the Owner, and the Owner's signed answer
travels back, to the bot that acts. Both legs ride the record (§10); there is no new transport. A relay only notifies:
only the Owner's signature authorizes (§6.2).

**1. Who asks.** A bot never poses a need of the Owner in its own chat alone. It raises a QUESTION `for: owner` through
its permitted hops to the coordinator. The roster writer arranges the lead route; the coordinator is the first gate. A hail reader uses the grants, not an inferred team crossing.
While the coordinator is away each lead gates its own team, and without an organisation team it does so always. The
auditor sends directly to the Owner, and no coordinator gate receives its verdicts. Each hop is filed, keeps the
original file unchanged at its pinned revision, and has its return grant. Urgent or overdue hops may be skipped only along a route already
permitted by those grants; an overdue coordinator never gives its lead `hail.owner`.

A decider may send directly to the bots its grants reach, to the coordinator by `hail.message`, or to the Owner by
`hail.owner`. Using the coordinator is the decider's choice. A decider's request for the Owner is a normal QUESTION to
that coordinator or an Owner-ask directly, with `for-team` naming its asking team (§10); `for: owner` is the bot need's
form. The reply to a person follows §8. The
intake line of a bot is its own QUESTION in its outbox; no extra direct message to the Owner is created.

**2. The gate.** From its own context, the gate does one of three things, and files it. It replies to the `for: owner`
QUESTION with an ANSWER whose `disposition` (§3) names what it did:
- **`relayed`:** it relays the need, as an Owner-ask whose `re` names the bot's hail, and whose `raised-by` names the
  raising bot (point 3). The ANSWER's source names the Owner-ask;
- **`answered`:** it answers the need with standing evidence (point 4). The ANSWER's source names that standing
  authority; or
- **`rejected`:** it rejects the need. Its body may explain the rejection; its source list may name evidence.

A bot between the raising bot and its gate that settles the need with standing evidence answers the same way, with
`disposition: answered`. An ANSWER to a `for: owner` QUESTION MUST carry `disposition`.

**The gates' audit.** Once a week, the Owner's digest lists every rejection, and every answer from standing evidence,
each with its original: all of them, not a sample (§10a).

A case belongs to the bot that filed its first relay, answer or rejection. Another session of that bot takes it only
through §10's claims. No absence transfers it to another bot. An ask a lead sends to the Owner while the coordinator is
away stays the Owner's after BACK until answered. A bot MUST NOT go round a rejection (§6.7).

**3. The Owner-ask.** Only a sender holding `hail.owner` sends a QUESTION `to: owner`: both principals, each decider,
and a lead while the coordinator is away, or always where the roster has no organisation team. `hail check` refuses
another sender. Each Owner-ask:
- asks for an Owner decision or act still needed within the roster's rules, never uses a DECISION to widen a grant;
- has `kind: allow-once`, `owner-act` or `decide`;
- has `why`, `clarify` and `decide-by`, and `raised-by` when it relays a bot's need;
- from a decider, has `for-team` naming a team in its scope, whose filer files the ask and the DECISION;
- points at the originals unchanged with their pinned file references; the one-line `why` says why the Owner must answer;
- on `decide` only, has two to five body lines `option A:` through `option E:`, sequential and each used once. Its body
  lines are its options only: its reason goes in `why`, its detail behind the source; prose beside them is refused.
  Other kinds have no option line and no hail/1 body budget. More than five options requires split asks;
- names the raising bot's surface first in `clarify`, then the gate's; for the sender's own need, its own address.

A desired grant or key change is an `owner-act` request for the Owner's commit. It carries no `roster` or `keys` header
and no permission-changing DECISION; the commit alone is judged by sealanes/1.

**4. Standing evidence.** A gate, or a bot between the raising bot and its gate, MUST answer a need only with standing
authority: a current roster grant, or a signed answer of the Owner's given as a standing rule, which on a DECISION is
`scope: standing` (point 7). It MUST NOT cite a DECISION whose scope is `once`, stated or by default. The raising bot
MUST check the cited evidence itself before it relies on it.
- **Citing a standing DECISION** doesn't use it up, and doesn't tie it to the later ask: it MAY be cited for every later
  need it applies to, by any gate, for any raising bot. Each citation is checked again: signed, filed and verified, in
  force, `scope: standing`, applying to this need, and within the runtime's limits and the roster's grants. A standing
  DECISION MUST NOT allow an act beyond the grants; widening them needs the Owner's own roster commit.

Before acting on a need settled by standing evidence, the bot files a WILCO whose `re` names the gate's ANSWER and whose
`to` is that gate, under its return grant. Only the first filed use claim acts, with §10's same-commit tie and §10b.9's
crash rules. This claims this need, not the standing authority: another need may cite it again. The session must also
hold the matter under §10. Across repositories, point 9's sender-head observation also applies to this use claim.

**5. Ringing the Owner.**
- **Urgent alone rings.** A sender holding `hail.owner` MUST NOT ring the Owner unless it judges the matter urgent, and
  marks its hail `urgent:` with the one-line reason (§3). Everything else goes to the Owner's digest. A ring passes the
  30-minute presence window and the hourly cap.
- **One reminder.** An Owner-ask that doesn't ring gets one reminder, 4 hours after it is filed, and one more only when
  talk lapses (point 7). A reminder that falls between 22:00 and 07:00 waits until 07:00, and is never skipped while its
  ask is open; one for an ask that has closed or expired is withdrawn (the cap, below). After that, only the digest
  lists it.
- **A concern** is bad news a bot raises, which may ask for no decision. The bot sends it hop by hop to its gate, as a
  WARNING, with `urgent:` (§3) when it is urgent. Only a sender holding `hail.owner` sends a concern to the Owner (§8),
  as a WARNING `to: owner` with `receipt: received`. A concern it relays from a bot carries `raised-by` (§3), naming the raising bot. A concern
  is never a STOP.
- **At night,** from 22:00 to 07:00, an urgent ring stays silent until 07:00, unless its hail carries `harm-by` (§3)
  with a time before that 07:00.
- **Checked at the moment it would ring:** these rules are checked on the Owner's device at the moment it would ring, in
  the Owner's time zone, as the Owner sets it.
- **The cap:** at most 6 notices in any rolling hour, counting everything but urgent rings, reminders included. The 7th
  MUST wait, and MUST NOT be dropped while its ask is open. A queued notice or reminder for an ask that has closed or
  expired is withdrawn, never delivered. Urgent rings are counted per sender.
- **"Seen" for a concern.** The Owner acknowledges an urgent concern with a signed "seen", which returns to the raising
  bot and its gate and closes every notice. The "seen" is a RECEIVED `from: owner <device>`, signed (§9), whose `re`
  names the concern. A "seen" carries no authority. With no "seen" 15 minutes after a ring with sound, the concern rings
  once more on the Owner's signing surface, then stays first in the Owner's digest.

**6. Fan-out: what each surface shows.**
- **All at once.** When an Owner-ask is filed, it is shown on the raising bot's own surface, on every session the Owner
  typed in within the last 30 minutes, and on the Owner's signing surface. Notices stay silent from 22:00 to 07:00
  unless `harm-by` names harm before that 07:00, and keep to point 5's cap.
- **An urgent concern** a sender holding `hail.owner` sends the Owner is shown at once on every surface, the signing
  surface included. Like every ring, it passes the 30-minute presence window and the cap; at night, its sound follows
  point 5.
- **Presence.** The Owner typed in a session when the Owner sent a real message there, and the session keeps that
  message's time locally. A tool call or an agent's text MUST NOT count as the Owner typing. A session that can't keep
  that time, or can't report it to the relay, has unknown presence. An Owner-ask goes to it too, since the Owner may
  have typed there, and the Owner's digest lists that session's presence as unknown. Only a session known to be outside
  the window is left out.
- **A card MUST carry only** who asks, the one-line why, where to clarify, the ask's options, the five answers (point 7)
  and a source. Who asks is shown as "`raised-by` via `from`" where the ask relays a bot's need. The originals stay
  behind the source, sealed where §9a says. A push MUST say only "an ask waits".
- **Rendered from the record.** A card is a view of the fetched landed hail, not a message carrying
  its text on another channel. The surface obtains and checks that record before rendering. Any
  notification to the surface contains only the record reference required by §7. A local shown
  status is observation data only and does not carry a direction or replace a hail.
- **Who, by id.** A surface shows who sends or asks by address: a bot's id `<team>.<handle>`, a decider's `person.<id>`
  or `owner`, which is what hail/1 reads and checks. It MAY show the bot's or the person's name or display name
  (sealanes/1 §4.2) beside the address, never in its place, and always marks a bot as a bot (sealanes/1 §4.4).
- **As data.** A surface shows an ask's text as data, fenced, and MUST NOT act on it.
- **Catch-up.** Every session lists the open Owner-asks when it starts; a signing surface does when it opens.
- **"Shown."** Each surface returns a "shown" receipt with the ask's id and hash. It is not a hail: it is one status
  line per surface, with the surface and the time, and it MUST NOT carry authority. A session or signing surface on the
  same machine has 15 minutes to return it; a session on a hosted agent platform, the `notice-interval` in
  `ext.hail.bots` plus 15 minutes, or 15 minutes where none is declared (§8). Each window starts when the relay first
  makes the ask available to that surface, and a retry doesn't restart it. A "shown" that comes after its window still
  records the surface as shown. The surface keeps its "shown" line in its own outbox, which the Owner's digest reads, as
  it reads intake lines (point 13). A missing "shown" means "unconfirmed"; "shown" never means seen. When no "shown"
  comes back within its window, the raising bot and its gate are told "unconfirmed", and the Owner's digest lists it
  (§10a).
- **A surface that shows only on its next turn** shows the ask then, adds an "answered" line after it once the ask is
  answered, and shows unknown presence and failed delivery as such.
- **The same answer everywhere.** Every surface, the raising bot's own included, offers the same signed answer, and
  filing an answer that ends the ask closes every copy. After `later` or `talk`, every copy shows the ask open, in that
  state. A surface that can't take a signature points the Owner to one that can.

**7. The five answers.**
- Every card offers five: **yes, or an option**; **no**; **own words**; **later**, until a time; and
  **talk** ("I'll come to your session"). Each is signed in one gesture.
- **Yes, or an option,** ends the ask, and may be acted on.
- **No** ends it, and MUST NOT be acted on.
- **Own words** end it, and authorize no act. The bot asks again with an option made from the words. Where they call for
  a grant or key change, the new ask is `owner-act`, for the Owner's commit; no bot applies it.
- **Later** keeps it open. A time past `decide-by` is flagged before signing, and the ask still expires at `decide-by`.
- **Talk** keeps it open for up to 4 hours. When they pass without an answer, talk lapses: the ask is open again, shown
  as waiting, with one reminder under point 5's night rule and cap. It too expires at `decide-by` (point 10).
- Only an answer that ends the ask counts as its first decision.
- **The DECISION's form** (§3):
  - `from: owner <device>`, where the device is `desktop` (§9); and an id `owner-<yyyymmdd>T<hhmm>Z-<key>` (§3);
  - `to:` the ask's `from`: the bot or person that asked;
  - `re:` the ask's id;
  - source #1: the ask's filed file, signature included, with its pinned file reference;
  - `answer:` is `yes`, `no`, `option <label>`, `words`, `later <time>` or `talk`, one for each answer above. `option
    <label>` names one of the ask's option lines by its label (point 3), and `later <time>` gives the time in
    `written-against`'s time form;
  - the Owner’s own words and reasons, if supplied, go in the body. A grammar alone may require prose;
  - it is signed (§9), in the namespace `hail-decision`.
- **Scope.** A DECISION carries `scope: once`, the default, or `scope: standing`. Only a DECISION that answers an ask of
  `kind: decide` MAY carry `scope: standing`. Only a standing DECISION may be cited by a gate as standing evidence
  (point 4).
- **The Owner signs, never a bot,** also in the bot's own session (§6.6).
- **Signing what is shown.** The Owner's signing tool fetches the ask by its id, checks its pinned bytes, renders it itself, and
  signs the ask's id, its committed source reference and the chosen answer, which the DECISION carries in `re`, source #1 and `answer`. It
  MUST NOT sign from a command or a link inside an ask.
- **By kind:** an `allow-once` yes permits the already-held use once; `owner-act` yes ends the ask but leaves its work
  "awaiting the Owner's act", which the bot never performs; `decide` takes the chosen option. A `yes` on a `decide` ask
  is refused; an option on another kind is refused. No, words, later and talk use the meanings above.

- **A STOP stays in force** except for §6.3's `allow-once` yes on an ask pointing to that STOP.

**8. Acceptance: one decision in force.**
- A DECISION counts once it is filed and verified in the asking team's record. Verified includes its binding to its ask:
  its `re` names the ask, its source #1 fixes the ask's filed file bytes (§3), and its `to` is the ask's
  `from`. A DECISION that fails any of these is refused.
- The first DECISION that ends the ask, on that record's default branch, wins subject to §10's tie rule; `later` and
  `talk` keep the ask open, and don't end it (point 7). A later one is refused unless it names, in `supersedes`, the
  decision in force at that moment. Only a decision that ended the ask is in force: a DECISION after a `later` or a
  `talk` needs no `supersedes`, and the first one after it that ends the ask is the first decision.
- **A change of mind** is a new DECISION that names the decision in force. It is allowed after the ask has closed, and
  from the Owner only. The first change filed that names it wins. A change that names an already-replaced decision is
  refused, and shown to the Owner. Work under a replaced decision stops at a safe point, and is reported. What is done
  stays done.
- A one-time DECISION (`scope: once`) is for its own ask, and for one use. A standing one is cited as point 4 says.

A DECISION on an open ask, later and talk included, must enter before `decide-by`, judged against its entering commit's
committer time. A change of mind naming the decision in force may enter after closing or expiry. A late first answer, or
another answer on a closed ask with no valid `supersedes`, is refused. The signing surface shows "not counted" and the
reason, and the digest lists it; a valid change of mind is shown accepted. Signing in time alone does not make a late
filing count.

**9. One use.** The acting bot verifies the DECISION, holds the matter and files a WILCO use claim before acting. Its
`re` names the DECISION. It goes to the bot whose INFORMATION delivered that answer under the bot's return grant; where
the acting bot asked directly, it goes `to: owner` under §8's reply rule. Only the first use claim filed in the
asking team's record acts; two entering in one commit both fail (§10). This WILCO is distinct from the session's CLAIM:
an ended or released session does not give a consumed decision a second use.
- A once DECISION lapses unclaimed after 24 hours from its filing commit's committer time. The bot asks again.
- Before acting, the bot checks that what the ask pointed at, followed to the raising need, is unchanged by hash. If
  not, it sends its gate a WARNING naming the DECISION and waits for a new ask.
- A later session that finds a use claim but no DONE MUST NOT repeat the act. It checks the outcome, or sends WARNING
  `outcome: unknown`, with `re` naming the DECISION, and waits.
- The act ends with DONE or DECLINED "can't act", and is shown in the Owner's digest.
- A decision asked by a decider that needs execution by a bot still needs a lawful delivery and return route. A bot's
  reply to the decider follows §8; it grants no new act or unsolicited reach.

Where a WILCO use claim's sender belongs to a team whose record is in another repository, the filer reads that record's
head afresh for acceptance. It co-files an observation beside the WILCO, at the hail's path plus `.session`, using
§10b.12's observation form, with exactly this line and one LF:

```text
sender-head: <full commit id>
```

This is filing data, not a changed signed hail. Acceptance and replay check the sender's exact bot and session for a
live START and claim on the need's matter at that observed head. The checker receives the observation, that head's
record chain and files, and their counted roster states and signatures. Missing or unreadable evidence, or an
observation known stale at acceptance, refuses the use claim. One use still counts only in its required home; this
observation creates no use in the sender's record. It also applies to point 4's standing-evidence WILCO.

The filer is trusted to read the head afresh; neither timestamps nor the observation prove freshness. A head change
seen before acceptance requires a fresh read and recheck, recorded in the observation; an ended START or released claim
there refuses the WILCO. The last such read is the observation boundary, not an atomic check across repositories. An
end after that read does not undo an accepted use, but the bot still needs its current live START and claim before
acting (§6.8, §10). Replay uses the recorded head, never today's head or a comparison of repository clocks.

**10. Lifetime.** An open Owner-ask expires at its `decide-by` time, including later or talk. A decision already in
force remains subject to point 8's change-of-mind rule. It MUST NOT expire with a default answer. A renewed ask
supersedes the old one, and names it in `supersedes`.

**11. Delivery back.**
- **The signing tool keeps a DECISION until it is filed.** It files it through the asking team's filer, within that
  team's push grants (§10), and retries. First it commits the signed file on a source branch of a listed repository; that is not yet a filing. To hand it over, it hails that filer with an INFORMATION, `from: owner
  <device>`, whose source names the DECISION, and the filer files the DECISION like any hail. That INFORMATION is a
  hail: it is signed in the namespace `hail` with the key of the device its `from` names (§9), and carries no authority
  of its own. The DECISION's signature is what counts. After 10 minutes unfiled, the signing tool shows "signed, not
  filed yet".
- **The relaying gate hands an accepted answer back** to the raising bot by an INFORMATION, whose `re` names the bot's
  QUESTION and whose first source names the filed DECISION at its commit. It keeps the answer until the bot claims it or
  declines. It resends after 30 minutes. After 2 hours, the Owner's digest shows "answered, not yet reached the bot".

**12. The coordinator away.** AWAY and BACK count only from the Owner's own signed commits into the organisation team's
record folder. Each is also a signed hail `from: owner desktop`, addressed to the coordinator, with `re` naming the
prior status record or `none` for the first. They need no Owner-ask. No bot's word or silence, the coordinator's
included, makes it away or brings it back. A team with no organisation team has no absence to judge: its leads hold
`hail.owner` by rule. An organisation team with no `record` has no AWAY, and its coordinator counts present.

In one repository, a lead's hail is sent while away only where its filing is after AWAY and before BACK in §10's chain
and path order. There is no clock comparison. The latest accepted AWAY or BACK decides; before either, present. An ask
already put to the Owner while away stays the Owner's until answered, even after BACK.
While the coordinator is away, the organisation team's other bots wait; they have no other gate.

Across repositories, the filer reads the organisation team's record at its head when filing the lead's hail. The latest
AWAY/BACK there decides. With that filing it keeps an observation file beside the hail, at the hail's path plus `.away`,
with exactly these lines and one LF after each:

```text
organisation-head: <full commit id>
status: <AWAY or BACK hail id, or none>
```

The observation is filing data, not a changed signed hail. Its head must be the head read for that filing; the record
check receives that head's chain, the status file and the Owner's signatures. Missing evidence, unreadable history or a
stale observation counts present. A later check replays the recorded observation, not today's head. The filer is trusted
to read the head afresh: no timestamp or sidecar can prove that it did. A live reader unable to read the organisation
record, or holding a stale copy, counts present and grants no lead `hail.owner` by absence.

**13. Intake.**
- **The intake line is the bot's own `for: owner` QUESTION,** kept in its outbox, and the Owner's digest reads each
  bot's outbox, so a gate can't hide a raised need. Nothing extra is sent. No bypass is created: the line is addressed
  to the bot's next hop, not to the Owner, and the need still waits on its gate.
- **"Not disposed":** a line with no ANSWER carrying a `disposition` after 4 hours, not counting 22:00 to 07:00, shows
  "not disposed".
- **Coverage.** The digest sees exactly the intake lines in outboxes it can read (§10). It doesn't see a hosted team's
  bots, whose outboxes are not on a machine it reads: that is the hosted-team limit, while no intake path reads them
  (§13). Nor does it see a fully isolated workbench, or a workbench that is unavailable. No line seen means no line
  seen, never that no need was raised.

**14. Three tracks.** An Owner-ask is followed on three tracks. An observation MUST NOT change the ask's state.
- **the ask** (authority): open; later until a time; talking; ended by its first decision; expired at `decide-by`;
  superseded by a renewed ask;
- **the bot that acts** (execution): claimed (WILCO); done (DONE); "can't act"; "outcome unknown"; "awaiting the Owner's
  act"; "answered, not yet reached the bot";
- **each surface** (observation): shown; unconfirmed.

**15. Before reliance.** Hail's tools are `hail check`, `hail send`, the signing tool, relay notices, the Owner's
digest, the watcher and presence hooks. No bot relies on the relay until these exist and pass §11's acceptance list:
- a tap in a bot's chat is consent inside that runtime for that bot's own act, and never closes an ask in another team's
  record;
- a decision with effects beyond the raising bot waits for the tools, or goes as an ordinary ask on the board's tracker,
  where there is a board.

## 11. Conformance (codebase and static analysis, not prose)

**`hail check <file>`** is offline and pure. It reads only explicit inputs and writes nothing that outlives its run. It
checks a hail or the roster state a hail relies on; sealanes/1 supplies the roster and keys change checks, not a
matching DECISION. The same inputs give the same result. It exits non-zero if any check fails and reports every fault it finds in one pass, never only the first. A
full authority verdict requires the inputs for every applicable row below. A form-only run may omit them, but reports
the checks not made and authorises no act. Allowed-signers data is derived in memory, never written.

**Filing and reading.** A failed check refuses filing. A grammar never voids a filed hail: record
readers report grammar faults under the grammar pinned at filing, keeping the landed record intact.
Changing a grammar later neither rechecks old hails under new rules nor erases their effect. A form
or authority failure keeps its own rules; grammar success never grants authority.

**One fix list.** Each fault is one line: `location: what is wrong; what to write instead.` A location
is `header <key>`, `body line <n>`, `ask`, or `source <n>`; setting and record checks name the field or
event. Body numbers start at 1 after the sources. Give the actual bad value and a concrete repair or
permitted replacement form, never just a code or a reference to this text. Where the reader cannot
know the right value, say how to supply it; do not invent it. Grammar codes follow their location as
`[code]`. No newline is embedded in one fault; escape a hostile value visibly.

Run independent checks even after failure. An invalid frame, count or unreadable section prevents only
checks that need that section. An invalid source label prevents citation matching for that source
block, not independent header or body grammar checks. An unknown opener prevents its meaning check,
not checks on other lines. Missing external inputs are separate not-checked reports, never a full
pass. Do not emit a cascade of guessed faults. State which dependent checks could not run.

Print header faults in their input order (missing required keys in the key-table order), source faults
in number order, body faults in line order, then ask faults; settings precede the frame, record-event
faults follow it. At one location use the order of this text's rules. Multiple independent faults at
one location each have a line. The aim is one fix list and a next filing that goes through.

The new form and source diagnostics use these repair forms, substituting the actual values:

| Fault | Repair line |
|---|---|
| count outside the range | `header sources: count N is outside 0 through 9; write sources: 9 or fewer and retain only those source lines.` |
| no source delimiter on an earlier form | `header sources: sources line is missing; replace the earlier-edition pointer line with sources: 0 or the required count.` |
| nonconsecutive label | `source n: label #m is out of order; write #n and update its citations.` |
| uncited source | `source n: source is not cited; cite #n in the body or ask, or remove the source and renumber.` |
| unresolved citation | `body line n: citation #m has no source; write the value here or cite an existing #1 through #9.`; use `ask` for an ask citation |
| unknown repository name | `source n: repository name X is not listed; use a listed name or have the record settings list X.` |
| absent commit in a reference | `source n: committed file reference is incomplete; write name:path@ followed by its committed revision.` |
| missing path | `source n: path is absent at the named commit; commit the file first and write that revision.` |
| required or wrong hash | `source n: settings require sha256; append sha256 H.` or `source n: sha256 differs from committed bytes; write sha256 H.` |

If an old pointer line is the sole attempted header delimiter, report its replacement as the single
missing-sources fault above; do not repeat the same repair as a retired-key fault. When a sources
line is also present, print `header pointer: this edition has one source list; remove pointer and
put any reference in the numbered sources.` A malformed source value skips lookup and hash checks
for that value; well-formed sources and independent fields still get checked.

Other form faults follow the same location/problem/repair convention, naming the required form from
§3. A `to` address lacking its bot component says `header to: X is not a bot address; write a listed
owner.bot or team.handle address.` An invalid written date says `header written-against: value is not
a calendar time; write a valid YYYY-MM-DDTHH:MM:SSZ time or numeric offset.` These are complete lines,
not a stop-at-first-error priority list.

| Check | Inputs beyond the hail |
|---|---|
| frame, header, conditional keys, duplicates, marker, body, options, ask and source form | this edition and the extension key table |
| sender, reach, return grant, signing flag, keys, edition and pin | sealanes/1 state in force, trusted bytes, reader kind (whole, resolving or slice), default-branch head and chain to a counted state; for a resolver, the organisation state, pin and lock |
| live versus record | live: the checking reader's state at its head; record: the entering commit and state that counted there; new action: current state as well |
| future written time | explicit reference time: the arrival clock, or entering commit's committer time; never the checker's own clock |
| sources and stale context | named committed bytes and repository tree entries proving paths and regular files; commit availability and scan policy; supplied lookup results, never fetched by the check |
| duplicate or reused id | the bot's readable history and team record, canonical bytes and earlier responses |
| DECISION, expiry, change of mind, standing evidence, one use | the ask's filed file and original need, decision in force, relevant filings and committer times, Owner keys then, claims and results, and current grants for an act |
| act check | brief and its hash, signed delivery, issuer's grant, prior briefs, kind definition for a principal, mode, fixed roster and clock snapshot, and exact final destination |
| session state, cap and waiting work | ordered team record, STARTs, claims and ends, each counted cap event, trackers or the starter's logged judgement, and starter's right; the starter also supplies its skills log under sealanes/1 |
| FORCED END | above, plus the filed STOP and exact item-to-matter match where required; at filing, both filing commit times, extension interval at STOP filing and responses known to the filer; record replay uses final filing acceptance (§10); no live claim needs no STOP |
| AWAY/BACK and lead's `hail.owner` | organisation record chain, Owner hail and commit signatures; across repositories the co-filed `.away` observation and its recorded head, plus the filer's fresh-read result |
| a decider or disclosure | person's current signing key and scope, received hail for a bot's reply, `for-team` and its record for a direct Owner-ask; for a disclosure, the ask's answer, marks, take-ups and hand-backs in force, its teams and expiry |
| sealed content | exact span, trusted opening result bound to it, reader's public recipient, roster oversight keys and policies; no private key |
| a roster or keys change | sealanes/1's chain, old counted state, candidate tree, identities, signatures, trusted bytes, pins and locks as applicable; no hail DECISION |

For FORCED END, supply the STOP's status at filing and the event that began the current uninterrupted claim. The two
filing times are §10's later STOP-or-claim starting commit and the FORCED END commit; the interval stays the one at STOP
filing. For a cross-repository use claim, supply the co-filed `.session` observation, its recorded chain and the filer's
fresh-read result (§10b.9). A proposed record-home move is refused under §10. Matter routing needs
the filed bindings of all `re` ids and any explicit `matter`; free text supplies no binding (§10).

The time check refuses a written time more than two minutes after its reference, and accepts exactly two. Given no
reference it checks the time's form only and reports that. It never refuses age alone. A re-send keeps its original
bytes; before a new act, the reader still checks current rights, session claim and any DECISION. The id minute
differing from the written minute is reported, not refused. The record-time check trusts the filer's committer time; it
catches a wrong clock, not a lying filer. Sealed content without its bound opening is not checked and is never acted on
(§9a). The check cannot judge the sender's record-class choice or physical key custody.

**`hail send <address> <path>`** takes a draft: header without `id` or written time, then the body and
required `ask`. It makes the id and time from one clock reading, obtains committed source bytes and adds a hash only when required or requested, reads the state in
force, builds the final frame, checks it and its actual destination, signs where required, seals where required, files it, and sends
only its landed reference by §7.
Checking, signing and sending are one step; a check of other bytes is no check of this send. It refuses:
- a failed form, source, current grant, signature, session or required scope check; the session check is §10's sending
  rule, including live START, matter claims, receipts and the first-hail filing claim;
- an absolute home-directory path in the header, body, ask or a source file: an absolute path under
  a user directory on any supported platform, including drive-letter forms, or the sending user's
  own home in any spelling. A tilde-relative path passes this particular test;
- a private id in a public-bound hail, in those same bytes. Prefixes and destinations come from the team's extension. A
  match is a configured ASCII-letter prefix ending `-`, then one or more ASCII digits, with no letter, digit or hyphen
  on either side, or at a line edge. Matching is case-sensitive. A malformed prefix refuses every public-bound hail
  until fixed; an empty list finds none. Private-bound text is not refused on this ground;
- a secret found in those bytes by the versioned secret-pattern set given as an input, or any pointed-at bytes it cannot
  obtain or verify. A pattern miss never relaxes §1's no-secret rule;
- required sealing it cannot perform, an unknown record class, a class below policy, or no eligible oversight setup.

Both tools scan each directly named source file with the same byte policies. `hail check` takes the
committed bytes, tree entries, destination and scan policy as explicit inputs; it fetches nothing.
An optional source hash, when supplied, is checked. Omitted inputs never give a full pass.

There is no defined form of an Owner quote to verify automatically: this is not a `hail send` refusal. A bare quote
still has no authority (§6.2). `hail check` is built first, then `hail send`; the build choice is open (§13). Until they
exist the bots follow the spec by hand. The reference rollout is Appendix D.

**Acceptance before reliance.** Hail's tools (§10b.15) pass the tests below end to end. Before a deployment's checker
refuses existing records, it makes a shadow pass over every filed hail and each refusal is reviewed. This rollout review
neither gives an invalid hail authority nor changes the current roster. Scope shadow mode is separate (§8a).

1. A builder's need travels by permitted hops and its lead to the coordinator; the verified answer reaches that bot.
2. An ask or option altered after filing is refused at signing.
3. An unfiled DECISION is kept, retried and shown "signed, not filed yet" after ten minutes.
4. An accepted answer not claimed or declined is resent after thirty minutes; after two hours the digest lists it.
5. Two sessions waking on one DECISION do not both act: matter claims and use claims are each checked.
6. A crash after a use claim never repeats the act; the next session checks or reports "outcome unknown".
7. Of two closing DECISIONs in separate filing commits, the first wins; in one commit both fail.
8. A change naming the decision in force wins; a change naming a replaced decision fails and is shown.
9. A renewed ask supersedes its old ask; an answer to that withdrawn ask is not acted on.
10. A delayed notice at night stays silent unless urgent with `harm-by` before that 07:00.
11. A seventh notice in a rolling hour waits; queued notices for closed or expired asks are withdrawn.
12. BACK changes no ask already put to the Owner while away; it stays there until answered.
13. A surface catches up on opening or session start and shows open and closed asks as such.
14. Both principals, a decider, an away-time lead and a lead with no organisation team may send an Owner-ask; another
    bot, or a lead with a present coordinator, may not.
15. A surface with recent or unknown Owner presence gets an ask; a known old one does not; the raising surface does.
16. Night and cap checks run on the Owner's device at ringing time, in the Owner's time zone.
17. A valid Owner signature verifies; a changed ask hash does not.
18. The filed DECISION has exactly the signed bytes and enters the asking team's record through a lawful filer.
19. The acting bot verifies the answer itself and still needs its current grants.
20. Closing, later and talk show the same state everywhere; a surface unable to sign points to one that can.
21. A gate ANSWER has `disposition`; its first source binds relayed or answered; rejected needs no prose reason.
22. A once DECISION cannot serve as standing evidence.
23. No and words need no prose reason under hail/1; words ends the ask and permits no act.
24. A `hail` signature fails on a DECISION; `hail-decision` fails on another hail; `git` fails on both.
25. One valid Owner signature suffices for a DECISION; `owner pair` and a second block are refused.
26. A DECISION grants no roster change: only the Owner's counted commit does, by sealanes/1 Table 1.
27. Owner keys change by that same table, including a same-commit hash change; no matching DECISION is required.
28. The Owner's signed INFORMATION delivers a DECISION to the filer; it adds no authority to that DECISION.
29. An undisposed intake line appears after four daytime hours; an unfiled answer appears after ten minutes.
30. A quiet ask gets one four-hour reminder and a talk-lapse reminder; night delays, cap and closure apply.
31. The Owner's signed RECEIVED closes a concern's notices and authorises nothing.
32. A tool call or bot text never updates Owner presence; the time is kept locally and reported to the relay.
33. Missing `shown` is unconfirmed after fifteen minutes locally, or `notice-interval` plus fifteen minutes hosted;
    retry does not reset it, and a late `shown` counts as shown.
34. `acting-for` and `handover` keys are refused; no silence or late principal grants another bot its work.
35. An unknown act outcome uses WARNING, `outcome: unknown`, and the DECISION id in `re`.
36. The weekly digest lists every rejection and standing-evidence answer with its original, not a sample.
37. The deployment's initial record-wide shadow pass reports every refusal for review before reliance.
38. A missing or unknown Owner-ask `kind`, missing `why`, `clarify` or `decide-by` is refused.
39. `scope: standing` on an ask other than `decide` is refused, not merely ignored as evidence.
40. Two to five sequential options A–E on `decide` pass; one, six, a repeated label, a body line beside the options, or
    options on another kind fail.
41. The hand-back INFORMATION names the raising QUESTION and pins the complete filed DECISION, signature included.
42. A person's hail signed with a different person's key is refused; the Owner's device is desktop only.
43. A surface stores its `shown` line with ask id, hash, surface and time in its outbox, with no authority.
44. A DECISION with wrong `re`, pinned ask file or `to` is refused.
45. Each decider's message, answer and brief reaches its granted bots without a forced coordinator detour; its message
    and answer may also reach the coordinator, but no decider directs the auditor.
46. A valid closing answer after later or talk needs no `supersedes` and is the first decision.
47. Standing evidence can serve later matching needs; the first WILCO on each gate ANSWER acts, not a second.
48. Replaced, inapplicable or once-scoped evidence fails; citing a standing rule consumes none of that rule.
49. Every act outside grants is refused even with an `allow-once` or standing DECISION.
50. Use claims in one commit both fail; separate commits select the first. No act precedes its filed use claim.
51. A once decision left unclaimed for twenty-four hours lapses; an ended session does not revive a used one.
52. Changed pointed-at context prevents acting and raises a WARNING for a new ask.
53. Talk lapses after four hours with one reminder; `decide-by` still expires it without a default answer.
54. A key revoked before filing refuses a hail; an already-filed hail verifies against its earlier counted state.
55. Concurrent sessions make distinct random ids without a counter; the sender prefix matches `from`.
56. Same id and canonical bytes is a duplicate; changed bytes is "id reused". Line ends and Unicode normalise.
57. Written time two minutes ahead passes; more fails. No supplied reference checks form only; age never fails. An
    offset outside hour 00–23 or minute 00–59 fails, as such a time does.
58. A new session resends the earlier answer unchanged and never repeats an act, including from local history.
59. Wake and watcher clocks use their declared longest gaps; sleep and urgent text create no absence or stand-in.
60. Each scanned location can trigger a secret refusal; unreadable or unverifiable pointed-at bytes also fail.
61. A side-branch copy before revocation is not filed; the filing event and current grants decide its later use.
62. One addressed bot's response stops its own clock only, never those of other addressees.
63. Live and record time inputs differ as stated; late arrival alone passes and brief age is checked before use.
64. A retry and a duplicate's old response keep exact bytes and do not reset a clock or bypass current rights.
65. Missing or unreportable Owner presence is unknown and included, not treated as absent.
66. The roster's edition decides; `hail@<commit>` differences are reported and grant nothing.
67. Unreadable history permits no act except a valid STOP; one team warning per failure, no reply to receipts or ANSWER,
    and DONE for STOP. A lifted STOP replay may hold again until its lift is known or made anew.
68. Duplicate keys, wrong-case keys or keywords, upper-case hex, and `re: None` fail; a hyphenated bot id reads whole.
69. `re` with one id-shaped part and free text fails; a complete `, ` id list passes form checking, with §10's matter
    rule still required; `owner` mixed with recipients fails.
70. An unknown non-reserved marker is INFORMATION, receipted only when requested and §8 permits a reply,
    and never acted on.
71. An unreadable span gets no reply; a readable malformed header gets DECLINED only with a listed sender, id and a
    reply permitted by §8. Canonicalisation precedes finding a frame with trailing spaces.
72. REQUEST and QUESTION need exactly one `ask`; every other marker forbids it. Without a grammar,
    neither five nor six body lines fails on line count.
73. A source fixes committed bytes, not converted checkout bytes; an optional file hash includes signature blocks
    and a `decision` hash excludes them. A file must already be committed. A workbench source fails.
74. Paths with `@`, `:`, spaces, empty, `.` or `..` segments fail; a wider name must be committed under a conforming path before it is cited. A bad filing path is refused before it enters.
75. All listed home-path forms fail in every scanned location on private and public routes; a tilde-relative path passes that test.
76. A bounded private-id match fails only for a public destination; malformed prefixes block every such send; an empty
    prefix list blocks none on that ground.
77. Every roster hail requires signing. Without a roster, each owner’s filing step decides. An unsigned
    hail filed before signing remains readable at its old step; one filed after the transition is refused.
78. A person's unsigned hail fails "not signed" under every roster setting; a decider's key and scope are checked.
79. A sealed hail lacks either `sealed-to` or `min-class`: refused. Both on an unsealed hail: refused.
80. Sealed bytes without a bound opening report "contents not checked" and permit no act; mismatched opening, recipient
    count, tag, reader key or required oversight recipient fails; a bound valid opening passes.
81. Minimum R, an unknown class, no eligible non-R key, too few eligible keys or no survivor fails. K1/R require X25519,
    K2/K3 a tagged chip recipient; the sender refuses a class below policy or an unknown record class.
82. Local watcher start checks both id and start time: a reused id does not block, a live matching process does.
83. A START from a lead or decider within its `bot.wake` passes; a grantless starter, a bot starting itself, a
    coordinator starting another team's non-lead, or any non-Owner starting a principal fails.
84. A start without waiting work or over the cap fails; unreadable trackers use a logged judgement, but an unreadable
    declared record permits no matter work. The starter supplies skills from the organisation state, not a lock.
85. START alone permits no matter work; an accepted CLAIM or new thread's first filing after START does. In one commit
    path order applies; an opener that would claim another session's matter fails.
86. Two bots may each hold a matter; two sessions of one bot may not. Their same-commit CLAIMs pick the first by path.
87. Filed RELEASE, END and FORCED END free claims as stated; writing or sending alone frees none.
88. A hail on unheld work waits for the next claimer. A target session never transfers a held matter.
89. STOP reaches every running session and ends none; its holder stops and answers. An unheld stopped item stays held
    back when claimed. Only the sender’s landed lift or the specified landed Owner allow-once yes lifts it.
90. At filing, FORCED END with a live claim needs a STOP still in force and the full window from the later STOP or
    current claim filing. A lift or supersession refuses even a stale retry; a reclaim or successor gets a full window.
    A known response from that exact holder blocks it, even late. Another session's receipt stops §5's bot clock but
    does not block this end. With no live claim it needs no STOP. Only the Owner may omit `hail.stop`.
91. FORCED END releases capacity and claims from filing; all later hails from that ended session fail.
92. A lower counted cap ends the latest excess STARTs at that event, before a later filing records them. A new START at
    the cap fails. A non-resolving reader's old pin retains sealanes/1's stated limit.
93. A filer's uncounted session files its valid START and CLAIM only; an otherwise unfileable record is the Owner's
    commit. The Owner's signed START may be filed by the starting filer's session; words alone permit no work.
94. A no-record team runs one session per bot, with starter logs and no binding CLAIM records; it claims no unfiled
    DECISION authority. A missing declared record is not treated as a no-record team.
95. Only Owner-signed AWAY/BACK in Owner commits count. Same-repository filing order decides the lead's route; missing,
    stale or unreadable evidence counts present; an organisation team with no record has no AWAY.
96. A cross-repository lead hail carries its filer's observed organisation head; replay uses that observation. A missing
    or stale observation gives no away right. A slice alone cannot check or act on a cross-team hail.
97. One team's mode does not change another's. Unknown extension keys refuse hail acts only, not other namespaces;
    declarations for teams omitted from a slice are not called undeclared merely for that omission.
98. An accepted REQUEST leaves the owed list by DONE, DECLINED "can't act" or sender withdrawal. No watcher computes a
    DONE deadline from prose; a follow-up QUESTION has its own clock.
99. Decider take-ups and hand-backs carried in hails change no answer by themselves; the ask's record decides.
    Outside-roster requests grant no act, and a disclosure is checked anew, recorded and never passes raw personal data.
100. The check makes no network read, clock read, key opening or persistent write. Each missing input is reported; a
     partial check never yields authority to act.

101. A FORCED END's STOP must be filed, still in force, named by `re` and pointed to, with its item equal to the
     current claim's matter. Test the full window from the later filing event of STOP and current uninterrupted claim,
     using that commit's and the end's committer times, not send or written times. Retaining or retrying a claim does
     not reset it. Replay uses that event's STOP status and claim; later lifts or responses do not undo acceptance.
102. In a recorded team, sending without a live filed START fails, except startup filing. A receipt with `matter`, or
     an ordinary hail allowed to omit it by §10, needs only START. A response with `matter` also needs the live claim
     unless it is a receipt.
     A new thread needs START and gains its claim only when its first hail is filed; competing holders fail.
103. The stated source count matches the following lines, numbered from #1 without gaps. Every source
     is cited in the body or ask; every citation names a source. Zero sources pass. Ten sources, #0,
     an uncited source, an unknown citation and an earlier-edition pointer line fail new filing.
104. A decider's sealed hail fails: `hail send` refuses it and filing refuses it, since sealing a decider's hail is open
     (§13). A decider's plain hail is filed whole and is readable by `seen-by`.
105. A bot may send the five allowed replies to a person's verified hail addressed to it, with matching `re`, without
     a person `to` grant; a reply to the Owner also needs no `hail.owner`. A wrong recipient, unrelated `re` or
     unsolicited message gains no right from this rule. The reply is filed in the bot's team's record.
106. A decider's direct Owner-ask needs an in-scope `for-team` with a record; missing or out-of-scope values fail.
     Its filer files both ask and DECISION there. A copy in another team creates no second decision.
107. A thread's first filed `matter` binds it; a tracker CLAIM covers threads first naming that tracker. With none,
     its first id names the thread matter. Otherwise references stay distinct; prose creates no alias.
108. An Owner-started non-filer hands its signed START through the filer or the Owner's commit; it gains no filing
     right. Both principals in this organisation are filers. Other organisation bots wait while the coordinator is away.

109. A cross-repository WILCO uses its co-filed sender head for live START and matter claim checks, at acceptance and
     replay. Missing or unreadable evidence, or a known-stale observation at acceptance, fails. A changed head seen
     before acceptance is read again; an END or RELEASE there refuses it. An end after the last read does not restore
     a consumed use; acting still needs
     current START and claim. Same-commit use ties and the asking home remain unchanged, also for standing evidence.
110. A record-home move is not defined, even from a quiet record. Changing its repository does not
     reset history or create a filing right. Preparation ends every STOP and owed REQUEST by the Owner's act.
111. One or several `re` ids bound to one matter route there; a different explicit matter fails. Ids bound to two
     matters require `matter` naming one: omission fails "matter required", another value fails "matter differs".
     Send and filing both refuse. A valid choice routes only to its holder, or waits for its claim; a target session
     transfers nothing. A REQUEST or QUESTION with free-text `re` requires an explicit matter, any one; another marker
     may omit it and needs only START; none creates a thread claim. Unreadable bindings
     fail; `re: none` opens a new thread under case 85. No reply rebinds or aliases the referenced threads.

**The act check's tests** (§8a), through `hail send` in isolated enforce fixtures unless stated otherwise:
1. A brief for another bot, or a key that proves only a team, fails.
2. A brief lacking its issuer's signed delivery or in-effect `hail.brief` fails; the principal's matching kind
   definition uses the Owner's roster commit instead, and expires only when that definition stops counting.
3. A STOP or REQUEST outside the brief's subject fails even with a permitted recipient.
4. Changed final bytes or a different real destination are not sent under an earlier check.
5. An expired or replaced brief fails; an unsettled ordering or overlap is ambiguous, not permission.
6. Each §8a.5 control passes without a brief and outside its targets, with all other checks; another act does not.
7. A non-control hail lacking `brief` fails; a person's signed hail needs no bot brief.
8. Shadow logs every scope failure without refusing on that ground; it never skips a grant or signing refusal.
9. A missing required subject fails in enforce and is logged in shadow.
10. A once-approved use with a missing or wrong DECISION id or canonical hash fails; a matching approval adds no act.
11. The canonical DECISION hash stays the same regardless of its signature bytes; a file source hashes both. A
    deployment setting enforce while any of the six open cases remains is refused; a fixture setting it is test-only.

## 12. Examples

Appendix C gives current no-roster examples. Appendix B illustrates roster roles and extension
settings, not an adopted roster. The current vector suite supplies further form and settings cases;
its compatibility corpus retains the previous draft's signed and sealed fixtures at their text pin.
No modified frame is presented with an old signature. A frame alone never proves authority.

## 13. Open

This is the complete list of points still open or deferred by this edition, with what happens meanwhile. A planned
feature creates no right. The public change list records the sections this draft changes.

| Point | Meanwhile | What waits |
|---|---|---|
| Exact-act hash | DECISION binds the ask id and filed file; the bot checks unchanged context before use | a separate exact-act form |
| Opened telemetry | surfaces report shown only | proof that a person read a card |
| Automatic grant proposer | only the Owner's roster commit changes a grant | automatic proposals |
| Other hail deadlines and ladders | only Owner-ask expiry and §5's first-response clocks are computed; DONE time is prose | other timed escalation |
| Owner's panel and user Board views of hails | §10b surfaces show asks; tracker asks stay on their own Boards; hails file in declared record folders | a shared panel form |
| Hosted intake | digest reads only reachable outboxes and states that limit | a path covering all hosted and isolated bots |
| Missing-shown notice component | digest says unconfirmed; raising bot and gate see it at catch-up; no stronger delivery claim | the dedicated notifier |
| Tool build choice | follow the rules by hand; no build language is authorised by this text | tool implementation and reliance on the relay |
| S2 for pointed-at files | no such file is sealed by this protocol; sensitive bytes stay outside the hail and its sources | sealing that content as a protocol form |
| Advisory level and trial results | a trial grants no authority; force follows the roster only | any further advisory semantics and a real-time relay tier |
| Hosted frame fidelity | send only the reference to the landed hail | a transport claim of byte preservation |
| Oversight software | use conforming formats and proven support; unsupported sealing holds or refuses | unverified chip or hybrid support |
| Tools and connectors | runtime permissions apply; kind skills follow sealanes/1 §5.6 | a broader enforced tool-only rule |
| Act check for pushes and filings | sealanes/1's grants and filing rules still bind | the separate git half |
| Owner-quote form | no automatic quote refusal; a bare quote has no authority | a format the sender can verify |
| Companion app and recovery signing role | no phone key, pair, end marker or app precondition in this edition | their later design; oversight R is unaffected |
| Sealing a decider's hail | not sealed: a decider's hail is sent and filed plain, and its overseers read it whole | a way for a person who oversees to read a sealed body, which needs a reading key sealanes/1 does not hold; the audit principal's sealing key in the roster in force; bots that no longer share one machine user, so a key sealed to is private to its bot |

**Six cases before enforce.** Each is an implementation choice the design has not settled. A deployment stays in shadow
and MUST NOT set `enforce` until all six have rules and cases:
1. Which event orders two briefs as newer; no writer may invent authority from a timestamp alone.
2. How `supersedes` relates to replacement by a newer brief.
3. Overlapping subjects, including two issuers, without one brief silently widening another.
4. Withdrawal of a brief, and when a reader knows it stopped counting.
5. Where `hail send` obtains one fixed snapshot of roster, briefs and clock for its final send.
6. The forms of brief ids and branch names in scope, and their interpretation beside sealanes/1's tracker ids; the old
   scope act for starting work cannot grant or brief a `bot.wake`.

First enrollment, recovery with no enrolled Owner key, roster form, return of a removed roster, presence storage,
ordinary committed-source forms, replies to people, decider ask homes, the START of an Owner-started bot that is
not its filer, matter bindings and the standing-evidence
use claim are no longer open: sealanes/1 and §§3, 8, 9, 10 and 10b settle them. This includes the active STOP and its
current holder's window, observed sender heads for cross-repository use claims, no record-home move,
and explicit matter selection for multi-thread replies and free-text orders and questions. A lost key that is still
enrolled is handled only
by sealanes/1's Owner change rule; this edition adds no bypass.

## 14. Security considerations

This section gathers rules stated elsewhere and adds none. The named section governs.

- **Authority and words** (§6, §8): grants come from the counted sealanes/1 state, not a hail, quote or stale worktree.
  A DECISION approves a use already held and never widens it. The Owner's own commit changes roster and keys.
- **Keys and identities** (§9): a signature proves its key, a bot only where other bots cannot use it, and never a
  session among the same bot's sessions. People sign always. Signing is required wherever a roster governs; otherwise it follows the owner’s recorded step. Namespace separation prevents using a signature for another purpose.
- **Root of trust** (§8, §9): sealanes/1's chain and Table 1 decide state. Before the first roster, and while no Owner
  key is enrolled, the forge's guard and the Owner's own gate are the remaining guard. A force-push can hide earlier
  chain history. A reader that cannot reach a counted state refuses rights.
- **Pins and slices** (§8): a non-resolving reader accepts what its pin holds until moved; a resolving reader refuses
  the changes sealanes/1 names. Cross-team hails need the whole roster. No text claims a stale lock sees a revocation.
- **Sessions** (§10): filed START and claim, ordered records, immediate cap reductions and FORCED END bound recorded
  authority. STOP holds work and ends no session. Shared keys leave unrecorded work indistinguishable from the bot's; a
  record rule is not a process-kill mechanism. A FORCED END trusts the filer's trigger check at filing; replay does not
  reopen its timing or known responses. That trigger needs a STOP still in force and a full window from the later
  filing of the STOP or current uninterrupted claim.
- **Absence** (§10b.12): only the Owner's records make the coordinator away; stale or unreadable means present. Across
  repositories the replay uses the filer's observation, trusting that filer to read the head afresh.
- **Replay and one use** (§4, §10b): history spans sessions; reused ids fail. A DECISION binds its ask's file and the
  answer; claims precede acts, same-commit use ties fail, and a crash never permits repeating an unknown outcome.
  Cross-repository use claims replay the observed sender head, trusting the filer's fresh read (§10b.9). No record-home move is defined (§10).
- **STOP with no history** (§6.3): it may hold work again after a replay until lifted; the exception only holds back.
- **Sealing and oversight** (§9a): a host that is also an endpoint sees plaintext. Separate keys, explicit recipients,
  minimum classes and a surviving oversight key bound access; opening is trusted for its exact binding to the span.
  Recipient tags alone prove no successful encryption. Unsupported sealing is held or refused, never silently plain.
- **Secrets and content** (§1, §11): sources hide no file from its readers. A secret pattern may miss a secret, so the
  sender's rule remains. Source faults refuse filing but do not erase a landed hail; the signing tool renders the ask itself, never
  signs from a command inside it, and surfaces show its text as data.
- **Deciders and outsiders** (§6, §8): a decider's signature has only its scope and grants. The ask's record decides
  take-ups and answers. A disclosure is checked at answering, adds no grant and never allows raw personal data.
- **Act check** (§8a, §13): it checks declared scope, not truth or wisdom. It starts in shadow, and enforce waits for
  six cases. A shared team key proves no bot, and the git half remains separate.
- **Consent and notices** (§6.4, §10b): consent is never relayed. Only urgent matters ring; night rules and the cap
  apply on the Owner's device. Intake and the weekly audit expose what readable gates disposed of, within the stated
  outbox limits, never claiming complete coverage.

## 15. Privacy considerations

This section gathers rules stated elsewhere and adds none. The named section governs.

- **What a host sees** (§9a): a sealed span's mailbox id, size and time; a host that is also an endpoint sees the
  plaintext. The signature is inside the sealed span.
- **The Owner's oversight** (§9a): every sealed hail names all required oversight recipients; no bot deposits its
  private key. The record stays readable by the Owner; no forward secrecy is claimed, and a leaked reading key exposes
  what was sealed to it. Key classes and rotation bound that risk.
- **Plaintext** (§9a): a sealed body is not filed where anyone outside the recipients and Owner can read it. Sensitive
  content on a hosted endpoint stays behind a
  source in a suitably restricted listed repository.
- **Cards and notices** (§10b): a card shows who (by address, any name beside it, a bot marked as a bot), why, where to
  clarify, options, answers and a source; originals stay behind it. A notice says only "an ask waits".
- **Presence** (§10b): the last real Owner message's time stays locally and is reported to the relay; tool calls and bot
  output do not count. A shown line holds surface, time, ask id and hash, and proves no reading.
- **Roster and people** (§8): sessions never enter the roster; deciders use opaque ids and signed identities. Their acts
  are kept for their stated overseers and auditor. Outsiders get public information unless a valid disclosure permits
  more, with raw personal data excluded (§6.9).

## 16. References

This is this specification's one reference list. The text uses these formats and procedures in its
own words and copies no standard's prose. A named source does not add a second settings authority.

- sealanes/1, edition one: roster state, grants, identities, pins, filing rights and change control.
- RFC 5234 and RFC 7405: ABNF notation and case-sensitive strings in Appendix A.
- Unicode 13.0.0: the pinned NFC normalization tables used for canonical bytes.
- The SSH signature and allowed-signers formats: detached hail signatures and namespace separation.
- age version 1 and the stated recipient stanza formats: sealing under §9a.
- TOML: record settings notation. The grammar companion has its own single source list.

## Appendix A. The frame's grammar in ABNF (informative)

This restates §§2–3, 8a, 9a and 10b in ABNF (RFC 5234, with RFC 7405 case-sensitive strings). The prose governs.
Semantic requirements such as unique keys, exactly one written time, sender-prefix matching, defined record state,
conditional fields and verified signatures are not weakened by this lexical grammar. Canonical bytes are UTF-8 NFC as
Unicode 13.0.0 defines it. A prose value below names an external format or one of §13's expressly open scope forms.

```abnf
hail             = plain-hail / sealed-hail
plain-hail       = open-line header *9source-line body [ask-line] end-line [signature-block]
open-line        = %s"hail/1" LF
end-line         = %s"end hail/1" LF
sealed-hail      = %s"hail/1 sealed" LF mailbox-line age-armor
                   %s"end hail/1 sealed" LF
other-form       = %s"hail/" 1*DIGIT [SP %s"sealed"] LF
                   ; form other than 1: section 2's readable-header refusal
header           = *header-line sources-line
header-line      = pair *(sep segment) LF
sources-line     = %s"sources: " %x30-39 LF
source-line      = "#" %x31-39 SP source-value LF
                   ; exactly the stated number, consecutive from 1
pair             = key %s": " value
key              = 1*(%x61-7A / %x2D)
sep              = SP %xB7 SP
segment          = pair / continuation
continuation     = component-text [remark]
value            = text-char *(WSP / text-char) [[SP] remark]
component-text   = 1*(WSP / text-char)
                   ; components contain no sep; a terminal remark is stripped
remark           = "(" 1*remark-char ")"
remark-char      = %x20-27 / %x2A-7E / %x80-10FFFF
                   ; no nested parentheses, no sep
line-text        = *(WSP / VCHAR / %x80-10FFFF)
text-char        = VCHAR / %x80-10FFFF
body             = *body-line
body-line        = body-text LF
body-text        = <line-text except exact end line or a line beginning ask:>
ask-line         = %s"ask: " text-char line-text LF
                   ; required exactly once on REQUEST and QUESTION only
signature-block  = <armored SSH signature block; one, in its proper namespace>
age-armor        = <age v1 ASCII armor, with the named recipient stanza types>

name             = 1*(%x61-7A / DIGIT / "-")
bot-id           = name "." name
person-name      = %s"person." name
opaque-name      = 1*(ALPHA / DIGIT / "." / "_" / "-")
session          = opaque-name
from-value       = bot-id SP session / person-name / %s"owner desktop"
address          = bot-id [SP session] / person-name
to-value         = address *(%s", " address) / %s"owner"
id-value         = (bot-id / person-name / %s"owner") "-" id-stamp "-" id-key
id-stamp         = 8DIGIT %s"T" 4DIGIT %s"Z"
id-key           = 6id-char
id-char          = DIGIT / %x61-68 / %x6A-6B / %x6D-6E / %x70-74 / %x76-7A
re-value         = %s"none" / id-value *(%s", " id-value) / free-text
free-text        = 1*(WSP / VCHAR / %x80-10FFFF)
                   ; no id-shaped comma part, no other-case none
supersedes-value = id-value
marker-value     = message-marker / receipt-marker / control-marker / %s"DECISION"
message-marker   = %s"INFORMATION" / %s"ADVICE" / %s"WARNING" / %s"QUESTION"
                 / %s"ANSWER" / %s"REQUEST" / %s"STOP"
receipt-marker   = %s"RECEIVED" / %s"WILCO" / %s"DECLINED" / %s"DONE"
control-marker   = %s"START" / %s"END" / %s"CLAIM" / %s"RELEASE"
                 / %s"FORCED END" / %s"AWAY" / %s"BACK"
reserved-marker  = %s"DECISION" / %s"INSTRUCTION" / %s"AWAY" / %s"BACK"
                   ; unknown non-reserved markers follow section 2

for-value        = %s"owner"
for-team-value   = name
                   ; decider direct Owner-ask only, in its scope, with a record
disposition-value = %s"relayed" / %s"answered" / %s"rejected"
kind-value       = %s"allow-once" / %s"owner-act" / %s"decide"
why-value        = component-text
clarify-value    = (bot-id / person-name) *(%s", " (bot-id / person-name))
raised-by-value  = bot-id
option-line      = %s"option " option-label %s": " text-char line-text LF
option-label     = %x41-45
                   ; 2 to 5 sequential options, on decide only
answer-value     = %s"yes" / %s"no" / %s"option " option-label / %s"words"
                 / %s"later " wa-time / %s"talk"
scope-value      = %s"once" / %s"standing"
outcome-value    = %s"unknown"
urgent-value     = component-text
harm-by-value    = wa-time
decide-by-value  = wa-time
bot-value        = bot-id
session-value    = session
matter-value     = %s"tracker:" repo-token ":" tracker-id
                 / %s"ask:" repo-token ":" opaque-name
                 / %s"thread:" id-value / %s"plan:" name
waiting-value    = component-text

brief-value      = brief-id SP sha256-hex
decision-value   = %s"owner-" id-stamp "-" id-key SP sha256-hex
subject-value    = subject-item *(%s", " subject-item)
subject-item     = matter-value / id-value / tracker-id / branch
                   ; a forced end's STOP uses one exact matter-value
brief-id         = <brief id, open under section 13>
branch           = <branch name in scope, open under section 13>
tracker-id       = 2*4%x41-5A "-" %x31-39 *DIGIT
scope-block      = %s"scope:" LF 1*scope-field
scope-field      = 2SP (scope-brief / scope-bot / scope-acts / scope-subject
                 / scope-targets / scope-until / scope-supersedes) LF
scope-brief      = %s"brief: " brief-id
scope-bot        = %s"bot: " bot-id
scope-acts       = %s"acts: " act-name *(%s", " act-name)
act-name         = %s"hail" / %s"stop" / %s"push" / %s"file"
scope-subject    = %s"subject: " subject-value
scope-targets    = %s"targets: " target *(%s", " target)
target           = bot-id / branch
scope-until      = %s"until: " wa-time
scope-supersedes = %s"supersedes: " brief-id
                   ; until absent only on a principal's kind definition;
                   ; supersedes optional on a brief, absent on that definition

wa-value         = wa-item *(sep wa-item)
wa-item          = repo-commit / forge-state / wa-time
repo-commit      = (repo-token / %s"hail") "@" commit
forge-state      = <non-empty text without sep>
wa-time          = 4DIGIT "-" 2DIGIT "-" 2DIGIT %s"T"
                   2DIGIT ":" 2DIGIT ":" 2DIGIT (%s"Z" / (("+" / "-")
                   2DIGIT [":"] 2DIGIT))
                   ; valid Gregorian date and time, second <= 59; Z or numeric offset; no fraction
                   ; offset hour <= 23 and minute <= 59, as the time's
model-value      = 1*VCHAR SP 1*VCHAR
sealed-to-value  = recipient *(%s", " recipient)
recipient        = <public recipient encoding of the applicable age type>
min-class-value  = %s"K1" / %s"K2" / %s"K3"
source-value     = repo-token ":" path "@" commit [SP %s"sha256" SP sha256-hex]
repo-token       = %x61-7A *(%x61-7A / DIGIT / "-")
                   ; a repository short name from the record settings
path             = segment-name *("/" segment-name)
segment-name     = 1*(ALPHA / DIGIT / "." / "_" / "-")
                   ; neither . nor .. as a whole segment
commit           = 7*64hex-lower
sha256-hex       = 64hex-lower
hex-lower        = DIGIT / %x61-66
mailbox-line     = %s"to-mailbox: " opaque-id LF
opaque-id        = 1*(ALPHA / DIGIT / "_" / "-")
```

## Appendix B. An example roster (informative)

This is content for an illustrative sealanes/1 roster, not a roster in force. All teams use the private repository
`https://forge.example/org/records`. Its source name is `records`, listed in each team’s record settings. An actual roster also needs the Owner's
identities, trusted file hashes and complete kind definitions under sealanes/1; this appendix claims no adoption or
executable roster fixture.

| Team | Role and filer | Place and record | Other bots |
|---|---|---|---|
| org | `org.coordinator`, principal and filer | tracker `trackers/org` (prefix OR), record `records/org` | none |
| audit | `audit.auditor`, principal and filer | tracker `trackers/audit` (prefix AU), record `records/audit` | none |
| a | `a.planner`, lead and filer | tracker `trackers/a` (prefix AA), record `records/a` | `a.builder-1`, `a.builder-2`, `a.reviewer-1`, `a.reviewer-2` |
| b | `b.planner`, lead and filer | tracker `trackers/b` (prefix BB), record `records/b` | `b.builder-1` |

Each bot has its corresponding kind. Each filer holds `records.file` for its own record folder, and no other bot holds
it there. Each bot holds `hail.answer` to its whole team, since a teammate may warn it. Both principals have
`hail.owner` by rule. Leads have it only during the coordinator's recorded absence. The auditor answers the coordinator
only on a workbench or brief and never takes its direction on findings.

| Bot | Additional declared grants |
|---|---|
| `org.coordinator` | `hail.message`, `hail.answer` and `hail.brief` to `a.*` and `b.*`; `hail.stop` to `a.planner` and `b.planner`; `bot.wake` for their lead kinds |
| `a.planner` | `hail.message` and `hail.answer` to `a.*`, `b.planner`, `org.coordinator`; `hail.brief` and `hail.stop` to `a.*`; `bot.wake` for its builders and reviewers |
| `a.builder-1`, `a.builder-2`, `a.reviewer-1`, `a.reviewer-2` | `hail.message` to `a.planner`, `hail.answer` to `org.coordinator`; no push and no file grant |
| `b.planner` | `hail.message` and `hail.answer` to `b.*`, `a.planner`, `org.coordinator`; `hail.brief` and `hail.stop` to `b.*`; `bot.wake` for its builder |
| `b.builder-1` | `hail.message` to `b.planner`, `hail.answer` to `org.coordinator`; no push or file grant |

`a.builder-1` and `org.coordinator` have `sessions = 2`; others default to one. A decider `m1` has scope `["a"]`, its
own signed identity and grants of `hail.message`, `hail.answer` and `hail.brief` for that scope; `hail.owner` comes by
rule. Bots reply to it as §8's replies to people allow. No decider is given a STOP or filing.

The extension below illustrates its declared types. The public recipient strings are retained
example data; they are not enrollment. K1 is permitted only without a chip, and R survives loss of
the main machine. A real deployment supplies its own signing and sealing keys.

```toml
[roster.ext.hail]
spec = "hail/1"
edition = 3
extends = { spec = "sealanes/1", edition = 1 }
oversight-keys = [
  { recipient = "age1609060eu4z03gcdlrxk4n5wws3ywe83z895rx4tgkkc5vp97pfvqaua0dn", class = "K1", survives-loss = false },
  { recipient = "age1e5sqhu32t2e9xng9f3fxwkrzz2tj02xnxze67d6hphrzxfy87pfsjejcsr", class = "R", survives-loss = true },
]

[roster.ext.hail.teams.a]
signing-required = true
mode = "shadow"
record-classes = { default = "K1" }
private-id-prefixes = []
public-destinations = []

[roster.ext.hail.teams.b]
signing-required = true
mode = "shadow"
sealing = { hosted = "plain" }

[roster.ext.hail.bots."a.planner"]
watcher-interval = 60

[roster.ext.hail.bots."b.planner"]
watcher-interval = 1740
wake-interval = 0
notice-interval = 0
```

All omitted extension values take the key table's defaults. A roster requires signing. These
illustrative settings grant nothing; a real roster also needs its complete core identities, keys,
grants and counted adoption. A start supplies no brief and no review tier. A bot's key proves no
particular session.

## Appendix C. Examples (informative)

The examples use a record with no roster, no grammar and signing off. Its listed bots are `a.lead` and
`b.lead`. Its repository names and addresses are supplied by §8's settings. These frames illustrate
form, not adoption or authority. A view labels each `by the forge's access alone`.

A question with no source:

```text
hail/1
from: a.lead s1
to: b.lead
id: a.lead-20261127T1000Z-123abc
re: none
marker: QUESTION
written-against: 2026-11-27T10:00:00+00:00
sources: 0
What is 4 plus 4?
ask: Give the sum.
end hail/1
```

Its answer:

```text
hail/1
from: b.lead s1
to: a.lead
id: b.lead-20261127T1001Z-234bcd
re: a.lead-20261127T1000Z-123abc
marker: ANSWER
written-against: 2026-11-27T10:01:00+00:00
sources: 0
8.
end hail/1
```

A result with one source, already committed in the listed `work` repository. The value is in the body:

```text
hail/1
from: a.lead s1
to: b.lead
id: a.lead-20261127T1002Z-345cde
re: none
marker: INFORMATION
written-against: 2026-11-27T10:02:00Z
sources: 1
#1 work:result.txt@aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
The result is 8, recorded in #1.
end hail/1
```

The repeated hex is illustrative; a source check needs the supplied committed tree and bytes, as
the vectors provide. This frame requests no receipt. A later edition's reader chooses the earlier
edition explicitly when reading an old pointer-line hail; it never rewrites its signed bytes.

The current-edition vector suite supplies further complete frames, file bytes and expected faults.
Earlier draft signature and sealing fixtures are retained byte for byte at their own text pin; they
are not examples to copy when writing a new hail.

## Appendix D. The reference deployment (normative for that deployment only)

These rules bind only this deployment: two working teams, A on one machine and B hosted, with the organisation and audit
teams and roles of Appendix B. They are not extra adoption conditions for others.

- **D.1 Force** (Status): the Owner's counted sealanes/1 roster commit names this edition. No trial, tool release,
  two-signature ceremony, Board gate or app supplies a second force condition. Before any roster has counted, the prior
  rules govern; after a counted removal, sealanes/1 refuses its rights and restores no old table.
- **D.2 Overdue ids** (§5): until a watcher exists, the next-turn check and team's evening tally list overdue ids.
- **D.3 Team B's hosted channel** (§9a): its `sealing.hosted` is plain by default, since the service is also its
  endpoint; an individual hail may opt in. Other channels keep §9a's defaults.
- **D.4 Keys** (§9): team A uses bot keys; where another bot can use them they prove only the team. Team B may use a
  core team key with each user's `keys = "team"`. That proves no individual bot for the act check. Each person's hail
  remains signed by its own key.
- **D.5 Private record** (§10): these bots' own hails stay in their private record folders, never in a public tracker.
  Public-text checks still apply to public output; no private ids or hashes are copied there.
- **D.6 Tools** (§11): after this edition, build `hail check`, then `hail send`. Before their first joint release, a
  session new to hail, on a different runtime from the builder's, gets only `QUICKSTART.md` and `--help` and sends and
  checks a hail within ten minutes, timed. The one page and help are checked against this spec.
- **D.7 Build choice** (§13): its own signed Board question follows this edition. The edition authorises no choice of
  implementation language and no work that needs a further grant.
- **D.8 Trial** (§7, §13): a six-day trial on two working teams runs on that first release. It grants no authority.
  Count round trips, Owner nudges and approvals, tokens, failures, restart cost, signed versus chat answers, tool-built
  versus hand-built hails, overdue receipts, pastes per day and protocol tokens against work tokens. Before day one,
  file the Owner's cut thresholds. Where hail costs more than it saves, the next edition cuts it down. The real-time
  relay tier waits for the data. Before team B joins, show its watcher waking its own bot, an ANSWER, DECLINED, DONE,
  digest-only receipt and overdue response, each once, without an Owner nudge.
- **D.9 Releases** (§13): one first release carries both tools after the ten-minute bar and before the trial; each tool
  remains a tracked item, and this release is not a patch. It does not wait for adoption or put a roster in force.
- **D.10 Forms and filers** (§7, §10): A's filer is `a.planner`, B's is `b.planner`; the organisation and audit teams
  have their own filers. A parent sends the landed reference to a child’s session address; that address grants nothing.
- **D.11 Sealing support** (§13): age v1 and the required recipient types govern. The source observations were for an
  `age` 1.3 implementation, chip plugins at 0.2.1 and 1.0.1, a carried-key plugin at 0.5.1 whose tagged support was not
  released there, and a native age implementation at 0.12.0. They are observations at those versions, not claims of
  current support. Before use, check actual output for the required type; an unsupported K3 or hybrid waits. No
  plugin-specific recipient stands in for `age1tag` or `age1tagpq` in a chip class.
- **D.12 Relay here** (§10b): use the time zone the Owner sets and the signing-keys path the roster names. The digest
  reads A's reachable outboxes and states B's hosted limit. Surfaces are the sessions and the Owner's signing surface.
  Hosted chat unable to sign points to that surface. Before the tools pass, §10b.15's reliance rule holds.

## Open questions

Every open point is in §13.

## Changes

The public list is [changes.md](changes.md), organized by section. It is the only change list.
