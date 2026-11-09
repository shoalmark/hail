# ⌜hail/1⌟

**Status:** hail/1, first edition. Not yet in force. hail/1 takes force in a deployment
only when two things hold: the Owner approves exact versions of the spec and the roster with two signatures, one on
the desktop and one in the companion app (§9), given through hail itself or through the board where there is one; and
the Owner then commits the approved roster, signed, on a default branch. Adopting hail/1 approves the whole roster, so
it is the gravest permission change (§10b). (The reference deployment's own rule for the time before both hold:
Appendix D, D.1.)
- **The decisions are chosen** (§0) and written into the sections they govern.
- **What stays open** is in §13.
- **Informative:** A six-day trial on two teams measures what hail/1 costs and where it fails.

**Abstract (informative).** hail/1 is a protocol by which agent seats message each other, under one person, the
Owner, who keeps the final word. It fixes a message's frame, its header, its markers and receipts, and what a message
may and may not claim, while content stays behind pointers named by sha256. A seat's hail carries no authority: the
reader computes authority from a signed roster of grants, and the Owner's words count only as the Owner's signed
answers. Hails take the nearest transport that reaches the reader, are signed once the roster's keys exist and
`hail send` signs, can be sealed to their readers and to the Owner's oversight keys, and are filed and never amended.
The rules are written per team and per principal, how far they reach is unknown, and hail/1 is not for swarms.

**Conventions.** The key words "MUST", "MUST NOT", "SHOULD", "SHOULD NOT" and "MAY" in this document are to be
interpreted as described in BCP 14 (RFC 2119, RFC 8174) when, and only when, they appear in all capitals, as shown
here. Where a rule is stated in plain words, without one of these key words, the plain words are the rule, and bind as
MUST does. §11's tests are how conformance is checked. Appendices A, B and C are informative: where they and the body
differ, the body governs. A deployment's own exceptions and rollout rules are in Appendix D, which is normative for the
reference deployment only; the body names the D item where each would otherwise sit.

**Terminology:**
- **Seat:** a role named in the roster as `<team>.<seat>`, such as `a.planner` or `b.auditor`; seats outlive sessions (§8).
- **Team:** a group of seats with one principal, filing in its own folder (§8, §10).
- **Principal:** the one seat of a team that hails other teams, and whose STOP reaches any seat of its team (§6.3, §8).
- **Owner:** the one person whose signed record is authority, which a seat's hail can only point at (§1, §6).
- **Roster:** the one signed file that says who may do what, with which keys (§8).
- **Hail:** each span from a line `hail/1` through the next line `end hail/1`, or its sealed form (§2).
- **Receipt:** one of RECEIVED, WILCO, DECLINED and DONE (§4).
- **Pointer:** the header line that names the content a hail rests on, in git or in a file named by its sha256 (§1, §3).
- **Transport:** the way a hail's bytes travel, which decides how fast a hail arrives, never whether it counts (§7).
- **Watcher:** a small helper that pulls, and rings the addressed seat (§10a).
- **Act check:** the check, inside `hail send`, that a seat's hail serves the brief it names, by the brief's scope
  block (§8a).
- **Scope:** two things. A brief's scope block is the block at its top that the act check reads (§8a). A DECISION's
  `scope` key says whether it answers once or stands as a rule (§3, §10b).
- **Digest:** a list read later, in place of the watcher's bell (§10a). A seat's digest holds the RECEIVED and WILCO
  that reach it; the Owner's digest is the most the Owner gets from the watchers, apart from the relay (§10b).
- **Filer:** the seat that files its team's hails and receipts, one writer per folder (§10).
- **Local socket:** the local channel between sessions on one machine whose hail bytes stay on that machine and never
  leave it (§9a).
- **Ask:** three things. A hail's `ask:` line is one line to the seat the hail addresses, on a REQUEST or a QUESTION
  only (§2, §3). An Owner-ask is a hail that asks the Owner (below). An ask on the board is a question put to the
  Owner there, which the Owner answers, signed (§6.2).
- **Owner-ask:** a QUESTION `to: owner`, sent only by a team's principal, or its stand-in while it stands in, for what
  no grant covers (§10b).
- **Raising seat:** the seat that raises a need of the Owner's, or a concern. An Owner-ask that relays a seat's need
  names it in `raised-by` (§3, §10b).
- **DECISION:** the Owner's signed answer to an Owner-ask, filed as a hail (§4, §10b).
- **Ring, urgent:** a principal rings the Owner, an alert with sound, only when it judges a matter urgent, and marks
  its hail `urgent:` with the reason (§3, §10b).
- **Concern:** bad news a seat raises, which may ask for no decision. It goes hop by hop to the seat's principal, and
  only a principal sends it to the Owner (§10b).
- **Surface:** a place where the Owner sees an Owner-ask: a seat's own session, or the companion app (§10b).
- **Companion app:** the Owner's app on the Owner's phone, which shows Owner-asks, notifies the Owner, and signs the
  Owner's answers with an Owner signing key (§9, §10b).
- **Stand-in:** the seat the roster names to stand in for a principal, with a stand-in filer (§10b).
- **Also used:** where there is one, the **board** is where the Owner answers asks on its trackers, each answer
  signed; hail works without it (§6.2). A **harness** is the program that runs a seat's session and holds its
  permissions; a **workbench** is a seat's own working folder; **git's forge** is the service that hosts the shared
  git remote. A seat's **definition** and its **brief** are defined in §1. The examples use **team A**, on one
  machine, and **team B**, hosted on an agent platform.

## 0. Decisions

| Decision | Chosen | Where |
|---|---|---|
| **How the oversight keys are held** | **Key classes.** Oversight keys are native `age1tag` recipients whose private half never leaves a chip: K2 (the machine's own chip) or K3 (a hardware key you carry). K1 (an ordinary key behind a biometric or PIN gate) MUST NOT be used where a chip exists. No user must buy a hardware key | §9a |
| **Recovery** | At least two oversight keys, one of which survives the loss of the main machine: a K3, another machine's K2, or an offline recovery key R | §9a |
| **Platforms** | K2 on the common desktop operating systems where a chip exists; K1 elsewhere, shown as the weakest class | §9a |
| **A hail's minimum key class** | A rule by class of record in `hail send`; the sender MAY raise it and MUST NOT lower it; a hail MUST be refused when no key of that class exists. The hail goes to every oversight key of that class or better, and to R | §9a |
| **Post-quantum** | P-256 now; the hybrid `age1tagpq` at the next key rotation once the plugins support it | §9a |
| **When a hail is sealed** | **Opt-in,** with sealing as the default policy for any channel whose host is not git's forge. The reference deployment's exception for team B's designated hosted channel is in Appendix D, D.3 | §9a |
| **How the Owner's words carry authority** | The Owner's words MUST NOT carry authority except as **the Owner's signed answers,** given through hail itself or through the board where there is one. Quick words stay evidence until the Owner answers an ask | §6.2 |
| **Who may answer whom** | **Explicit return grants in the roster;** no protocol reply right | §8 |
| **How an act within the grants is checked** | **The act check.** Each brief starts with a scope block; each of a seat's hails names its brief; `hail send` checks the final hail against the scope; shadow mode first, with the mode in the signed roster. The git half is separate | §8a |
| **How the Owner is reached** | **Hop by hop to the principal, then the relay.** A seat that needs the Owner goes hop by hop to its principal, which relays, answers with standing evidence, or rejects. Only a principal, or its stand-in while it stands in, sends an Owner-ask; it is shown on the raising seat's own surface, on every session the Owner typed in within the last 30 minutes and on the companion app. The Owner's signed answer that ends the ask, filed and verified, closes it everywhere; `later` and `talk` keep it open | §6.7, §10b |

## 1. What hail is, and is not

- **A protocol, not a pipe.** It fixes a message's shape, its receipts, and what it may and may not claim. How bytes
  travel is §7's business.
- **A seat's hail carries no authority.** Authority is the Owner's signed record, which a seat's hail can only point
  at (§6). The Owner's signed DECISION is that record, filed as a hail (§10b).
- **Pointers, not copies.** Content lives in git, or in a file named by its sha256. A hail cannot drift from the record.
- **Small on purpose.** A body of at most five lines, and one ask. Anything longer goes behind a pointer.
- **No secrets.** A hail MUST NOT carry a secret, such as a credential, a private key, a token or a password: not in
  its header, its body or the content its pointer names. A pointer keeps content off the channel that carries the
  hail; it hides nothing from whoever can read the file it names (§9a).
- **A seat's definition and its brief.** A seat's **definition** is the file that defines the seat's role for its
  harness. A seat's **brief** is the task document the seat is given. Its scope block, at its top, is what the act
  check reads (§8a).

## 2. The frame

```text
hail/1
<header lines>
<body: 0 to 5 lines>
ask: <one line>            (only on REQUEST and QUESTION)
end hail/1
<signature block, if signed (§9)>
```

- **A hail is each span** from a line that is exactly `hail/1` through the next line that is exactly `end hail/1`,
  or from a line that is exactly `hail/1 sealed` through the next line that is exactly `end hail/1 sealed`.
  - A sealed span is opened under §9a. Its contents then follow these plain-frame rules.
  - A signature block that starts on the line straight after `end hail/1` belongs to that hail. On a permission
    change, a second block that starts on the line straight after the first belongs to it too (§9).
  - Everything outside a recognized span is ignored, for example a platform's wrapper or a greeting. Platforms may
    wrap a message in text of their own.
- **Several frames in one message are several hails.**
- **The form is the frame's line.** "/1" names hail's one form, the same for all seats and teams; it is not a version.
  - hail/1 grows only in ways its readers can live with: keys and markers they can safely skip. A key that carries
    authority is never one a reader skips (§3).
  - A revision of this text is named by its edition.
  - A change hail/1's readers can't read becomes hail/2, the next form.
  - A line of the form `hail/<n>`, or `hail/<n> sealed`, with `<n>` other than 1, opens a span the reader can't read.
    It answers DECLINED, "unknown form".

## 3. The header

**Grammar** (Appendix A gives it in ABNF, informatively):
- **A header line** is `key: value`: the key, a colon and exactly one space, then the value. A key is lower-case
  letters and hyphens. Keys, markers and the frame's own lines are case-sensitive, written exactly as this document
  writes them.
- **A line MAY hold several pairs,** separated by ` · ` (space, U+00B7, space). A segment that does not begin with a
  key continues the value before it, as in `written-against: a@1 · b@2 · <time>`.
- **Any component MAY end in one remark in parentheses:** a value, or a ` · ` continuation segment, such as
  `(verified on team B's host)`. The reader removes the remark from that component before validating it. A remark
  holds no ` · ` and no further parentheses.
- **The header always ends with the `pointer` line.** The body is every line after it, up to `ask:` or `end hail/1`.
  So a body line such as `note: …` MUST NOT be read as a header.
- **A reader ignores a header key it does not know,** except on a DECISION: a key on the Owner's signed record
  carries authority, so a reader MUST refuse a DECISION that carries a key it doesn't know (§2). A new key on a
  DECISION therefore comes only with hail/2.

| Key | Required | Value |
|---|---|---|
| `from` | yes | `<team>.<seat> <session>`: the roster's seat name, then the session that wrote it, as `a.planner ‹session›`. On the Owner's hails (a DECISION, a "seen", the signing tool's hand-over to a filer), `owner <device>`, where the device is `desktop` or `phone`; a DECISION with two signatures carries `owner pair` (§9, §10b) |
| `to` | yes | the same form, with no session needed. Several readers are separated by `, `. An Owner-ask, a concern sent to the Owner, and a principal's claim on a DECISION it acts on itself are addressed `to: owner` (§10b). A DECISION is addressed to its ask's `from` (§10b) |
| `id` | yes | `<team>.<seat>-<yyyymmdd>T<hhmm>Z-<key>`: the UTC date and minute the hail was written, then a key of 6 characters drawn at random from Crockford's base32 alphabet in lower case (`0`–`9`, and `a`–`z` without `i`, `l`, `o` and `u`), as `a.planner-20261108T0812Z-k8chqr`. No counter is kept, so each session and each device makes its ids alone. A sender MUST NOT reuse an id, except to send the same bytes again (§5). Every id carries its team, because seat names repeat across teams; a team's name is unique in its roster. The Owner's hails take `owner-<yyyymmdd>T<hhmm>Z-<key>` |
| `re` | yes | the id or ids this answers, or `none`. A free description MUST NOT be used unless the thing answered has no id, such as a pasted relay. On a DECISION, the ask's id (§10b) |
| `supersedes` | no | the id of the sender's own earlier hail that this one replaces. The reader treats the old one as withdrawn. On a renewed Owner-ask, it names the ask it renews; on a DECISION, the decision in force that it replaces (§10b) |
| `marker` | yes | one of §4 |
| `written-against` | yes | each `<repo>@<commit>` the sender relied on, any forge state it relied on (as `release v1.0.0: none`), and the time in RFC 3339 form with its numeric offset, as `date -Iseconds` prints it (for example, `2026-11-08T09:12:40+01:00`), with no `Z` and no fractional seconds. A reader also accepts the offset written `+0100`. The time is taken fresh for each hail: `hail send` writes it from its own clock when it builds the hail, and a re-send keeps it (§5). `hail check` judges it as §11 says. A sender MAY name the spec text it wrote against as `hail@<commit>`: it identifies that text, and changes no rule (§8) |
| `model` | no | the writing session's model and effort, as `<model> <effort>` |
| `decide-by` | on an Owner-ask | the time by which the Owner's answer is due, in `written-against`'s time form. At that time the Owner-ask expires (§10b). In this version it appears on an Owner-ask only: deadlines for other hails wait (§13) |
| `for` | on a need of the Owner's | `owner`, on a seat's QUESTION that needs the Owner (§10b). It MUST NOT appear on any other hail |
| `disposition` | on an ANSWER to a `for: owner` QUESTION | `relayed`, `answered` or `rejected`: what the gate did with the need (§10b) |
| `urgent` | no | the one-line reason a matter is urgent, on a `for: owner` QUESTION, an Owner-ask or a concern (§10b) |
| `harm-by` | no | with `urgent` only: the time harm comes, in `written-against`'s time form. At night, a ring sounds only when that time is before 07:00 (§10b) |
| `kind` | on an Owner-ask | `allow-once`, `grant-change`, `owner-act` or `decide`: the ask's kind (§10b) |
| `raised-by` | on an Owner-ask that relays a seat's need, and on a concern a principal relays from a seat | `<team>.<seat>`: the raising seat, whose need or concern the hail relays (§10b) |
| `answer` | on a DECISION | `yes`, `no`, `option <label>`, `words`, `later <time>` or `talk` (§10b). Own words and reasons go in the body |
| `scope` | no | on a DECISION: `once`, the default, or `standing` (§10b) |
| `roster` | on a permission change | `<sha256 before> -> <sha256 after>`: the roster file's sha256 before and after the change (§10b) |
| `acting-for` | while a stand-in stands in | `<principal>`, on each hail the stand-in sends for it (§10b) |
| `handover` | on a handover | on the stand-in's INFORMATION that hands its cases back, whose pointer names the list of open cases, with what the stand-in did on each of the principal's cases. Any value: the key itself marks the handover (§10b) |
| `outcome` | no | `unknown`, on a WARNING whose `re` names a DECISION whose act's outcome is unknown (§10b) |
| `brief` | on a seat's hail once the act check enforces, except its control acts (§8a) | `<id> <sha256>`: the brief the hail serves, and the brief's sha256 (§8a) |
| `subject` | on a seat's STOP and REQUEST once the act check enforces (§8a) | one or more work items, separated by `, `, in the scope's `subject` form: tracker ids, hail ids or branch names. Other hails MAY carry it |
| `decision` | on an act beyond a grant, beside `brief` (§8a) | `<id> <sha256>`: the DECISION's id, and the sha256 of its signed frame: its canonical bytes (§9), from `hail/1` through `end hail/1` with one LF after it, without its signature blocks |
| `pointer` | yes | one of: `<repo>:<path>@<commit> sha256 <hex>`; `<repo>:<path> sha256 <hex>` when the path is as at the reader's head; `<workbench>:<path> sha256 <hex>` for a file not yet filed; `none`. Several files go behind one pointer to a list of their sha256s, as a `SHA256SUMS`. On a DECISION, it names the ask's filed frame, with its sha256 (§10b) |
| `ask` | REQUEST and QUESTION only | one line. It MUST NOT appear on any other marker. Receipts MUST NOT carry an ask: a question goes as a QUESTION |

A hail MUST carry every key whose Required column reads yes.

**The written-against warning light:** if a repository it names has moved past the commit it names, the reader is told
what moved before it acts. This is the case of two hails that cross, such as a hold and a publish.

## 4. Markers

**Messages:**

| Marker | Means | The reader answers |
|---|---|---|
| INFORMATION | a fact for the record | RECEIVED, or any answer |
| ADVICE | a recommendation; the decision stays with the reader | RECEIVED, or any answer |
| WARNING | a risk to weigh now | RECEIVED, or any answer. A concern rung to the Owner: the Owner's signed "seen" (§10b) |
| QUESTION | needs an answer | ANSWER; a `for: owner` QUESTION: the gate's ANSWER with `disposition` (§10b); an Owner-ask: the Owner's DECISION (§10b). RECEIVED MUST NOT come first unless the answer can't come right away |
| ANSWER | answers a QUESTION | nothing |
| REQUEST | asks the reader to act within its own grants | either DECLINED, which ends it, or WILCO followed by DONE. RECEIVED MUST NOT come first unless the decision can't come right away |
| STOP | stop one named item, record why, and wait (§6) | DONE when stopped, or DECLINED "already done". RECEIVED MUST NOT come first unless stopping takes time |

**Receipts:**
- **RECEIVED:** the hail was read in full, and its pointer's sha256 checked where the reader could. It says nothing
  about having read what the pointer names. A RECEIVED `from: owner`, signed, whose `re` names a concern, is the
  Owner's "seen" (§10b).
- **WILCO:** will comply.
- **DECLINED:** will not. The next line gives one of these reasons, or another stated plainly:
  - `pointer differs: <hex>`;
  - `commit not found: <sha>`, after one pull;
  - `malformed: <first failure>`;
  - `not in the roster`;
  - `reserved marker`;
  - `unknown form`;
  - `id reused` (point 5, below);
  - `already done at <time>`, with a pointer to the result;
  - `can't act`, from the seat that would act on a DECISION (§10b).

  An act the reader's harness refuses is DECLINED, and MUST NOT be passed to another seat.
- **DONE:** finished. It carries a pointer to the result, or `pointer: none` and one line saying what changed. An
  accepted REQUEST ends with DONE; a declined one ends with DECLINED. An ANSWER MUST be used only for a QUESTION.

**Rules:**
1. **Receipts aren't receipted.**
2. **A receipt quotes the id it answers in `re`.**
3. **Every answer counts,** for token efficiency.
   - Any hail from an addressed seat whose `re` names the id counts as that seat's RECEIVED for its clock, and stops
     it (§5). It says the hail was read, not that its pointer was checked.
   - For a QUESTION, a REQUEST or a STOP, RECEIVED is sent only when the real answer must wait. A reader that can
     answer at once skips it.
   - For an INFORMATION, an ADVICE or a WARNING, RECEIVED is a full answer: it stops the reader's clock, and nothing
     more is due.
4. **A reader MUST NOT act on a REQUEST whose pointer doesn't verify.**
5. **Duplicates:** a duplicate is the same id with the same canonical bytes (§9), however its transport wrapped it. A
   reader knows the ids it has seen from its team's filed record and from its own history (§10), never from one
   session's memory alone, so a new session of a seat sees what earlier sessions saw. A reader that can't read its
   history doesn't act on the hail. It answers only where §4 asks for an answer and a return grant allows one, and
   then only with RECEIVED; it sends nothing for a receipt or an ANSWER (rule 1). It tells its team once, by one
   WARNING (§8), for as long as that failure lasts: no acknowledgment, other hail or later session raises another,
   and a re-send of it follows §5. Once its history is readable again, a new failure is warned of anew. A reader
   that gets a duplicate it has already answered sends its receipts again, unchanged, and doesn't act again. For a
   QUESTION, it sends its original ANSWER again. A hail that reuses an id the reader has already seen with other
   canonical bytes MUST NOT be acted on: the reader answers DECLINED, "id reused", and the sender sends it again under
   a new id.

**Reserved:** DECISION and INSTRUCTION. These name the Owner's signed record. A seat's hail carrying either one is
DECLINED, "reserved marker". A DECISION is the Owner's alone: the Owner's answer to an Owner-ask, signed with an Owner
signing key (§9, §10b). A DECISION without that signature is a seat's hail.

## 5. Overdue

**Clocks run per addressed seat:** one for each pair of hail id and addressed seat. A response from one addressed seat
stops that seat's clock, and MUST NOT stop any other seat's clock. A retry, a handover or an escalation keeps every
addressee still outstanding.

**Windows, until that seat's first response:**

| Transport | Window |
|---|---|
| a harness's own cross-session message, same machine | 15 minutes. A message is read only at the reader's next tool round, which may take minutes |
| a hosted agent platform's own agent message | the reader's wake interval, as the roster declares it for that seat (§8), plus 15 minutes. The interval is the longest gap between two of its turns. A seat that the message itself wakes declares 0, so its window is 15 minutes |
| across teams, through git | the reader's watcher interval, as the roster declares it, plus 15 minutes. The interval is the *longest gap between two polls*. For example, a watcher that polls every minute gives 1 + 15 = 16 minutes. A 15-minute routine that may start up to 7 minutes early or late leaves up to 7 + 15 + 7 = 29 minutes between polls, so 29 + 15 = 44 minutes |

- **A seat asleep within its declared interval is not overdue** by this clock, a principal included. An urgent
  matter keeps its own trigger for the stand-in (§10b, point 12).
- DONE is due by the time the ask names, if it names one. Otherwise DONE has no window.
- **When a response is overdue,** the sender re-sends once to that seat, with the same id and the same bytes. Then it
  tells its own principal. It MUST NOT go round the reader through another seat. Readers that already answered treat
  the re-send as a duplicate (§4).
- **A sender that is itself a principal** tells the Owner, through the Owner's digest (§10a). It MUST NOT ring the
  Owner, unless it judges the matter urgent (§10b).
- **A principal that is overdue** by this clock is stood in for by its stand-in (§10b).
- **Until a watcher exists,** the sender checks at its next turn. (The reference deployment's own rule for listing
  overdue ids: Appendix D, D.2.)

## 6. Authority

1. **The reader computes authority; the writer MUST NOT claim it.** A REQUEST MUST NOT be acted on outside the reader's
   grants (the roster, §8) and its harness's permissions. Neither can be widened by a seat's hail. An accepted
   DECISION that allows an act once lets the raising seat do that act once, beyond its grants, and never beyond its
   harness's permissions (§10b).
   - **The grants cover acts that leave the reader's own workbench:** messages, stops, spawns, pushes and filings.
   - **Work inside the reader's own workbench needs no grant:** reading, writing in its own folder, reviewing.
   - **The act check** (§8a) checks a seat's acts against the brief each names. It only narrows.
2. **The Owner's words MUST NOT carry authority except through the Owner's signature.**
   - **What counts:** the Owner's signed answer, whose signature the reader verifies against the Owner's signing keys
     (§9). It comes through hail itself, as a DECISION on an Owner-ask (§10b), or through the board where there is
     one. Hail works without the board. Where a deployment has both, the board's panel for hails shows hail's asks
     and answers them with the same signed answer; its form waits (§13). The board's own tracker asks stay the
     board's. One matter is asked in one place only: as an Owner-ask, or as an ask on the board's tracker, never both.
     A seat that would raise a matter already asked in the other place points at that ask instead. A quick word in
     chat stays evidence until the Owner answers an ask.
   - **What doesn't:** an unsigned filed word is evidence, not authority. Its date, its hash, and a seat's signature on
     the hail that carries it don't make it the Owner's.
   - **A bare quote carries no weight at all.**
3. **A STOP needs the roster's stop grant, and no Owner pointer.**
   - A STOP can only hold work back, so the reader obeys one from a seat whose grant names it, and records it.
   - **Lifting:** the STOP's sender lifts it with a hail that supersedes it. The Owner can lift any STOP, either by a
     signed answer (point 2), or in person in the reader's own session.
   - **Reach** (the roster): each team's principal MAY STOP any seat of its own team; across teams, a STOP MUST NOT go
     except from principal to principal.
   - **A sender withdraws its own REQUEST** with a hail that supersedes it, not with a STOP.
   - **A STOP stays in force** under a DECISION, unless the DECISION names it (§10b).
4. **Consent MUST NOT be relayed.** A hail cannot approve a permission prompt, change a setting or a seat's definition,
   or stand in for the Owner's act.
5. **The transport proves nothing about the sender.** A subagent's cross-session message, for example, leaves under
   its parent session's address. Only §9's signature can name the key that wrote a hail.
6. **The Owner in a seat's own session is that session's user, not a hail.** What the Owner types there is governed by
   the harness, and hail's rules don't apply to it. A seat MUST NOT pass it on as the Owner's word, except as point 2
   allows.
   - A tap in a seat's chat is consent inside that harness, for that seat's own act only. It never closes an Owner-ask
     in another team's record, and never stands for a DECISION.
   - The Owner always signs an answer, never a seat, in the seat's own session too (§10b).
7. **A need of the Owner's goes through the gate.**
   - **No seat poses a question for the Owner in its own chat alone,** where no one may be looking. A seat that needs
     the Owner MUST raise the need hop by hop to its principal (§10b).
   - **The principal is the first gate.** From its own context, it relays the need to the Owner as an Owner-ask,
     answers it with standing evidence, or rejects it with its reason and with evidence where there is any. Each is
     filed, so the Owner can audit every case the principal kept from them (§10a, §10b).
   - **A principal's answer never creates Owner authority.** It can only point at authority that already exists: a
     current roster grant, or a signed answer of the Owner's given as a standing rule (§10b).
   - **No seat goes round a rejection.** A seat whose need was rejected MUST NOT reach the Owner another way. It MAY
     raise the need with its principal again, with new evidence.
8. **A seat verifies a DECISION before it acts on it.** It MUST check that the DECISION's form is exact; that it is
   signed with an Owner signing key (§9), with both signatures where it changes a permission; that it is filed and
   verified in the asking team's record, and is the decision in force for its ask; and that the act stays within what
   was asked (§10b). For a one-time DECISION, it MUST also check that it answers the ask this seat's own need led to,
   and that no earlier claim has used it. A standing DECISION cited as standing evidence is checked as §10b, point 4
   says, and citing it uses nothing up.

## 7. Transports

**The rule:** the nearest transport that reaches the reader. The frame's bytes are the same on every transport. The
transport decides how fast a hail arrives, never whether it counts.

| Reader | Transport |
|---|---|
| a seat on the same machine whose harness has its own cross-session message | that harness's own cross-session message |
| a subagent | its parent's own channel to it (the reference deployment's form: Appendix D, D.10) |
| a session that wakes only when resumed, on the same machine | a local helper resumes it with the hail |
| a seat on a hosted agent platform, from another seat on that platform | the hosted agent platform's own agent message. Whether it delivers a frame byte for byte is open (§13). Where it may not, the sender MAY send a pointer to the hail's filed copy (§10) instead of the frame |
| another team, or another machine | a file in the shared private repository, read after the reader's next pull. Every hail across teams MUST go from a principal to a principal (a stand-in counts as its principal while it stands in, §10b): another seat's work crosses as a pointer in its principal's hail, and there is no courier form |
| a person's paste | the frame unchanged; it MUST NOT be wrapped. Where a tool would wrap it, a pointer to the filed hail MUST be pasted instead of the frame |

**Across teams or machines, every commit a hail names is on the shared remote before the hail is sent.**

**The doorbell:** a hail through git needs something to tell the reader to pull. That is a watcher (§10a). Where none
runs, it is a one-line nudge from the Owner, which MUST NOT carry the content.

**No outside service:** the simplest setup that works without new dependencies comes first: git, each harness's own
channel, and the watchers. Real-time delivery through a relay is a later tier; whether one is added is not decided in
this edition. (The reference deployment's own rule for deciding it: Appendix D, D.8.) That delivery tier is not the
Owner-ask relay of §10b, which rides the record. The companion app's notices are part of the Owner-ask relay, and a
push says only "an ask waits" (§10b).

## 8. Who may hail whom

- **The roster** (Appendix B gives an example) is the one signed file that says who may do what, with which keys:
  - who may hail whom, and who may answer whom (return grants);
  - who may STOP whom; any seat MAY send a WARNING to its whole team;
  - each seat's hail-signing key (§9) and encryption key (§9a);
  - the Owner's oversight keys with their class (§9a);
  - the sealing policy per channel (§9a);
  - the exact spec text it adopts, by commit and sha256 (Status). A reader reads every hail under the text that its
    roster in force adopts when it reads the hail. A `hail@<commit>` in `written-against` identifies the text the
    sender wrote against and changes no rule; where it differs, the reader reports the difference (§3). Over the
    record, a hail is read under the text of the roster in force when it was filed (§10);
  - the watcher interval of each seat that runs a watcher (§5);
  - the wake interval of each seat on a hosted agent platform: the longest gap between two of its turns, or 0 where a
    message wakes it (§5); and, where a relay's notice wakes it differently, that notice's own interval (§10b,
    point 6);
  - for each principal, its stand-in and a stand-in filer (§10b);
  - who may brief whom ("may brief"), each principal's seat definition as its own scope, and the act check's mode,
    `shadow` or `enforce` (§8a).
- **How the roster changes.** The roster that takes force first is the Owner's own signed commit (Status). A later
  grant change goes through a permission-change answer (§10b): a seat commits exactly the approved change, and the
  checks accept a roster change only when the Owner's signed answer matches it.
- **A sender checks it before sending; a reader checks it before acting.** An unlisted pair MUST be refused: DECLINED,
  "not in the roster", where a return grant to the sender exists; otherwise the reader records the refusal locally.
- **The roster's pairs govern seats' hails.** A hail from the Owner (a DECISION, a "seen", the signing tool's
  hand-over) holds no roster pair. A reader verifies it by its signature against the Owner's signing-keys file (§9),
  and by §10b's rules.
- **Answers need a return grant.** The roster lists, per seat, whose hails it may answer with §4's responses. A return
  grant gives no other right, and no unsolicited message.
- **Every route that needs a response has its return grant.** For each permitted message, STOP and WARNING whose marker
  §4 answers, the roster holds the reverse `answer` grant. `hail check` MUST reject a roster that lacks one.
- **Across teams, every hail MUST go from a principal to a principal,** a stand-in counting as its principal while it
  stands in (§10b).
- **A need of the Owner's goes hop by hop** (§10b). An urgent need, or one whose hop is overdue, goes straight to the
  principal, copying the hop it skips; that hail needs its roster pair like any other. Only a principal, or its
  stand-in while it stands in, MAY send a hail `to: owner`.
- **A hail passed on keeps its original.** A seat that forwards another seat's hail, or relays it to the Owner, points
  at the original unchanged, with its sha256. Its own words are only its summary, under its own `from`.
- **Seats outlive sessions.** Any session of the named seat MAY answer, and its receipt's `from` names its own session.
  Every handover lists each hail still waiting for a receipt, by id.

## 8a. The act check

A seat acts within its grants without asking anyone, and its grants are narrow. It can still be steered off its task,
by injected text, a misread brief or drift, and do something its grants allow and its brief never asked for. The act
check refuses that at the moment of sending: it checks each act of a seat against the brief the act names. This
section is hail's half of it.

- **What it never does:** widen a grant, stand in for the Owner's signature, or judge whether a brief is wise. A scope
  only narrows: an act the grants forbid stays forbidden, whatever the scope says.
- **The acts it covers:** every act of a seat that leaves its workbench (§6.1): a hail, a STOP among them, a spawn, a
  push and a filing.
  - hail's half, this section, checks hails;
  - pushes and filings are the git half, a commit trailer and a pre-push hook, which is separate (§13);
  - a spawn is checked by its harness's own hook where the harness has one. Where it has none, the spawn is logged,
    and the log is reviewed.

  The Owner's hails are no seat's acts, and carry no brief.

**1. The scope block.** A brief starts with a scope block, before its prose:

```text
scope:
  brief: <id>
  seat: <team>.<seat>
  acts: hail, stop, push, file
  subject: <item>, <item>
  targets: <branch>, <seat>
  until: <time>
  supersedes: <brief id>
```

Every field is typed (Appendix A):
- `brief`: the brief's own id;
- `seat`: the one seat the brief is for;
- `acts`: the acts it allows: `stop` for a STOP, `hail` for any other hail, and `push` and `file` for the git half;
- `subject`: the work items it covers: tracker ids, hail ids or branch names;
- `targets`: where it may push, as branches, and whom it may hail, as seats;
- `until`: when the brief is spent, a time in `written-against`'s time form (§3). A condition stays in the prose;
- `supersedes`, the only optional field: the id of an older brief from the same issuer for the same subject, which
  this one replaces.

The brief's issuer writes the block, within its own grants. A seat MUST NOT write its own scope. The prose keeps the
reasons; the block is what the check reads.

**2. Who may brief, and when a brief counts.**
- **"May brief"** is a roster grant (§8): it names the seats each issuer may brief. A principal briefs its own team;
  any other issuer, only the seats its grant names (Appendix B).
- **A principal's own scope is its seat definition,** which the Owner signs into the roster. A principal never briefs
  itself.
- **A brief counts only when** all of these hold:
  - a signed hail from its issuer delivered it, with the brief's sha256 in its pointer;
  - the issuer holds "may brief" for the brief's `seat`;
  - its `until` time hasn't passed;
  - no newer brief from the same issuer for the same subject has replaced it.

**3. Every act names its brief.** A seat's hail carries `brief: <id> <sha256>` (§3): the brief it serves, and that
brief's sha256.
- **Its subject:** a seat's STOP and REQUEST carry `subject:` (§3), listing the work items they are about. Other hails
  MAY carry it, and need not.
- **Its DECISION:** an act beyond a grant names both its brief and the Owner's DECISION. Beside `brief`, it carries
  `decision: <id> <sha256>` (§3): the DECISION's id, and the sha256 of its signed frame, so the check reads the exact
  signed bytes. The sha256 covers the DECISION's canonical bytes (§9), which both signatures of a pair cover, without
  its signature blocks; so it stays the same when the phone's signature is added. The check verifies the signatures
  over those bytes as a separate step. The DECISION gives the authority (§6.1, §10b), and the brief still limits the
  act's subject and targets; neither replaces the other.

**4. The check.**
- **Where it runs:** inside `hail send`, on the final frame and its real destination. Building the hail, checking it,
  signing it and sending it are one step (§11); a check of a file before sending doesn't count.
- **What it reads:** a fixed snapshot of the brief, the roster's version and the clock.
- **What passes:** a seat's hail passes only when:
  - the brief it names counts (point 2), and the brief's bytes match the named sha256;
  - the brief's `seat` is the acting seat, proven by the signing key the roster lists for that seat (§9);
  - its act, `hail` or `stop`, is in `acts`;
  - its recipient is in `targets`;
  - for a STOP or a REQUEST, every item of its `subject` header is in the brief's `subject`. A STOP or a REQUEST
    without the header MUST be refused when enforcing, and is logged in shadow mode;
  - for an act beyond a grant, its `decision` header names the DECISION by its id, and the sha256 matches the
    DECISION's canonical bytes, without its signature blocks (point 3). The DECISION itself is verified as §6.8 says;
  - the snapshot's clock is before `until`.
- **Unknown means refused.** Anything unknown, missing or ambiguous MUST be refused when enforcing, and is logged
  in shadow mode. So:
  - where a team signs with one team-level key, or with seat keys that its seats can all reach (§9), the key can't
    prove which seat acts, and the check refuses;
  - a case these rules don't settle is refused as ambiguous. Each such case is open in §13.

**5. Control acts without a brief.** Only these pass without a brief, and only to the brief's issuer or the seat's
principal:
- a receipt (RECEIVED, WILCO, DECLINED or DONE) whose `re` names an existing hail;
- one WARNING, "act refused", for each refused act;
- an urgent concern: a WARNING that carries `urgent` (§3).

The list is fixed. It lifts only the need for a brief: every other check still applies (§6, §8, §9). It never
covers a REQUEST, a STOP, any other recipient, or the act that was refused.

**6. The mode.**
- **The roster sets it,** `shadow` or `enforce`, and the Owner signs it (§8). A seat MUST NOT set it.
- **Shadow first.** In shadow mode the check refuses nothing: it logs each act, its brief, what it would have refused,
  and why. It starts with the first briefs that carry a scope block, and isn't replayed over older ones. The shadow
  run of `hail check` over every filed hail (§11) is a separate test.
- **Enforcing** starts only once the Owner has reviewed what the check would refuse, and the false refusals are fixed,
  in the scope blocks or in the check. Once enforcing, a seat's hail
  without a `brief` header MUST be refused, not skipped, unless point 5 covers it.
- **The sending side enforces.** A reader that doesn't know `brief` ignores it (§3), so older readers stay compatible.
- **It runs once `hail send` exists** (§11), since it runs inside it.

## 9. Signing: which key wrote it

Signing and sealing are separate layers, with separate keys. A signing key MUST NOT encrypt.

- **What it gives:** integrity, and the identity of the key that signed, against any host. It gives a *seat's*
  identity only once that seat alone can use the key.
  - **Alone** means that no process of another seat can read the key's private half or have it sign: for example,
    under a separate OS user, in a hardware key or chip that gates each use, or in a hosted platform's per-agent
    secret store that no other agent reads.
  - Where seats share an OS user and can reach each other's keys, each key names the team, not the seat.
  - The same holds for a seat's own history (§10): where seats share an OS user, each can change the others', so
    duplicate and replay protection then holds for the team, not for one seat.
- **When:**
  - a hail that crosses teams, or travels through a host other than git's forge, MUST be signed once the roster's keys
    exist and `hail send` signs. Until then hails go unsigned;
  - on one machine, a hail is signed once `hail send` signs for the seat, so a hail keeps the same bytes when it later
    moves to git or to the other team. Not by hand before that.
- **Keys:** one Ed25519 signing key per seat, listed in the roster. `hail check` writes the allowed-signers file that
  `ssh-keygen -Y verify` needs from the roster, so each key is written down once. (The reference deployment's own
  exception for one team: Appendix D, D.4.)
- **A seat's key changes through the roster** (§8, §10b): a roster change revokes or rotates it, and counts from the
  commit that makes it on the default branch. A hail signed with the old key and filed before that commit (§10)
  stands, and is verified against the roster in force when it was filed; one filed after it is refused, and its sender
  sends it again, signed with its new key. Verifying an old hail says who signed it; a new act on it still needs the
  current grants (§6.1).
- **The Owner's signing keys** sign the Owner's answers, "seen"s and the signing tool's hand-overs (§10b). They are not
  the oversight keys of §9a.
  - They sit in one allowed-signers file, at the path the deployment names (the reference deployment's: Appendix D,
    D.12). hail and the board both read that one file, and it ships with hail alone too.
  - A comment line beside each key names the device it signs on: `desktop` or `phone`. Each key's `namespaces=`
    option lists the namespaces it may sign in (below), and limits it to them.
  - On the phone, the Owner chooses which to enroll: a biometric-gated phone key, a hardware security key, or both.
  - Enrolling or revoking a key there counts for both. A change to that file needs the Owner's two signatures, as a
    permission change does (below), with the two exceptions below; `hail check`, once it exists, enforces that. A key counts only once the change
    that enrolls it is on the default branch.
  - **The first keys** are enrolled by the Owner's own signed commit to that file, made in person on the Owner's
    desktop, through the board's gate. This is one of the two changes to that file made with one signature.
  - **Revocation** counts from the commit that removes the key on the default branch. A DECISION signed with a
    revoked key and not yet filed then is refused; one already filed stands.
  - **Rotation** enrolls the new key, with two signatures, then revokes the old one. **Recovery,** when no enrolled
    key is left, enrolls again as the first keys were, in person on the desktop, through the board's gate: the other
    change made with one signature.
  - A DECISION is always signed, with an Owner signing key; "until then" doesn't apply to it. So is the Owner's
    "seen". A reader verifies both against this file.
- **One namespace per purpose:** a seat's hail, the Owner's "seen" and the signing tool's hand-over (§10b) are
  signed in the namespace `hail`, and a DECISION in `hail-decision`; the board keeps its own. A signature MUST NOT
  count outside its namespace.
- **Two signatures, for a permission change** (§10b). A DECISION that changes a permission, adoption included
  (Status), MUST carry two signatures:
  - **the same payload:** both cover the DECISION's canonical bytes, below;
  - **the sender:** the DECISION carries `from: owner pair` (§3);
  - **the order:** the desktop signs first, and its block starts on the line straight after `end hail/1`; the phone
    adds the second, whose block starts on the line straight after the first block ends;
  - **the roles:** the first MUST verify against a key listed for the desktop, and the second against a key listed for
    the phone. One key twice, or two keys of one device, is not a pair;
  - **a partial pair:** a DECISION with the desktop's signature alone is pending, not an answer. The first signature
    stays in the Owner's signing tool, unfiled, until the second is made. Only the complete pair is filed, and no
    seat acts on a partial one;
  - **verified when filed:** each key MUST still be enrolled when the pair is filed and verified, and the pair counts
    only under §10b's rules for acceptance: an ask still open, or a decision still in force to replace.
- **Canonical bytes:**
  - UTF-8 in NFC;
  - LF line endings;
  - no trailing spaces;
  - from `hail/1` through `end hail/1`, with exactly one LF after it.

  A reader rebuilds that form before verifying.
- **How:**
  - sign with `ssh-keygen -Y sign -n hail`, or `-n hail-decision` for a DECISION;
  - the armored signature starts on the next line;
  - verify with `ssh-keygen -Y verify` in the same namespace: a seat's hail against the allowed-signers file written
    from the roster, and the Owner's hails against the Owner's signing-keys file.
- **The signed bytes hold `from`, `to` and `id`,** so a signed hail can't be passed off as addressed to someone else.
  For a sealed hail they also hold `sealed-to` (§9a).
- **Why sign even when sealed:** with several recipients, any of them holds the per-message key and could forge to the
  others. `age` makes no claim about who sent a file.

## 9a. Sealing: who may read it

- **What it gives:** confidentiality from every host that carries or stores the hail and never handles its plain text.
  It says nothing about the sender: that is §9's job. A platform that is also an endpoint, where a seat composes or
  reads its hails inside hosted prompts and tool calls, sees the plain text whatever is sealed in between. Sealing
  doesn't hide a hail from it; keep sensitive plain text out of such a platform: there, a seat MUST put sensitive
  content behind a pointer. A short body that holds nothing sensitive MAY travel as plain text.
- **When:** opt-in, per hail or per channel. Any channel whose host is not git's forge seals by default, by the
  roster's policy. A hail in git or on the local socket MUST NOT be sealed unless it opts in.
  - **The reference deployment's own exception** for one channel is in Appendix D, D.3.
  - **A channel whose sealing can't be done yet** holds its hail or refuses it. It MUST NOT send the hail unsealed in
    silence.
- **Levels:** S0, plain (signed once `hail send` signs); S1, the hail is sealed. Whether an S2 that also seals the file
  behind the pointer is needed is open (§13).
- **The design:** multi-recipient key encapsulation, with the Owner as a standing, visible recipient. Not key escrow:
  a seat's private key MUST NOT be deposited.
  - A random key per hail encrypts the hail (AEAD): the signed hail once signing is required (§9); before that, the
    canonical unsigned hail with its `sealed-to` line.
  - It is wrapped once per reader and once per Owner oversight key.
  - **The tool:** `age` 1.3 or later, with no custom cryptography. That means a random file key, a ChaCha20-Poly1305
    payload, and one recipient stanza per recipient: an X25519 stanza for a seat's encryption key, and a `p256tag`
    stanza for each `age1tag` oversight key. Senders need plain `age`; only the reader of an oversight key needs its
    hardware plugin.
- **Keys:**
  - one X25519 encryption key per seat, apart from its signing key;
  - **the Owner's oversight keys,** by class:
    - **K1:** an ordinary key behind a biometric or PIN gate (an OS keychain, a password manager, a passphrase file).
      It can be copied once unlocked, so it is the weakest class, and it MUST NOT be used where a chip exists;
    - **K2:** a key made inside the machine's own chip and tied to it, as an `age1tag` key, through that chip's `age`
      plugin, and gated by the chip's own biometric or PIN check;
    - **K3:** a hardware key you carry (when one conforms: §13);
    - **R:** an offline recovery key, printed and kept away from any machine;
  - **every sealed hail goes to all of the Owner's oversight keys at or above its minimum class, and always to R where
    R exists,** so no class ever rests on a single key. A sealed hail is only as strong as the weakest key other than R
    that it goes to, so the setup's class is that of its weakest key other than R. The board, where there is one,
    shows it, and whether R exists (where a deployment without a board shows it is open: §13);
  - **at least two oversight keys,** one of which survives the loss of the main machine: a K3, another machine's K2,
    or R. Setup is not complete without them. R counts for every class;
  - **a minimum class per hail:** `hail send` applies the rule for the hail's class of record, for example "K3 only"
    or "K2 or better". The sender MAY raise it and MUST NOT lower it, and `hail send` MUST refuse when no key of that
    class exists. A minimum class MUST NOT be enabled unless at least two keys are eligible for it (keys at or above
    the class, plus R), at least one of which survives the loss of the main machine. A setup of one K2 and one K3,
    with no R, can't send "K3 only" until a second K3 or an R exists. Where a seat's key is not that seat's alone, a
    minimum class protects only hails with no seat among their readers;
  - **P-256 now;** the hybrid `age1tagpq` at the next rotation, once the plugins support it;
  - keys rotate through the roster.
- **Order: sign, then seal,** once signing is required (§9). The signature is sealed with the hail, so the host can't
  see who sent it. Before then, a sealed hail is unsigned: sealing then authenticates neither its sender nor its
  `sealed-to` line.
- **On the wire:**

```text
hail/1 sealed
to-mailbox: <opaque id>
<age armor>
end hail/1 sealed
```

- **What the host sees:** the opaque mailbox id, the size and the time.
- **Keeping the Owner a recipient:**
  - `hail send` MUST add the Owner's oversight keys;
  - the signed bytes carry `sealed-to: <fingerprint>, …`, naming every recipient key;
  - each reader's team filer files the sealed hail as received, as ciphertext and nothing more. Filing creates no hail
    and no response: the protocol's responses are only those §4 requires. The Owner checks access by opening the filed
    ciphertext and reading the original hail, verifying its signature where signing is required or a signature is
    present, and otherwise recording it as unsigned. If that fails, the Owner looks at recipient inclusion, the
    identity's availability and the ciphertext's integrity before blaming the sender;
  - a reader also checks each `p256tag` stanza's 4-byte tag against the Owner's public keys in the roster. A tag
    shows a stanza was made for that key, not that its content is valid.
- **No forward secrecy, by choice.** The record MUST stay readable by the Owner. That is why a leaked oversight key
  exposes everything sealed to it, why the classes above exist, and why oversight keys rotate.
- **Plaintext stays within the recipients.**
  - A sealed hail's plaintext MUST NOT be filed where anyone outside its recipients and the Owner can read it: not in
    the shared private repository, and not on a host.
  - The Owner reads the filed ciphertext with an oversight key.
- **The limit:** sealing hides a hail from the host and from other teams. It hides a hail from another seat only where
  that seat can't use a reader's key.

## 10. The record

- **Every hail and receipt is filed by its team's filer,** the seat the roster names for it. Each team files in its own
  folder of the shared private repository, one writer per folder. (The reference deployment's filers: Appendix D,
  D.10.)
- **When a hail is filed:** when the commit that adds it to its team's folder first enters the shared repository's
  default branch. A copy on another branch or in a workbench isn't filed. The roster lives in that same repository,
  so one history orders both. Whether a hail was filed before or after a roster change, such as a key's revocation,
  is read from that branch's history, by which commit entered it first, never from a time a sender wrote or from
  when a side branch was made.
- **Every seat keeps its own history:** before it answers or acts on a hail, it keeps the hail's id and canonical
  bytes (§9), and then its own responses, where its later sessions read them (§4, point 5). Filing first also meets
  this.
- **A seat without a file grant keeps a copy** of every hail it sends in its workbench, `out/hail/<id>.txt`, with the
  hail's exact bytes, and its team's filer files it. The content a hail points at stays in `out/to-<seat>/`. A fully
  isolated workbench, such as a review with no network, answers by its hand-back file; for it `out/hail/` is optional.
- **A filed hail MUST NOT be amended.** A correction is a new hail with `supersedes`.
- **A sealed hail is filed sealed,** as §9a says.
- **A DECISION is filed through the asking team's filer,** in that team's record and within that team's push grants
  (§10b). A valid signature gives no push grant.
- **While a stand-in stands in,** the stand-in filer the roster names files for that team, under the team's push rules
  (§10b).
- **Outboxes and intake** (§10b): a seat's outbox is its `out/hail/`. A seat's intake line is its own `for: owner`
  QUESTION there, and the Owner's digest reads it there; nothing extra is sent. Since `out/hail/` is required only
  for a seat without a file grant, and optional for a fully isolated workbench, the digest sees a seat's intake only
  where that seat keeps an outbox the digest can read.
- **The reference deployment's own rule** for its own seats' hails is in Appendix D, D.5.
- **For users, open:**
  - a `hail/` folder under the project's tracker, with one file per hail, and the board showing what is unanswered and
    overdue;
  - in a public repository, `hail send` refuses what fails the public-text checks.

## 10a. The watcher

- **What it is:** a small helper that pulls, and rings the addressed seat.
- **What it rings for:**
  - every hail to a local seat that §4 expects an answer to: INFORMATION, ADVICE, WARNING, QUESTION, REQUEST and
    STOP;
  - an ANSWER, a DECLINED or a DONE to one of that seat's own open hails;
  - a DECISION to one of that seat's own Owner-asks, and the INFORMATION that hands one back (§10b, point 11), when
    it reaches the raising seat;
  - an overdue response.
- **What it doesn't ring for:** RECEIVED and WILCO, which ask nothing of the seat they reach. They go to a digest, so
  the bell isn't ignored.
- **It is stoppable by its owner's own means:**
  - a local process MUST write its PID, MUST refuse to start while that PID lives, MUST exit on SIGTERM, MUST have a
    time limit, and MUST NOT be stopped by name;
  - a hosted platform's routine is stopped in that platform's settings.
- **Who needs one:** a seat needs a watcher only where hails reach it through git (§7). On one machine or one hosted
  agent platform, a team's seats hail each other by the nearest transport, and across teams only principals hail
  (§8). So a team whose seats share one machine or one platform needs one watcher, its principal's.
- **Where it runs:** inside the seat it rings, as a background task of that seat's own session, so the seat is woken
  directly. It MUST NOT write anywhere but its own clone and its own state folder, MUST NOT fetch into another seat's
  repository, and MUST have a time limit. A local watcher stops by its own PID; a platform's routine, which has no local
  PID, stops in that platform's settings.
- **It rings the seat, and MUST NOT ring the Owner,** with one named exception: the Owner-ask relay (§10b), where a
  principal rings the Owner for an urgent matter. The bell itself still rings seats only. Apart from the relay, the
  Owner gets a digest at most. Together with the return grants (§8), a principal answers the other team without
  waiting for the Owner.
- **The Owner's digest** lists, from the relay (§10b):
  - every matter that doesn't ring;
  - each principal's rings, which the Owner can mark "not urgent" with one tap; nothing demotes a principal
    automatically;
  - the asks, counted by `kind` and by principal, with each gate's answers and rejections and their originals;
  - once a week, a section listing every rejection, and every answer from standing evidence, each with its original:
    all of them, not a sample (§10b, point 2);
  - every intake line, and "not disposed" for one left without a relay, answer or rejection (§10b, point 13);
  - the acting seat's WILCO, DONE and DECLINED "can't act";
  - "answered, not yet reached the seat";
  - each surface whose "shown" for an Owner-ask didn't come back within its window, as "unconfirmed" (§10b, point 6);
  - an urgent concern that rang twice with no "seen", first.
- **An example:** a fetch loop on the seat's own clone, which ends itself at its time limit.

## 10b. The Owner-ask relay

A need that only the Owner can meet travels out, from the seat that raises it to the Owner, and the Owner's signed
answer travels back, to the seat that acts. Both legs ride the record (§10); there is no new transport. A relay only
notifies: only the Owner's signature authorizes (§6.2).

**1. Who asks.**
- **No seat poses a question for the Owner in its own chat alone** (§6.7).
- **Hop by hop.** A seat that needs the Owner raises the need with a QUESTION that carries `for: owner` (§3), to the
  next seat on its way to its principal, as its grants allow: for example, a Builder to its Planner, and the Planner
  to the principal. A seat between them settles the need within its own grants, with standing evidence (point 4), or
  forwards it unchanged (§8). The answer comes back as point 11 says: the relaying principal hands it straight to the
  raising seat.
- **Straight to the principal:** an urgent need, marked `urgent` (§3), or one whose next hop is overdue by §5's
  clock, goes straight to the principal, copying the hop it skips (§8).
- **Recorded:** every hop is filed, with its time. The seat that acts checks the Owner's signature itself (§6.8).
- **The intake line** is the seat's own `for: owner` QUESTION, in its outbox, for the Owner's digest (point 13).
  Nothing extra is sent.

**2. The principal's gate.** From its own context, the principal does one of three things, and files it. It replies to
the `for: owner` QUESTION with an ANSWER whose `disposition` (§3) names what it did:
- **`relayed`:** it relays the need, as an Owner-ask whose `re` names the seat's hail, and whose `raised-by` names the
  raising seat (point 3). The ANSWER's pointer names the Owner-ask;
- **`answered`:** it answers the need with standing evidence (point 4). The ANSWER's pointer names that standing
  authority; or
- **`rejected`:** it rejects the need. The ANSWER's body gives its reason, and its pointer names evidence where there
  is any, or is `none`.

A seat between the raising seat and its principal that settles the need with standing evidence answers the same way,
with `disposition: answered`. An ANSWER to a `for: owner` QUESTION MUST carry `disposition`.

**The gates' audit.** Once a week, the Owner's digest lists every rejection, and every answer from standing evidence,
each with its original: all of them, not a sample (§10a).

A case belongs to the seat that filed its first relay, answer or rejection. While a stand-in stands in, it MAY act on
the cases its absent principal opened, for the principal, under `acting-for`; those cases stay the principal's
(point 12). Otherwise a case passes to another seat only through the handover hail (point 12). A seat MUST NOT go
round a rejection (§6.7).

**3. The Owner-ask.** Only a team's principal, or its stand-in while it stands in, sends one: a QUESTION `to: owner`.
`hail check` MUST refuse a hail `to: owner` from any other seat. An Owner-ask:
- MUST be only for what no grant covers: a decision, an act beyond every grant, or a permission only the Owner can
  give;
- is one of four kinds, and MUST name it in `kind` (§3): **allow once** (`allow-once`), **change a permission**
  (`grant-change`), **do it yourself** (`owner-act`: the Owner is asked to act), or **decide between options**
  (`decide`);
- MUST carry who asks: its `from`, the team and the seat that sends it, and, where it relays a seat's need,
  `raised-by: <team>.<seat>` (§3), naming the raising seat. It MUST also carry a one-line why that says why only the
  Owner can answer, where to clarify, its options, and a pointer to the originals it relays, unchanged, with their
  sha256;
- **its options** are written in the body, one per line, each as `option <label>: <text>`, as `option A: …` and
  `option B: …` (Appendix A). A label is one capital letter, A to Z, used once per ask;
- **where to clarify** names the raising seat's own surface first, because it knows the need, and the principal's
  own surface second;
- for a permission change, `kind: grant-change`, MUST carry the exact roster change as
  `roster: <sha256 before> -> <sha256 after>` (§3): the roster file's sha256 before the change and after it;
- MUST carry a `decide-by` time (§3).

**4. Standing evidence.** A principal, or a seat between the raising seat and its principal, MUST answer a need only
with standing authority: a current roster grant, or a signed answer of the Owner's given as a standing rule, which on
a DECISION is `scope: standing` (point 7). It MUST NOT cite a DECISION whose scope is `once`, stated or by default.
The raising seat MUST check the cited evidence itself before it relies on it.
- **Citing a standing DECISION** doesn't use it up, and doesn't tie it to the later ask: it MAY be cited for every
  later need it applies to, by any gate, for any raising seat. Each citation is checked again: signed, filed and
  verified, in force, `scope: standing`, applying to this need, and within the harness's limits and the roster's
  grants. A standing DECISION MUST NOT allow an act beyond the grants: such an act needs its own `allow-once` or
  `grant-change` ask.

**5. Ringing the Owner.**
- **Urgent alone rings.** A principal MUST NOT ring the Owner unless it judges the matter urgent, and marks its hail
  `urgent:` with the one-line reason (§3). Everything else goes to the Owner's digest. A ring passes the 30-minute
  presence window and the hourly cap.
- **One reminder.** An Owner-ask that doesn't ring gets one reminder, 4 hours after it is filed, and one more only
  when talk lapses (point 7). A reminder that falls between 22:00 and 07:00 waits until 07:00, and is never skipped
  while its ask is open; one for an ask that has closed or expired is withdrawn (the cap, below). After that, only
  the digest lists it.
- **A concern** is bad news a seat raises, which may ask for no decision. The seat sends it hop by hop to its
  principal, as a WARNING, with `urgent:` (§3) when it is urgent. Only a principal sends a concern to the Owner
  (§8), as a WARNING `to: owner`. A concern it relays from a seat carries `raised-by` (§3), naming the raising seat.
  A concern is never a STOP.
- **At night,** from 22:00 to 07:00, an urgent ring stays silent until 07:00, unless its hail carries `harm-by` (§3)
  with a time before that 07:00.
- **Checked at the moment it would ring:** these rules are checked on the Owner's device at the moment it would ring,
  in the Owner's time zone, as the Owner sets it.
- **The cap:** at most 6 notices in any rolling hour, counting everything but urgent rings, reminders included. The
  7th MUST wait, and MUST NOT be dropped while its ask is open. A queued notice or reminder for an ask that has
  closed or expired is withdrawn, never delivered. Urgent rings are counted per principal.
- **"Seen" for a concern.** The Owner acknowledges an urgent concern with a signed "seen", which returns to the
  raising seat and its principal and closes every notice. The "seen" is a RECEIVED `from: owner <device>`, signed
  (§9), whose `re` names the concern. A "seen" carries no authority. With no "seen" 15 minutes after a ring with
  sound, the concern rings once more on the phone, then stays first in the Owner's digest.

**6. Fan-out: what each surface shows.**
- **All at once.** When an Owner-ask is filed, it is shown on the raising seat's own surface, on every session the
  Owner typed in within the last 30 minutes, and on the companion app. The companion app stays silent from 22:00 to
  07:00 unless the ask names harm before that 07:00, in `harm-by` (§3), and keeps to the cap of point 5.
- **An urgent concern** a principal sends the Owner is shown at once on every surface, the companion app included.
  Like every ring, it passes the 30-minute presence window and the cap; at night, its sound follows point 5.
- **Presence.** The Owner typed in a session when the Owner sent a real message there, and the session keeps that
  message's time locally. A tool call or an agent's text MUST NOT count as the Owner typing. A session that can't
  keep that time, or can't report it to the relay, has unknown presence. An Owner-ask goes to it too, since the
  Owner may have typed there, and the Owner's digest lists that session's presence as unknown. Only a session known
  to be outside the window is left out.
- **A card MUST carry only** who asks, the one-line why, where to clarify, the ask's options, the five answers
  (point 7) and a pointer. Who asks is shown as "‹raised-by› via ‹from›" where the ask relays a seat's need. The
  originals stay behind the pointer, sealed where §9a says. A push MUST say only "an ask waits".
- **As data.** A surface shows an ask's text as data, fenced, and MUST NOT act on it.
- **Catch-up.** Every session lists the open Owner-asks when it starts; the companion app does when it opens.
- **"Shown."** Each surface returns a "shown" receipt with the ask's id and hash. It is not a hail: it is one status
  line per surface, with the surface and the time, and it MUST NOT carry authority. The companion app has 10 minutes
  to return it; a session on the same machine, 15 minutes; a session on a hosted agent platform, the wake interval
  the roster declares for the relay's notice to it, plus 15 minutes, or 15 minutes where none is declared (§8). Each
  window starts when the relay first makes the ask available to that surface, and a retry doesn't restart it. A
  "shown" that comes after its window still records the surface as shown. The surface keeps its "shown" line in its
  own outbox, which the Owner's digest reads, as it reads intake lines (point 13). A missing "shown" means
  "unconfirmed"; "shown" never means seen. When no "shown" comes back within its window, the raising seat and its
  principal are told "unconfirmed", and the Owner's digest lists it (§10a).
- **A surface that shows only on its next turn** shows the ask then, adds an "answered" line after it once the ask is
  answered, and shows unknown presence and failed delivery as such.
- **The same answer everywhere.** Every surface, the raising seat's own included, offers the same signed answer, and
  filing an answer that ends the ask closes every copy. After `later` or `talk`, every copy shows the ask open, in
  that state. A surface that can't take a signature points the Owner to one that can.

**7. The five answers.**
- Every card offers five: **yes, or an option**; **no**, with a reason; **own words**; **later**, until a time; and
  **talk** ("I'll come to your session"). Each is signed in one gesture.
- **Yes, or an option,** ends the ask, and may be acted on.
- **No** ends it, and MUST NOT be acted on.
- **Own words** end it, and MUST NOT be acted on unless they narrow an offered option; otherwise the seat asks again.
- **Later** keeps it open. A time past `decide-by` is flagged before signing, and the ask still expires at
  `decide-by`.
- **Talk** keeps it open for up to 4 hours. When they pass without an answer, talk lapses: the ask is open again, shown
  as waiting, with one reminder under point 5's night rule and cap. It too expires at `decide-by` (point 10).
- Only an answer that ends the ask counts as its first decision.
- **The DECISION's form** (§3):
  - `from: owner <device>`, where the device is `desktop` or `phone`, or `from: owner pair` on a DECISION with two
    signatures (§9); and an id `owner-<yyyymmdd>T<hhmm>Z-<key>` (§3);
  - `to:` the ask's `from`: the principal, or the stand-in, that asked;
  - `re:` the ask's id;
  - `pointer:` the ask's filed frame, with its sha256: the bytes the Owner signs over;
  - `answer:` is `yes`, `no`, `option <label>`, `words`, `later <time>` or `talk`, one for each answer above.
    `option <label>` names one of the ask's option lines by its label (point 3), and `later <time>` gives the time in
    `written-against`'s time form;
  - the Owner's own words and reasons go in the body. A reason MUST be given with `no` and with `words`, and MAY be
    given with the others;
  - it is signed (§9), in the namespace `hail-decision`.
- **Scope.** A DECISION carries `scope: once`, the default, or `scope: standing`. Only a DECISION that answers an ask
  of `kind: decide` MAY carry `scope: standing`. Only a standing DECISION may be cited
  by a gate as standing evidence (point 4).
- **The Owner signs, never a seat,** also in the seat's own session (§6.6).
- **Signing what is shown.** The Owner's signing tool fetches the ask by its id, checks its hash, renders it itself,
  and signs the ask's id, its hash and the chosen answer, which the DECISION carries in `re`, `pointer` and `answer`.
  It MUST NOT sign from a command or a link inside an ask.
- **By kind:**
  - **allow once** (`allow-once`): a yes lets the raising seat do that act once (§6.1);
  - **change a permission** (`grant-change`): two signatures, desktop and phone (§9), over a DECISION that carries
    the exact roster change as `roster: <sha256 before> -> <sha256 after>` (§3). A seat MUST commit exactly that
    change: a commit that turns the roster file with the first sha256 into the roster file with the second. The checks
    MUST NOT accept a roster change unless the Owner's signed DECISION matches it;
  - **do it yourself** (`owner-act`): the ask ends with the Owner's yes. The work's track reads "awaiting the
    Owner's act" until the act itself lands. The seat watches for it, and MUST NOT perform it;
  - **decide between options** (`decide`): the chosen option.
- **A STOP stays in force** unless the DECISION names it.

**8. Acceptance: one decision in force.**
- A DECISION counts once it is filed and verified in the asking team's record. Verified includes its binding to its
  ask: its `re` names the ask, its `pointer` gives the sha256 of the ask's canonical bytes (§9), and its `to` is the
  ask's `from`. A DECISION that fails any of these is refused.
- The first DECISION that ends the ask, on that record's main branch, wins; `later` and `talk` keep the ask open, and
  don't end it (point 7). A later one is refused unless it names, in `supersedes`, the decision in force at that
  moment. Only a decision that ended the ask is in force: a DECISION after a `later` or a `talk` needs no
  `supersedes`, and the first one after it that ends the ask is the first decision.
- **A change of mind** is a new DECISION that names the decision in force. It is allowed after the ask has closed,
  and from the Owner only. The first change filed that names it wins. A change that names an already-replaced
  decision is refused, and shown to the Owner. Work under a replaced decision stops at a safe point, and is reported.
  What is done stays done.
- A one-time DECISION (`scope: once`) is for its own ask, and for one use. A standing one is cited as point 4 says.

**9. One use.** The seat that acts verifies the DECISION (§6.8), then MUST claim it with a WILCO whose `re` names the
DECISION before it acts. The claim goes to the seat whose hail delivered the answer: the relaying principal, whose
INFORMATION handed it back (point 11). That claim is the raising seat's answer to the principal it goes to, under its
return grant to that principal (§8), though its `re` names the DECISION. Where the principal itself acts, the claim
goes `to: owner`, which only a principal, or its stand-in while it stands in, may send (§8). A claim counts once it
is filed on the asking team's record's main branch, and the first claim filed there acts, as the first DECISION
filed wins (point 8): the seat acts only once its claim is filed and is the first. A later claim MUST NOT act.
- **Stale context.** A one-time DECISION lapses if no claim is filed within 24 hours of its own filing; the seat
  then asks again. Before acting, the seat checks that what the ask's pointer named, followed through to the raising
  seat's need (the commit, branch or file the act is on, by sha256), is unchanged. If it changed, the seat MUST NOT act: it sends its principal a WARNING whose `re` names the
  DECISION, and the principal asks again.
- **Unknown outcome.** A later session that finds a claim but no DONE MUST NOT repeat the act: it checks, or reports
  "outcome unknown" and waits. That report is a WARNING with `outcome: unknown` (§3), whose `re` names the DECISION.
- The seat that acts answers WILCO, then DONE, or DECLINED "can't act", each in the Owner's digest.

**10. Lifetime.** An Owner-ask expires at its `decide-by` time, whatever its state. It MUST NOT expire with a default
answer. A renewed ask supersedes the old one, and names it in `supersedes`.

**11. Delivery back.**
- **The signing tool keeps a DECISION until it is filed.** It files it through the asking team's filer, within that
  team's push grants (§10), and retries. To hand it over, it hails that filer with an INFORMATION,
  `from: owner <device>`, whose pointer names the DECISION, and the filer files the DECISION like any hail. That
  INFORMATION is a hail: it is signed in the namespace `hail` with the key of the device its `from` names (§9), and
  carries no authority of its own. The DECISION's signatures are what count. After 10 minutes unfiled, the signing
  tool shows "signed, not filed yet".
- **The relaying principal hands an accepted answer back** to the raising seat by an INFORMATION, whose `re` names
  the seat's QUESTION and whose pointer names the filed DECISION by its sha256. It keeps the answer until the seat
  claims it or declines. It resends after 30 minutes. After 2 hours, the Owner's digest shows "answered, not yet
  reached the seat".

**12. The stand-in.** The roster names, for each principal, a stand-in, and a stand-in filer with it (§8).
- The stand-in stands in when the principal is overdue by §5's clock, or after 10 minutes on an urgent matter.
- While it stands in, every hail it sends for the principal carries `acting-for: <principal>` (§3), and nothing
  reaches the Owner unjudged.
- While it stands in, it MAY act on the cases the absent principal opened, for the principal, under `acting-for`.
  Those cases stay the principal's.
- Its stand-in filer files while it acts, under the team's push rules.
- When the principal returns, the stand-in hands every open case back in one hail, an accepted answer still to deliver
  included: an INFORMATION that carries `handover:` (§3), whose pointer names the list of open cases, with what the
  stand-in did on each of the principal's cases. The principal takes cases over only through that handover hail.

**13. Intake.**
- **The intake line is the seat's own `for: owner` QUESTION,** kept in its outbox, and the Owner's digest reads each
  seat's outbox, so a gate can't hide a raised need. Nothing extra is sent. No bypass is created: the line is
  addressed to the seat's next hop, not to the Owner, and the need still waits on its gate.
- **"Not disposed":** a line with no ANSWER carrying a `disposition` after 4 hours, not counting 22:00 to 07:00, shows
  "not disposed".
- **Coverage.** The digest sees exactly the intake lines in outboxes it can read (§10). It doesn't see a hosted
  team's seats, whose outboxes are not on a machine it reads: that is the hosted-team limit, until the companion
  app's intake path (§13). Nor does it see a fully isolated workbench, or a workbench that is unavailable. No line seen
  means no line seen, never that no need was raised.

**14. Three tracks.** An Owner-ask is followed on three tracks. An observation MUST NOT change the ask's state.
- **the ask** (authority): open; later until a time; talking; ended by its first decision; expired at `decide-by`;
  superseded by a renewed ask;
- **the seat that acts** (execution): claimed (WILCO); done (DONE); "can't act"; "outcome unknown"; "awaiting the
  Owner's act"; "answered, not yet reached the seat";
- **each surface** (observation): shown; unconfirmed.

**15. Before reliance.** No bridge is built. Until hail's tools exist and pass the acceptance list of §11:
- a tap in a seat's chat is consent inside that harness for that seat's own act, and never closes an ask in another
  team's record;
- a decision with effects beyond the raising seat waits for the tools, or goes as an ordinary ask on the board's
  tracker, where there is a board.

## 11. Conformance (codebase and static analysis, not prose)

- **`hail check <file>`:** offline and pure, and tested in the project's test suite. It checks:
  - the frame;
  - the grammar of §3;
  - the required keys;
  - the marker;
  - the body's length;
  - the pointer forms;
  - the written-against time against a reference time it takes as an explicit input, never reading a clock itself:
    the reader's clock when the hail arrives, or over the record the committer time of the commit that filed it
    (§10). It refuses a written-against time more than 2 minutes after that reference. The filer sets that committer
    time, so over the record the check catches a wrong clock, not a filer who sets a false one. It never judges a
    hail's age: a transport's window can be longer than any fixed limit (§5), and a re-send keeps its original bytes
    (§4, §5). Given no reference time, it checks the time's form alone, and reports that it did. Age alone never
    expires a hail: before acting on a late one, the reader still checks its own current grants and permissions
    (§6.1), the pointer (§4) and, for an act beyond a grant, the DECISION (§6.8);
  - canonical bytes;
  - for a signed hail, the signature;
  - for a sealed hail, `sealed-to`;
  - for a hail `to: owner`, that its sender is a principal, or its stand-in while it stands in (§10b). Whether a
    stand-in stands in is read from explicit inputs, the clock and the principal's open hails, as the act check reads
    a fixed snapshot (§8a); without them, a stand-in's hail `to: owner` is refused;
  - that `for` appears only on a QUESTION, and `harm-by` only beside `urgent`;
  - for an Owner-ask: a `kind` of §3's forms, and `roster` with `kind: grant-change`;
  - for a DECISION: `from: owner <device>`, or `from: owner pair` with two signatures, an `owner-` id, an `answer` of
    §3's forms, a reason in the body with `no` and with `words`, `scope: standing` only on a
    DECISION that answers an ask of `kind: decide`, a signature in the namespace `hail-decision` against the Owner's signing-keys file, and
    `roster` with two signatures on a permission change;
  - for the Owner's "seen", a RECEIVED `from: owner <device>`, signed in the namespace `hail`;
  - for the signing tool's hand-over, an INFORMATION `from: owner <device>`, signed in the namespace `hail`;
  - for a change to the Owner's signing-keys file, the Owner's two signatures (§9), except §9's two one-signature
    changes: the first enrollment, and recovery when no enrolled key is left.

  It exits non-zero on the first failure. It comes first. Before it refuses anything, it runs over every filed hail,
  and each refusal it would have made is reviewed (test 37).
- **`hail send <seat> <path>`:**
  - runs the check;
  - computes the pointer's sha256 and the written-against line, its time from `hail send`'s own clock;
  - MUST refuse a home path, a private id in public text, or an Owner quote that doesn't verify;
  - MUST scan the hail, each file its pointer names and each file a pointed-at list of sha256s names, with a
    versioned set of secret patterns it takes as an input, and MUST refuse a hail in which that set finds a secret,
    or whose pointed-at bytes it can't obtain or verify (§1);
  - checks the roster;
  - runs the act check (§8a) on the final frame and its real destination, in one step with signing and sending;
  - signs, and seals as decided;
  - delivers by §7.

  It comes later; until then, hails are written by hand. (The reference deployment's own rule for when: Appendix D,
  D.6.)
- **How both are built is open** (§13).
- **Until both exist,** the seats follow this spec by hand.
- **Acceptance before reliance** (§10b, point 15). No seat relies on the relay until hail's tools pass every test
  below, each run end to end:
  1. A Builder's need goes hop by hop to the principal, and the answer comes back to that Builder.
  2. An ask, or an option, altered after filing is refused at signing.
  3. A DECISION that can't be filed is kept, retried, and shown "signed, not filed yet".
  4. An accepted answer that never reaches its seat is resent, then shown "answered, not yet reached the seat".
  5. Two sessions of one seat wake on one DECISION, and only the first claim acts.
  6. A seat that crashed after acting doesn't act again: it checks, or reports "outcome unknown".
  7. Of two answers that each end one ask, filed seconds apart, the first filed wins, and the second is refused.
  8. A change that names the decision in force wins; one that names a replaced decision is refused, and shown.
  9. An ask renewed at `decide-by` supersedes the old one, and no answer to the old one is acted on.
  10. A notice delayed or replayed into the night stays silent, unless it is urgent and carries `harm-by` with a time
      before 07:00.
  11. The 7th notice in a rolling hour waits, and is never dropped while its ask is open; a queued notice for an ask
      that has closed or expired is withdrawn.
  12. A principal that returns while its stand-in acts takes cases over only through the handover hail.
  13. A surface that was offline catches up: it lists the open asks, and shows the answered ones as answered.
  14. Only a principal's, or its stand-in's, Owner-ask is taken in, with its signature verified where §9 requires one.
  15. A session the Owner typed in within 30 minutes shows the ask; a session known to be outside the window doesn't;
      the raising seat's own surface shows it whatever the window; and unknown presence shows as unknown.
  16. Night hours and the shared cap are checked on the Owner's device, at the moment it would ring.
  17. An answer signed on the Owner's device verifies, and one over an altered hash is refused.
  18. The filed DECISION is byte for byte what was signed, filed through the asking team's filer.
  19. The seat that acts verifies the DECISION before it acts.
  20. Once an answer that ends the ask is filed, every surface shows it closed; after `later` or `talk`, every surface
      shows it open, in that state.
  21. A gate's ANSWER to a `for: owner` QUESTION carries `disposition`: a relayed one points at the Owner-ask, an
      answered one at the standing authority, and a rejected one gives its reason.
  22. A seat refuses standing evidence that cites a DECISION whose scope is `once`.
  23. A DECISION with `no` or `words` and no reason in its body is refused.
  24. A signature made in the namespace `hail` doesn't verify a DECISION, and one made in `hail-decision` doesn't
      verify any other hail.
  25. The first signature of a pair is never filed alone; the complete pair is filed.
  26. A commit whose roster sha256s, before and after, differ from the DECISION's `roster` is refused.
  27. A change to the Owner's signing-keys file with one signature is refused, outside §9's two exceptions: the first
      enrollment, and recovery when no enrolled key is left.
  28. The signing tool hands a DECISION over by an INFORMATION `from: owner <device>`, signed in the namespace
      `hail`, to the asking team's filer, which files it.
  29. A DECISION still unfiled after 10 minutes shows "signed, not filed yet"; an intake line with no disposition
      after 4 hours, not counting 22:00 to 07:00, shows "not disposed".
  30. An Owner-ask that doesn't ring gets one reminder after 4 hours, and no other but the one when talk lapses (test
      53); one that falls between 22:00 and 07:00 comes at 07:00 while its ask is open, and is withdrawn if the ask
      has closed or expired by then.
  31. The signed "seen" closes every notice of its concern, and authorizes nothing.
  32. A tool call or an agent's text in a session doesn't count as the Owner typing there.
  33. A "shown" line missing after 10 minutes from the companion app, after 15 minutes from a session on the same
      machine, or from a hosted session after the wake interval declared for the relay's notice plus 15 minutes, or
      after 15 minutes where none is declared, also when a message wakes that seat but the notice doesn't, counted
      from when the ask
      was first made available to that surface and not restarted by a retry, shows "unconfirmed": the raising seat
      and its principal are told, and the Owner's digest lists it. A session that never takes a turn is reported so,
      and a "shown" that comes later still records the surface as shown.
  34. A stand-in's hails carry `acting-for`, and its handover is an INFORMATION with `handover:`.
  35. A later session that finds a claim but no DONE, and can't check the outcome, sends a WARNING with
      `outcome: unknown` whose `re` names the DECISION, and doesn't act.
  36. The week's digest lists every rejection and every answer from standing evidence, each with its original.
  37. Before `hail check` refuses anything, it runs in shadow mode over every filed hail of the deployment, reporting
      what it would refuse and refusing nothing; every refusal it would have made is reviewed, and it enforces only
      once that review is done.
  38. An Owner-ask without `kind`, or with a `kind` outside §3's four, is refused.
  39. A DECISION with `scope: standing` on an ask whose `kind` isn't `decide` is not accepted as standing evidence.
  40. An Owner-ask's options are body lines `option <label>: <text>`, each label one capital letter, used once, and
      a DECISION's `option <label>` names one of them.
  41. An accepted answer reaches the raising seat as an INFORMATION whose `re` names its QUESTION and whose pointer
      names the filed DECISION by its sha256.
  42. A DECISION with two signatures carries `from: owner pair`, and one with `from: owner pair` and a single
      signature is refused.
  43. A surface keeps its "shown" line in its own outbox, and the Owner's digest reads it there.
  44. A DECISION whose `re`, `pointer` sha256 or `to` doesn't match its ask is refused.
  45. A stand-in that acts on a case its absent principal opened files under `acting-for`; the case stays the
      principal's, and the handover lists what the stand-in did on it.
  46. A `yes` filed after a `later` on the same ask ends it without `supersedes`, and is the first decision.
  47. A standing DECISION in force is accepted as evidence for a second matching need, and when another raising seat's
      gate cites it; neither citation uses it up.
  48. A standing DECISION that has been replaced, or doesn't apply to the need, is refused as evidence.
  49. An act beyond the grants, settled only by a standing DECISION, is refused.
  50. Of two claims on one DECISION, only the first filed on the record's main branch acts; the seat acts only after
      its claim is filed.
  51. A one-time DECISION with no claim filed within 24 hours of its filing lapses, and the seat asks again.
  52. A seat whose ask's pointed-at commit, branch or file has changed doesn't act, and sends a WARNING.
  53. When talk's 4 hours pass without an answer, the ask is open again, shown as waiting, with one reminder.
  54. A DECISION signed with a key revoked before it is filed is refused; one filed before the revocation stands.
  55. Two sessions of one seat, each writing a hail in the same minute, make their ids alone, with no shared counter,
      and the two ids differ.
  56. A hail that reuses an id the reader has already seen with other canonical bytes (§9) is not acted on, and is
      answered DECLINED, "id reused"; the same id with the same canonical bytes is a duplicate (§4), even where its
      line endings or Unicode form arrived differently.
  57. `hail check` refuses a hail whose written-against time is more than 2 minutes after the reference time it is
      given, and passes one exactly 2 minutes after it; it takes that time only from its input, never refuses a hail
      for its age, and given no reference time reports that it checked the time's form alone. `hail send` writes the
      time from its own clock when it builds a hail.
  58. A new session of a seat that gets a hail an earlier session already answered sends the same receipts again, or
      for a QUESTION its original ANSWER, and doesn't act again; a hail whose canonical bytes match a seen one's is a
      duplicate, however its transport wrapped it. This holds when the earlier session answered and stopped before
      its team filed the hail; a hail that reuses the id with other canonical bytes is then answered DECLINED, "id
      reused"; and a reader that can't read its history doesn't act.
  59. On a non-urgent matter delivered by its platform's own message, a hosted principal asleep within its declared
      wake interval is not overdue, and its stand-in doesn't stand in; once that interval plus 15 minutes has passed
      with no response, it is overdue, and a seat that declares 0 is overdue after 15 minutes. On an urgent matter,
      the stand-in still stands in after 10 minutes (§10b, point 12). A hail through git to a hosted principal is
      timed by its watcher interval.
  60. `hail send` refuses a hail in which its pattern set finds a secret: in the header, in the body, in a file its
      pointer names, or in a file a pointed-at list of sha256s names. It also refuses a hail whose pointed-at bytes it
      can't obtain or verify.
  61. A hail signed with a seat's key that a roster change revoked, and not filed before that change, is refused,
      even where a copy of it sat on another branch or in a workbench before the change; one filed before it stands,
      and still verifies after the rotation, against the roster in force when it was filed. Order is read from the
      shared repository's default branch, where the roster and the filings both live; a new act on an old hail still
      needs the current grants.
  62. A later hail from an addressed seat whose `re` names an id stops that seat's clock for it, as a RECEIVED
      would, without claiming that its pointer was checked; every other addressee's clock runs on.
  63. On arrival, a hail whose written-against time is 3 minutes after the reader's clock is refused; one that
      arrives 29 minutes after it was written through git, or after a declared hosted wake interval longer than 10
      minutes, passes. Over the record, a hail filed a day after it was written passes, and one whose written-against
      time is a day after the committer time of the commit that filed it is refused. A hail that arrives after its
      sender's brief expired in transit isn't refused for its age; before acting, the reader still checks its grants,
      the pointer and any DECISION.
  64. An unchanged re-send after an overdue window, from a filer as from any seat, and an original answer sent again
      to a duplicate by a later session, keep their original bytes and pass at any age; the signature, grant and act
      checks still apply to them.
  65. A session that can't keep the time of the Owner's last message, or can't report it, gets the Owner-ask, and the
      Owner's digest lists that session's presence as unknown; the raising seat's own surface gets it whatever its
      presence.
  66. A hail is read under the spec text its reader's roster in force adopts when it reads it: one that names no
      `hail@<commit>`, one that names that text, and one that names another, whose difference is reported and changes
      no rule; a roster change while a hail is in transit applies at reading; over the record, a hail is read under
      the roster in force when it was filed.
  67. A reader that can't read its history acts on nothing: to a REQUEST it answers RECEIVED, where its return grant
      allows, and warns its team by one WARNING; to an ANSWER and to each receipt it sends nothing. The RECEIVED its
      team sends to that WARNING, and every later hail while the failure lasts, raise no further WARNING; after its
      history is readable again, a new failure raises one new WARNING.
- **The act check's tests** (§8a), each run end to end through `hail send`, in enforce mode unless the test says
  otherwise:
  1. A brief whose `seat` isn't the acting seat, or whose seat its signing key can't prove (§9), is refused.
  2. A brief that no signed hail from an issuer holding "may brief" for its seat delivered is refused.
  3. A STOP or a REQUEST about a work item its brief doesn't cover is refused, even when sent to the right seat.
  4. A hail whose final frame or real destination differs from the one checked is not sent: the hail that is sent
     is the one checked.
  5. A brief past its `until`, or replaced by a newer brief from the same issuer for the same subject, is refused.
  6. Without a brief, only point 5's control acts pass, and only to the issuer or the principal; a REQUEST, a STOP,
     a second "act refused" for the same act, or any other recipient is refused.
  7. Once enforcing, a seat's hail without a `brief` header, and not a control act, is refused.
  8. In shadow mode, the check refuses nothing, and logs each act, its brief, what it would have refused, and why.
  9. A STOP or a REQUEST without a `subject` header, or whose `subject` lists an item outside the brief's `subject`,
     is refused when enforcing, and logged in shadow mode.
  10. An act beyond a grant without a `decision` header, or whose `decision` names the wrong DECISION, or whose
      sha256 doesn't match the DECISION's signed frame, is refused.
  11. Adding the phone's signature to a pending DECISION, still unfiled, doesn't change its `decision` sha256. The
      pair is then filed once, and an act that names it passes only once that DECISION is accepted.

## 12. Examples

Appendix C gives one complete hail per marker, and one signed hail. The relay's hails (§10b) have no example yet.
Appendix C's hails carry no `brief` header, and its STOP and REQUEST no `subject` header: they show the frame in
shadow mode, Appendix B's mode, where a missing header is logged, not refused (§8a). Once the act check enforces, each
needs `brief`, except its control acts, and a STOP or a REQUEST also needs `subject`.

## 13. Open

**The first version's scope.** The first version of the relay holds all of §10b. These wait:
- **an exact-act hash for any act but a roster change:** the Owner's answer binds the exact change only for a
  permission change (§10b, point 7);
- **"opened" telemetry** from a surface;
- **an automatic proposer** that turns an ask answered the same way again and again into a grant proposed to the
  Owner;
- **deadlines and ladders for hails that aren't Owner-asks,** the general ladder of surfaces included;
- **the Owner's panel for hails,** on the board and in the companion app, and the companion app showing the board;
- **the companion app's intake path:** every seat sending a silent intake line straight to the app, which would also
  cover hosted teams (§10b, point 13).

**Open in the relay,** beyond the scope above: how a deployment without a board enrolls the Owner's first keys, and
how the Owner recovers when one device's key is lost and the other is left (§9); which
tool tells the raising seat and its principal that a "shown" is missing (§10b, point 6); and how two sessions of one
seat are kept from acting twice on a need settled by standing evidence (§10b, point 4). The
reference deployment's own open points for the relay are in Appendix D, D.12.

**Open elsewhere:**

- **How `hail check` and `hail send` are built:** one option is a native component, installed separately beside the
  existing shoalmark tool, which stays as it is. (The reference deployment's own rule for deciding it: Appendix D,
  D.7.)
- **S2, sealing the file behind a pointer** (§9a), for content that must not sit readable on the forge: the Owner's
  to decide, per class of record.
- **Where users' hails live,** and how the board shows them (§10): after `hail check` exists and the trial has run.
- **The reference deployment's own trial and release rules** are in Appendix D, D.8 and D.9.
- **The forms Appendix A leaves open,** given there as prose values or comments: the number after `hail/`;
  a team, seat or session name; the separator between several ids in `re`; a
  forge state; a repository, workbench, path or commit; the case of a sha256's hex digits; the case of a value's
  keywords; a mailbox id; a recipient key's fingerprint in `sealed-to`; and the model and the effort in `model`.
- **The roster's file form:** settled together with `hail check`, which reads it. Appendix B is an informative example
  of the roster's content, not of its form.
- **Whether a hosted agent platform's own agent message carries a frame byte for byte.**
- **Where a deployment without a board shows the setup's oversight class,** and whether R exists (§9a).
- **The software the reference deployment relies on,** each fact as of the version it names: Appendix D, D.11.
- **Tools and connectors:** a rule that a seat uses only the tools and connectors its definition or its brief names
  (§1, §14).
- **The act check's git half,** a commit trailer and a pre-push hook for pushes and filings, is a separate tracked
  item, with its own signed answer (§8a).
- **The act check's open cases** (§8a), each refused as ambiguous when enforcing until it is settled:
  - the event that orders two briefs, so that one is "newer";
  - how `supersedes` relates to the replacement of a brief by a newer one;
  - overlapping subjects: a brief whose subjects overlap a narrower brief's, and briefs from two issuers for the
    same subject;
  - withdrawing a brief;
  - the evidence a principal's root scope, its seat definition, gives a check;
  - where `hail send` takes its snapshot of the roster, the briefs and the clock;
  - the forms of a brief's id, a tracker id and a branch name, and the name of a spawn in `acts`.

## 14. Security considerations

This section gathers rules stated elsewhere and adds none. The section named carries each rule and governs.

- **Signing** (§9): a signature gives integrity and names the key that signed, against any host. It names a seat only
  where that seat alone can use the key. Signing and sealing use separate keys. Signing is required across teams and
  through hosts other than git's forge once the roster's keys exist and `hail send` signs; until then hails go
  unsigned. §9 says when a seat alone can use a key.
- **Sealing** (§9a): sealing gives confidentiality from every host that carries or stores a hail and never handles its
  plain text, and says nothing about the sender. A platform that is also an endpoint sees the plain text. Sealing hides
  a hail from another seat only where that seat can't use a reader's key. A channel whose sealing can't be done yet
  holds or refuses its hail, never sending it unsealed in silence.
- **The oversight keys** (§9a): classes K1, K2, K3 and R; at least two keys, one of which survives the loss of the main
  machine; a sealed hail is only as strong as the weakest key other than R that it goes to; a minimum class per hail,
  which the sender can raise and never lower.
- **The transport proves nothing about the sender** (§6.5, §7): only a signature can name the key that wrote a hail.
- **Secrets** (§1, §11): a hail carries no secret. `hail send` refuses one its pattern set finds; a secret no pattern
  matches can still pass, so the rule on the sender stands on its own. A pointer keeps content off the
  channel that carries the hail, and hides nothing from whoever can read the file it names.
- **Authority** (§6.1 to §6.3): the reader computes it, within its grants and its harness's permissions, which no hail
  can widen. The Owner's words carry authority only through the Owner's signature, and a bare quote carries no weight
  at all. A STOP needs the roster's stop grant.
- **Consent is never relayed** (§6.4): a hail cannot approve a permission prompt, change a setting or a seat's
  definition, or stand in for the Owner's act.
- **A forged Owner-ask, meant to phish an approval** (§8, §10b, §11): only a principal, or its stand-in, sends a hail
  `to: owner`, and `hail check` refuses any other. The Owner's signing tool fetches the ask by its id, checks its hash
  and renders it itself; nothing is signed from a command or a link inside an ask. A surface shows an ask's text as
  data, and never acts on it.
- **An altered ask or option** (§10b): the signing tool checks the ask's hash, and signs the ask's id, its hash and the
  chosen answer.
- **An answer replayed onto another ask** (§6.8, §10b): a one-time DECISION is for its own ask, and for one use; a
  standing one is cited as §10b, point 4 says. The first
  DECISION filed that ends the ask wins, a change must name the decision in force, and only the first claim acts.
- **A seat acting beyond its grants** (§6.1, §6.8): only on an accepted DECISION that it has verified itself, and only
  once.
- **A permission change** (§8, §9, §10b): two signatures, desktop and phone, over a DECISION whose `roster` names the
  roster file's sha256 before and after, which the checks match against the change committed. A change to the
  Owner's signing-keys file needs the same two signatures, except §9's first enrollment and recovery when no enrolled
  key is left, each made in person through the board's gate.
- **A signature used for another purpose** (§9): one namespace per purpose, `hail` for hails and `hail-decision` for
  DECISIONs; a signature never counts outside its namespace.
- **Notification fatigue** (§10b): only an urgent matter rings; at most 6 notices in a rolling hour; one reminder for
  an ask that doesn't ring; silence at night unless `harm-by` names a time before 07:00; and the Owner can mark a ring
  "not urgent".
- **A gate that hides a need** (§10a, §10b): intake lines, each seat's own `for: owner` QUESTION, are read from seats'
  own outboxes, and a line left without a disposition shows "not disposed". Once a week the digest lists every
  rejection and every answer from standing evidence, with its original. The hosted-team limit is stated there.
- **Indirect prompt injection,** through a hail's body or the content its pointer names (§1, §3, §4, §6.1, §6.2,
  §10b): a seat's hail carries no authority, and nothing in it widens the reader's grants or its harness's
  permissions; the Owner's words count only through the Owner's signature. A pointer names its content by sha256, and
  a reader doesn't act on a REQUEST whose pointer doesn't verify. A surface shows an Owner-ask's text as data, fenced,
  and never acts on it, and the signing tool never signs from a command or a link inside an ask. A seat steered by
  such text still meets the act check (§8a).
- **Goal hijacking** (§6.1, §6.8, §10b): a seat acts beyond its grants only on the Owner's signed DECISION, which
  signs the ask's id, its hash and the chosen answer, so it is bound to the exact ask. Before it acts, the seat checks
  that the DECISION answers the ask its own need led to, and that the act stays within what was asked. Within its
  grants, a seat's hail passes only the act check of its brief (§8a).
- **Memory poisoning** (§1, §6.1, §6.2): what a seat reads when it starts, a handover, a filed hail or any other
  record, is evidence, and carries no authority unless the Owner signed it. Authority comes only from the roster's
  grants and the Owner's signed answers.
- **Tool supply chain** (§6.1, §13): a seat acts within its grants and its harness's permissions. A rule that a seat
  uses only the tools and connectors its definition or its brief names is open (§13).
- **The act check** (§8a): a seat steered off its task can still act within its grants. `hail send` refuses, at the
  moment of sending, a seat's hail that doesn't serve the brief it names: the wrong seat, act, subject, target or
  time. It enforces declared constraints, not the truth of a message or the wisdom of a task. Its limits: hail's
  half checks hails only, and the git half is separate (§13); a spawn is only logged where its harness has no hook; a
  team-level key, or seat keys that a team's seats can all reach (§9), can't prove one seat, so such a team's hails
  that need a brief are refused when enforcing; an unsettled case is refused as ambiguous; and it starts in shadow
  mode, refusing nothing.

## 15. Privacy considerations

This section gathers rules stated elsewhere and adds none. The section named carries each rule and governs.

- **What a host sees of a sealed hail** (§9a): the opaque mailbox id, the size and the time. The signature is sealed
  with the hail, so the host can't see who sent it.
- **The Owner as a standing recipient** (§9a): `hail send` always adds the Owner's oversight keys, and `sealed-to`
  names every recipient key. This is not key escrow: no seat's private key is deposited.
- **No forward secrecy, by choice** (§9a): the record stays readable by the Owner, so a leaked oversight key exposes
  everything sealed to it. That is why the key classes exist and why oversight keys rotate.
- **Plaintext stays within the recipients** (§9a): a sealed hail's plaintext is not filed where anyone outside its
  recipients and the Owner can read it. On a platform that is also an endpoint, sensitive content sits behind a pointer;
  a short body that holds nothing sensitive may travel as plain text.
- **What a card and a push carry** (§10b): a card carries only who asks, a one-line why, where to clarify, the ask's
  options, the five answers and a pointer; the originals stay behind the pointer, sealed where §9a says. A push says
  only "an ask waits".
- **The Owner's presence** (§10b): a session keeps the time of the Owner's last real message locally. A "shown" line
  carries only the surface and the time, with the ask's id and hash.

## Appendix A. The frame's grammar in ABNF (informative)

This appendix restates §2, §3, §8a's scope block, §9a and §10b's forms in ABNF (RFC 5234). It is informative: the
prose governs. It is derived only from what the prose states; where the prose leaves a form open, the grammar leaves
it open, as a comment or as an ABNF prose value in angle brackets.

```abnf
; Values are Unicode code points; the canonical bytes are UTF-8 in NFC,
; with LF line endings and no trailing spaces (section 9).
; ABNF quoted strings match case-insensitively (RFC 5234, section 2.3).
; The frame lines, keys and markers are case-sensitive (sections 2 and
; 3), so the frame lines are written with %x values and the markers
; with %s strings (RFC 7405). The prose leaves the case of a value's
; keywords open, so they are quoted strings.
; A <prose value> marks a form the prose leaves open.
; full-date, time-hour, time-minute, time-second and time-numoffset
; are RFC 3339's.

hail             = plain-hail / sealed-hail

; --- the frame lines (section 2) ---
plain-hail       = open-line header body [ask-line] end-line
                   [signature-block [signature-block]]
                   ; a second block only on a DECISION that changes a
                   ; permission: the desktop's, then the phone's
                   ; (section 9)
open-line        = %x68.61.69.6C.2F.31 LF
                   ; exactly "hail/1"
end-line         = %x65.6E.64.20.68.61.69.6C.2F.31 LF
                   ; exactly "end hail/1"
sealed-hail      = sealed-open mailbox-line age-armor sealed-end
sealed-open      = %x68.61.69.6C.2F.31.20.73.65.61.6C.65.64 LF
                   ; exactly "hail/1 sealed"
sealed-end       = %x65.6E.64.20.68.61.69.6C.2F.31.20.73.65.61.6C.65.64 LF
                   ; exactly "end hail/1 sealed"
other-form       = %x68.61.69.6C.2F form-number [%x20.73.65.61.6C.65.64] LF
                   ; "hail/n" or "hail/n sealed", n other than 1: a
                   ; form other than hail/1, which "/1" names; the
                   ; reader answers DECLINED, "unknown form"
form-number      = <n, for which the prose gives no form>

body             = 0*5 body-line
                   ; at most five lines: every line after the pointer
                   ; line, up to the first line that begins "ask:" or is
                   ; exactly "end hail/1"; that first end line ends the
                   ; hail
body-line        = body-text LF
body-text        = <a line-text that is not exactly "end hail/1" and does not begin with "ask:">
ask-line         = %x61.73.6B ": " text-char line-text LF
                   ; one line, on REQUEST and QUESTION only, with
                   ; exactly one space after the colon
signature-block  = <the armored signature of ssh-keygen -Y sign, in its namespace>
                   ; -n hail-decision on a DECISION, -n hail on any
                   ; other hail (section 9)
                   ; from the line straight after the end line
line-text        = *( WSP / VCHAR / %x80-10FFFF )

; --- the header (section 3) ---
header           = *header-line pointer-line
                   ; the header always ends with the pointer line: the
                   ; first line that holds a pointer pair is pointer-line,
                   ; and no header-line holds one. The prose fixes no
                   ; other order. Section 3 lists from, to, id, re,
                   ; supersedes, marker, written-against, model,
                   ; decide-by, for, disposition, urgent, harm-by,
                   ; kind, raised-by, answer, scope, roster,
                   ; acting-for, handover, outcome, brief, subject,
                   ; decision and pointer.
                   ; From, to, id, re, marker, written-against and
                   ; pointer are always required;
                   ; the others are required only where section 3
                   ; says, or optional. Ask follows the body, on
                   ; REQUEST and QUESTION only; and sealed-to is in a
                   ; sealed hail's signed bytes (section 9a). A
                   ; reader ignores a key it doesn't know, except on a
                   ; DECISION, which it refuses (section 3).
header-line      = pair *( sep segment ) LF
pointer-line     = [ pair *( sep segment ) sep ] pointer-pair
                   [ sep pair *( sep segment ) ] LF
                   ; the last header line may hold other pairs, as
                   ; section 3 allows, before or after the pointer pair,
                   ; each with its own continuation segments, and it
                   ; still ends the header. The segment straight after
                   ; the pointer pair begins with a key, since a
                   ; continuation there would continue the pointer
                   ; value, which keeps its own forms
pointer-pair     = %x70.6F.69.6E.74.65.72 ": " pointer-value [ [SP] remark ]
pair             = key ": " value
                   ; exactly one space follows the colon (section 3)
key              = 1*( %x61-7A / "-" )
                   ; lower-case letters and hyphens
sep              = SP %xB7 SP
                   ; space, U+00B7, space
segment          = pair / continuation
                   ; a segment that doesn't begin with a key continues
                   ; the value before it
continuation     = segment-text [remark]
value            = component-text [remark]
component-text   = text-char *( WSP / text-char )
                   ; a value after a key's colon: it begins with a
                   ; character that is not WSP, so the one space
                   ; after the colon is the only one; holds no sep,
                   ; which this grammar doesn't exclude
segment-text     = 1*( WSP / text-char )
                   ; a continuation segment, whose first character
                   ; the prose doesn't restrict; holds no sep, which
                   ; this grammar doesn't exclude
text-char        = VCHAR / %x80-10FFFF
remark           = "(" remark-text ")"
                   ; at most one, at the end of a component; a space
                   ; before it is shown only by example
remark-text      = <one or more remark-char, with no sep anywhere among them>
remark-char      = %x20-27 / %x2A-7E / %x80-10FFFF
                   ; no parentheses

; --- values (section 3) ---
from-value       = seat-name SP session / owner-from
owner-from       = "owner" SP ( device / "pair" )
                   ; on the Owner's hails: a DECISION, a "seen", the
                   ; signing tool's hand-over to a filer (section
                   ; 10b); pair only on a DECISION with two
                   ; signatures (section 9)
device           = "desktop" / "phone"
to-value         = address *( ", " address ) / "owner"
                   ; owner: an Owner-ask, a concern sent to the
                   ; Owner, or a principal's claim on a DECISION it
                   ; acts on itself, from a principal or its stand-in
                   ; only (section 10b)
address          = seat-name [SP session]
seat-name        = team "." seat
team             = <a team name, for which the prose gives no form>
seat             = <a seat name, for which the prose gives no form>
session          = <a session id, for which the prose gives no form>
id-value         = seat-id / owner-id
seat-id          = seat-name "-" id-stamp "-" id-key
                   ; team.seat-yyyymmddThhmmZ-key (section 3)
owner-id         = "owner" "-" id-stamp "-" id-key
                   ; on the Owner's signed hails
id-stamp         = 8DIGIT %s"T" 4DIGIT %s"Z"
                   ; the UTC date, hour and minute it was written
id-key           = 6id-char
                   ; drawn at random; no counter is kept
id-char          = DIGIT / %x61-68 / %x6A-6B / %x6D-6E / %x70-74 / %x76-7A
                   ; Crockford's base32 in lower case: 0-9, and a-z
                   ; without i, l, o and u
re-value         = "none" / id-value *( id-sep id-value ) / free-text
id-sep           = <a separator between ids, which the prose doesn't give>
free-text        = 1*( WSP / VCHAR / %x80-10FFFF )
                   ; only when the thing answered has no id
supersedes-value = id-value
marker-value     = message-marker / receipt-marker / decision-marker
message-marker   = %s"INFORMATION" / %s"ADVICE" / %s"WARNING"
                 / %s"QUESTION" / %s"ANSWER" / %s"REQUEST" / %s"STOP"
receipt-marker   = %s"RECEIVED" / %s"WILCO" / %s"DECLINED" / %s"DONE"
decision-marker  = %s"DECISION"
                   ; only on the Owner's answer to an Owner-ask,
                   ; with owner-from and owner-id, signed with an
                   ; Owner signing key (sections 4, 9, 10b)
reserved-marker  = %s"DECISION" / %s"INSTRUCTION"
                   ; a seat's hail carrying either is DECLINED,
                   ; "reserved marker"; a DECISION is valid only as
                   ; decision-marker above
decide-by-value  = wa-time
                   ; in written-against's time form; on an Owner-ask
                   ; only (section 10b)

; --- the relay's values (sections 3 and 10b) ---
for-value        = "owner"
                   ; on a QUESTION only
disposition-value = "relayed" / "answered" / "rejected"
kind-value       = "allow-once" / "grant-change" / "owner-act"
                 / "decide"
                   ; on an Owner-ask
raised-by-value  = seat-name
                   ; on an Owner-ask that relays a seat's need, or a
                   ; concern a principal relays from a seat: the
                   ; raising seat (section 10b)
option-line      = "option" SP option-label ":" SP text-char line-text
                   ; a body line of an Owner-ask, one per option, as
                   ; "option A: ..." (section 10b)
urgent-value     = component-text
                   ; the one-line reason
harm-by-value    = wa-time
                   ; beside urgent only
answer-value     = "yes" / "no" / "option" SP option-label
                 / "words" / "later" SP wa-time / "talk"
                   ; on a DECISION; a reason goes in the body, and is
                   ; required with no and with words
option-label     = %x41-5A
                   ; one capital letter, as A or B; each once per ask
scope-value      = "once" / "standing"
                   ; once is the default; standing only on a
                   ; DECISION that answers an ask of kind decide
roster-value     = sha256-hex SP "->" SP sha256-hex
                   ; the roster file's sha256 before, then after
acting-for-value = seat-name
                   ; the principal the stand-in acts for
handover-value   = component-text
                   ; any value: the key itself marks the handover
outcome-value    = "unknown"

; --- the act check (section 8a) ---
brief-value      = brief-id SP sha256-hex
                   ; on a seat's hail: the brief it serves
brief-id         = <a brief's id, whose form is open>
subject-value    = subject-item *( ", " subject-item )
                   ; on a seat's STOP and REQUEST; in the scope's
                   ; subject form
decision-value   = owner-id SP sha256-hex
                   ; on an act beyond a grant, beside brief: the
                   ; DECISION's id, and the sha256 of its signed frame:
                   ; its canonical bytes (section 9), without its
                   ; signature blocks
scope-block      = %x73.63.6F.70.65.3A LF 1*scope-field
                   ; exactly "scope:", at the top of a brief, before
                   ; its prose
scope-field      = 2SP ( scope-brief / scope-seat / scope-acts
                 / scope-subject / scope-targets / scope-until
                 / scope-supersedes ) LF
                   ; indented as in section 8a's example; the prose
                   ; fixes no order. Every field but supersedes is
                   ; required
scope-brief      = %s"brief: " brief-id
scope-seat       = %s"seat: " seat-name
scope-acts       = %s"acts: " act-name *( ", " act-name )
act-name         = "hail" / "stop" / "push" / "file"
                   ; stop for a STOP, hail for any other hail; push
                   ; and file for the git half
scope-subject    = %s"subject: " subject-item *( ", " subject-item )
subject-item     = id-value / tracker-id / branch
tracker-id       = <a tracker id, whose form is open>
branch           = <a branch name, whose form is open>
scope-targets    = %s"targets: " target *( ", " target )
target           = seat-name / branch
scope-until      = %s"until: " wa-time
                   ; a time only; a condition stays in the prose
scope-supersedes = %s"supersedes: " brief-id

wa-value         = wa-item *( sep wa-item )
wa-item          = repo-commit / forge-state / wa-time
                   ; each repo@commit relied on, any forge state, and
                   ; the time, as in the example "a@1 sep b@2 sep time";
                   ; the prose fixes no order beyond that example
repo-commit      = repo "@" commit
forge-state      = <forge state, as "release v1.0.0: none", no form given>
wa-time          = full-date %x54 time-hour ":" time-minute ":" time-second
                   ( time-numoffset / short-offset )
                   ; "T" between date and time; as date -Iseconds
                   ; prints it: a numeric offset, no "Z" and no
                   ; fractional seconds (section 3)
short-offset     = ( "+" / "-" ) time-hour time-minute
                   ; as "+0100", which a reader also accepts
model-value      = <the model and the effort, as the model's name, a space, the effort>
sealed-to-value  = fingerprint *( ", " fingerprint )
                   ; every recipient key, in a sealed hail's signed
                   ; bytes (section 9a)
fingerprint      = <a recipient key's fingerprint, whose form is open>

pointer-value    = repo ":" path "@" commit SP "sha256" SP sha256-hex
                 / repo ":" path SP "sha256" SP sha256-hex
                   ; the path as at the reader's head
                 / workbench ":" path SP "sha256" SP sha256-hex
                   ; a file not yet filed
                 / "none"
sha256-hex       = 64HEXDIG
                   ; upper or lower case, the prose doesn't say
repo             = <a repository name, for which the prose gives no form>
workbench        = <a workbench name, for which the prose gives no form>
path             = <a path, for which the prose gives no form>
commit           = <a commit, for which the prose gives no form>

; --- the sealed frame (section 9a) ---
mailbox-line     = %x74.6F.2D.6D.61.69.6C.62.6F.78 ": " opaque-id LF
                   ; "to-mailbox: "
opaque-id        = <an opaque mailbox id, whose form is open>
age-armor        = <the armored output of age, whose form is age's>
```

## Appendix B. An example roster (informative)

An example, not a roster in force. It shows the roster's structure: two teams, their seats, a principal each, and the
grants in plain words. Team A runs on one machine; team B is hosted on an agent platform. Each seat is granted only
what is listed against it, and the Owner's acts at the end are no seat's, whatever is listed.

**Team A, on one machine.** Its principal is `a.auditor`. Any seat of team A can send a WARNING to the whole team.

| Seat | Granted |
|---|---|
| `a.auditor` (principal) | hail every seat of team A, and `b.auditor`; answer the same; brief every seat of team A; STOP any seat of team A, and `b.auditor`; file into the shared private repository, fast-forward only, after its record check passes; set up and start the critical reviews, each as an independent session; run a watcher, for example one that polls every minute |
| `a.planner` | hail `a.auditor`, the Builders and the Reviewers; answer every seat of team A; brief the Builders and the Reviewers; push lane branches and open their pull requests, never main, a tag, or a security branch early; mark its own pull requests ready once their review tier has found them ready; orchestrate the Builders and the standard reviews, as its subagents |
| `a.builder-*` | hail `a.planner`, and `a.auditor` straight when a need of the Owner's is urgent or `a.planner` is overdue (§8); answer every seat of team A; commit in its own worktree only, and push nothing |
| `a.reviewer-*` | hail `a.planner`, and `a.auditor` straight when a need of the Owner's is urgent or `a.planner` is overdue (§8); answer every seat of team A; commit a verdict in its own worktree only, and push nothing |
| `a.designer` | hail `a.auditor` only with a QUESTION `for: owner` that raises a need of the Owner's (§10b, point 1); answer every seat of team A |

**Team B, hosted on an agent platform.** Its principal is `b.auditor`. Any seat of team B can send a WARNING to the
whole team.

| Seat | Granted |
|---|---|
| `b.auditor` (principal) | hail every seat of team B, and `a.auditor`; answer the same; brief every seat of team B; STOP any seat of team B, and `a.auditor`; push for every seat of team B, the only one that does, to branches only, and a branch only once its review tier has found it ready; file into the shared private repository, fast-forward only, after its record check passes: its own records and team B's filings; check every commit of team B a second time before it pushes (hashes, the record check, no secret); run a watcher, for example a 15-minute routine that may start up to 7 minutes early or late, so up to 29 minutes between polls |
| `b.*`, the rest of team B | hail each other and `b.auditor`; answer every seat of team B; push nothing, and hand work over to `b.auditor` by sha256 |

**Wake intervals** (§5, §8): each seat of team B declares 0, since the platform's own message wakes it.

**Across both teams:**
- Every seat can answer its whole team, because any teammate can WARN it and its principal can STOP it. The principals
  also answer each other. Across teams, only the principals hail each other.
- Reads within a team are free and read only: never a fetch, never a write, never the Owner's checkouts.
- A need of the Owner's goes hop by hop: a Builder or a Reviewer to `a.planner`, then to `a.auditor`, and
  `a.designer` to `a.auditor`; on team B, straight to `b.auditor`. An urgent need, or one whose hop is overdue, goes
  straight to the principal. Only `a.auditor` and `b.auditor`, or a stand-in while it stands in, send hails
  `to: owner` (§10b).

**The act check** (§8a): its mode is `shadow`. Each principal's own scope is its seat definition, which the Owner
signs into the roster.

**Stand-ins** (§10b): `a.planner` stands in for `a.auditor`, and files for team A while it does. Team B's stand-in is
proposed by `b.auditor`, for the Owner's approval (Appendix D, D.12).

**Sealing, per channel:**
- git: plain unless a hail opts in;
- the local socket: plain unless a hail opts in;
- team B's designated hosted channel: plain unless a hail opts in, because its platform is also the endpoint
  (Appendix D, D.3);
- a relay: sealed by default. None exists; it is a later tier (§7).

**Keys:** each seat of team A lists its own signing key (§9), which names the seat only where that seat alone can
use it (Appendix D, D.4); team B's seats sign with its team-level key, held by `b.auditor`. Each seat lists its
encryption key (§9a). Every key is a placeholder until it is made.
The Owner's oversight keys are a K2, in the machine's own chip, and a K3, a hardware key you carry. A sealed hail goes
to every oversight key at or above its minimum class, and to R whenever R exists; lower classes are left out. With no
R, "K3 only" needs a second K3. The Owner's signing keys, desktop and phone, sit in their own file, not in the roster
(§9).

**The Owner's acts, which no grant can name:**
- a merge into main, with the forge's merge button;
- a merge into any other base, local and signed;
- a tag, signed;
- a signed answer, through hail or the board;
- a seat's definition, typed by the Owner;
- a force-push or a history purge;
- the forge's releases and repository settings.

## Appendix C. Examples (informative)

One complete hail per marker, then one signed hail. Frames only: once signing is required (§9), the hails across teams
carry a signature block, as in C.12. Ids, times and placeholders are illustrative; `records` stands for the shared
private repository, and `workbench` for the sender's workbench. The seats are those of Appendix B, with two Builders
and two Reviewers on team A.

**C.1 INFORMATION.** A result crosses to team B as a pointer. Through git, a watcher rings `b.auditor`; the RECEIVED
that comes back goes to the sender's digest.

```text
hail/1
from: a.auditor ‹session› · to: b.auditor
id: a.auditor-20261108T1002Z-w97m7a · re: none · marker: INFORMATION
written-against: records@‹commit› · 2026-11-08T11:02:15+01:00
pointer: records:results/lane-4.md@‹commit› sha256 ‹hex›
Lane 4 is built and reviewed. Its result is behind the pointer.
end hail/1
```

**C.2 ADVICE.** One of two Reviewers advises the Planner; the decision stays with the Planner.

```text
hail/1
from: a.reviewer-1 ‹session› · to: a.planner
id: a.reviewer-1-20261108T0941Z-qr04p6 · re: a.planner-20261108T0930Z-qh33f3 · marker: ADVICE
written-against: shoalmark@‹commit› · 2026-11-08T10:41:07+01:00
pointer: workbench:out/to-a.planner/review-lane-4.md sha256 ‹hex›
Lane 4 can merge as it stands. The review behind the pointer says why.
end hail/1
```

**C.3 WARNING.** One WARNING to five seats starts five clocks; each seat's answer stops only its own.

```text
hail/1
from: a.planner ‹session› · to: a.auditor, a.builder-1, a.builder-2, a.reviewer-1, a.reviewer-2
id: a.planner-20261108T1230Z-w65254 · re: none · marker: WARNING
written-against: shoalmark@‹commit› · 2026-11-08T13:30:44+01:00
pointer: none
The forge has refused every push since 13:25.
A push now may land in part.
end hail/1
```

**C.4 QUESTION.** A question across teams, principal to principal.

```text
hail/1
from: a.auditor ‹session› · to: b.auditor
id: a.auditor-20261108T1115Z-xnqxnt · re: none · marker: QUESTION
written-against: records@‹commit› · 2026-11-08T12:15:40+01:00
pointer: records:results/lane-4.md@‹commit› sha256 ‹hex›
Lane 4's result went to team B this morning.
ask: Did team B's review of it find anything?
end hail/1
```

**C.5 ANSWER.** One Builder's ANSWER to the Planner's QUESTION; it quotes the question's id in `re` and stops only its
own seat's clock.

```text
hail/1
from: a.builder-1 ‹session› · to: a.planner
id: a.builder-1-20261108T1152Z-v769d3 · re: a.planner-20261108T1140Z-s6a7rp · marker: ANSWER
written-against: shoalmark@‹commit› · 2026-11-08T12:52:10+01:00
pointer: none
Yes: lane 4's tests pass at ‹commit›.
end hail/1
```

**C.6 REQUEST.** The Planner asks a Builder to build lane 4.

```text
hail/1
from: a.planner ‹session›
to: a.builder-1
id: a.planner-20261108T0812Z-k8chqr
re: none
marker: REQUEST
written-against: shoalmark@‹commit› · 2026-11-08T09:12:40+01:00
pointer: workbench:out/to-a.builder-1/lane-4.md sha256 ‹hex›
Build lane 4 from the brief behind the pointer.
Work in your own worktree.
ask: Either DECLINED, which ends it, or WILCO followed by DONE with the commit.
end hail/1
```

**C.7 STOP.** Through git, principal to principal.

```text
hail/1
from: b.auditor ‹session› · to: a.auditor
id: b.auditor-20261108T1320Z-yp0kcg · re: none · marker: STOP
written-against: shoalmark@‹commit› · records@‹commit› · 2026-11-08T14:20:03+01:00
pointer: records:notes/hold-lane-4.md@‹commit› sha256 ‹hex›
Hold the push of lane 4.
The reason is behind the pointer.
end hail/1
```

**C.8 RECEIVED.** `b.auditor` needs a teammate's word before it can answer C.4, so it sends RECEIVED. Its pointer, with no
`@<commit>`, is the "path at the reader's head" form of §3, with a remark.

```text
hail/1
from: b.auditor ‹session› · to: a.auditor
id: b.auditor-20261108T1131Z-y633ec · re: a.auditor-20261108T1115Z-xnqxnt · marker: RECEIVED
written-against: records@‹commit› · 2026-11-08T12:31:09+01:00
pointer: records:results/lane-4.md sha256 ‹hex› (verified on team B's host)
end hail/1
```

**C.9 WILCO.** The Builder accepts C.6.

```text
hail/1
from: a.builder-1 ‹session› · to: a.planner
id: a.builder-1-20261108T0814Z-mv87v0 · re: a.planner-20261108T0812Z-k8chqr · marker: WILCO
written-against: shoalmark@‹commit› · 2026-11-08T09:14:02+01:00
pointer: none
end hail/1
```

**C.10 DECLINED.** The second Builder's brief changed after the REQUEST for lane 5, so its pointer doesn't verify, and
the Builder acts on nothing.

```text
hail/1
from: a.builder-2 ‹session› · to: a.planner
id: a.builder-2-20261108T0815Z-q8hxvg · re: a.planner-20261108T0813Z-nv585s · marker: DECLINED
written-against: shoalmark@‹commit› · 2026-11-08T09:15:30+01:00
pointer: none
pointer differs: ‹hex›
end hail/1
```

**C.11 DONE.** `a.auditor` passed C.7 on to the Planner as its own STOP, `a.auditor-20261108T1321Z-zb530w`. The
Planner holds the push, records why, and answers DONE with `pointer: none`.

```text
hail/1
from: a.planner ‹session› · to: a.auditor
id: a.planner-20261108T1326Z-f67t1m · re: a.auditor-20261108T1321Z-zb530w · marker: DONE
written-against: shoalmark@‹commit› · 2026-11-08T14:26:51+01:00
pointer: none
The push of lane 4 is held; nothing was pushed.
end hail/1
```

**C.12 A signed hail.** C.7, signed. The signed bytes run from `hail/1` through `end hail/1`, with exactly one LF after
it; the armored signature that `ssh-keygen -Y sign -n hail` writes starts on the next line.

```text
hail/1
from: b.auditor ‹session› · to: a.auditor
id: b.auditor-20261108T1320Z-yp0kcg · re: none · marker: STOP
written-against: shoalmark@‹commit› · records@‹commit› · 2026-11-08T14:20:03+01:00
pointer: records:notes/hold-lane-4.md@‹commit› sha256 ‹hex›
Hold the push of lane 4.
The reason is behind the pointer.
end hail/1
-----BEGIN SSH SIGNATURE-----
‹signature›
-----END SSH SIGNATURE-----
```

## Appendix D. The reference deployment (normative for that deployment only)

The reference deployment is the one hail/1 was written in: team A on one machine and team B hosted on an agent
platform, with the seats of Appendix B. Below are its own rollout rules, its exceptions for one channel and one team,
its seats' forms and the software it relies on. They bind that deployment only. The
body names each item where it would otherwise sit.

- **D.1 Before force** (Status): until both force conditions hold (the Owner's two-signature approval, through hail
  itself or through the board, and the Owner's signed roster commit), the rules before them stay in force.
- **D.2 Overdue ids** (§5): beside the next-turn check, until a watcher exists, the team's evening count (its
  end-of-day tally) lists every overdue id.
- **D.3 Team B's designated hosted channel** (§9a): the agent message of the hosted agent platform that team B runs on
  is unsealed by default, because that platform is also its endpoint, so the default would hide nothing from it. A
  hail MAY still opt in. The general rule of §9a holds for every other channel.
- **D.4 The teams' signing keys** (§9): on team B, the nearest is one team-level key, held by its principal,
  `b.auditor`. Team A's seats each hold a key of their own. Where they can reach each other's keys, those keys name
  the team, not the seat (§9), and the act check (§8a) can't prove a seat there, so it can't enforce there.
- **D.5 Its own seats' hails** (§10): the reference deployment's own seats' hails stay in its private record, and
  MUST NOT be in its public repository's tracker. They carry private ids, private hashes and pointers to the Owner's
  records.
- **D.6 When the tools come** (§11): `hail check` and `hail send` are the first milestone once this edition is out.
  The trial (D.8) runs on their first running version.
- **D.7 How the tools are built** (§13): the choice of how `hail check` and `hail send` are built goes to the board as
  its own signed question.
- **D.8 The trial** (§7, §13): a six-day trial on two teams, of the simplest setup of §7, on the first running
  version of `hail check` and `hail send` (D.6), counted daily. It measures round trips, the Owner's nudges and
  allows, tokens, failures and the watchers' restart overhead. It also counts the Owner's answers given as words in a
  seat's chat against those given as signed answers: hail/1 is meant to replace the first with the second. The relay
  tier of §7 is decided on its numbers.
  - **Before it starts on team B:** team B's watcher wakes its own seat with no nudge from the Owner, and an ANSWER,
    a DECLINED or a DONE to the seat's own hail, a digest-only receipt and an overdue response are each shown once.
  - **Until the roster is in force,** a seat that needs the Owner's allow to answer counts each allow as an Owner act.
- **D.9 Releases** (§13): the release for `hail check`, and later for `hail send`, is each its own tracked item once
  this spec is in force, and it MUST NOT be a patch release.
- **D.10 Its seats' forms** (§7, §10): team A's filer is `a.auditor`, and team B's is `b.auditor`. On team A, a parent
  reaches its subagent by the agent's id.
- **D.11 The software it relies on** (§13), each fact as of the version it names:
  - **`age`:** 1.3 or later is needed only to seal: a hail that opts in, or a relay. Team B's designated hosted
    channel is unsealed by default (D.3);
  - **the `age` plugin for one platform's chip, v0.2.1,** makes `age1tag` keys with `--recipient-type tag` (its
    default is still the plugin's own type). Its access control includes biometric-only options; its default is
    "any-biometry-or-passcode". It needs a minimum version of that platform's operating system, and the chip;
  - **the `age` plugin for a hardware key you carry, v0.5.1:** `age1tag` support is in its unreleased changes only. A
    K3 oversight key MUST be an `age1tag` key. Until the release, a K3 key waits; it MUST NOT use a build from source
    unless that build produces `age1tag` keys. A key of the plugin's own recipient type, not `age1tag`, is not a
    conforming oversight key;
  - **the `age` plugin for the chip on the other platforms, v1.0.1,** makes `age1tag` keys by default, gated by a PIN
    only. Its platforms are not listed;
  - **a native implementation:** the Rust `age` crate 0.12.0 encrypts to `age1tag` and `age1tagpq` natively;
  - **post-quantum with a chip:** only the first chip plugin's `--pq` option is a candidate, and its key prefix is
    unverified.
- **D.12 The relay here** (§10b):
  - **the Owner's time zone** is the one the Owner sets;
  - **the Owner's signing-keys file** (§9) is the board's allowed-signers file, `work-tracker/allowed_signers`, which
    the board's gate guards as the Owner's alone. hail reads that same file;
  - **team A's stand-in** is `a.planner`, which also files for team A while it stands in, under team A's push rules;
  - **team B's stand-in** is proposed by `b.auditor`, for the Owner's approval. *Placeholder:* it is written here
    once approved;
  - **intake:** on team A, the Owner's digest reads each seat's own outbox. Team B's seats are hosted, so the
    hosted-team limit of §10b applies to them;
  - **the surfaces:** team A's harness sessions; team B's hosted chat, a surface that shows only on its next turn
    (§10b, point 6); and the companion app. Team B's hosted chat doesn't make the Owner's signature itself: it points
    the Owner to a surface on the Owner's own device that can sign, and where none can, point 15 of §10b applies.

## Open questions

Every open point is in §13.

## Changes

- hail/1, first edition.
- Before its announcement, the first edition was fixed in place after an outside review, and the fixes were reviewed
  independently. It keeps its name.
