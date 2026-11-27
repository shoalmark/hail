# hail-grammar/1

Testable draft, unsigned. This edition specifies data and a procedure; it ships no checker. Implementation
and shadow use follow in the next stage. A grammar has its own edition, starts small and grows by use.

## 1. Scope

A grammar file holds three kinds of rule and no more: the number of body lines each marker may take;
openers with their meanings; and the allowed and required openers per marker. Identification metadata
names the format, grammar, edition and whether it is a start; it adds no fourth kind of body rule.
There are no word lists, per-opener lengths, source rules or required result pointers in this format.
A grammar may tighten hail/1 and never loosen it. It changes no header, source, signing step, grant,
marker meaning or filed hail's effect. hail/1 alone defines settings, citations and filing refusal.

## 2. File form

A grammar is UTF-8 TOML. Duplicate keys or invalid TOML refuse it. The first line names the local
JSON Schema: `#:schema ./grammar.schema.json`. It is a comment for TOML, not an extra data key.
Unknown keys at any level refuse the file. These are all its data fields:

| Field | Value |
|---|---|
| `hail-grammar` | integer 1, the edition of this format |
| `name` | 2–32 lower-case letters, digits or hyphens, a letter first |
| `edition` | nonnegative integer, independently incremented for this grammar |
| `start` | boolean; true identifies a starting grammar, not a claim of completeness |
| `openers` | nonempty table keyed by opener, each value a table with only `means`, a nonempty string |
| `markers` | exactly one table for each marker below, each with `lines`, `allow`, `require` |
| `markers.<marker>.lines` | nonnegative integer; maximum body lines, not a minimum |
| `markers.<marker>.allow` | array of distinct opener names; empty allows no prose lines |
| `markers.<marker>.require` | array of distinct opener names; empty requires none |

An opener matches `[A-Z][a-z]*( [a-z]+){0,2}`. The colon and following space delimit it exactly:
`Not done` and `Not done today` can coexist lexically; no prefix match chooses an opener. A meaning
must contain a non-space character. All fields above are required; there are no implicit marker rows
or implicit allow lists. The marker rows, in diagnostic order, are:

`INFORMATION`, `ADVICE`, `WARNING`, `QUESTION`, `ANSWER`, `REQUEST`, `STOP`, `RECEIVED`, `WILCO`,
`DECLINED`, `DONE`, `START`, `END`, `CLAIM`, `RELEASE`, `FORCED END`, `AWAY`, `BACK`, `DECISION`.

`INSTRUCTION` is reserved and cannot be made valid by a grammar. Unknown hail markers use the
INFORMATION row, as hail/1 says. Every allowed or required opener must exist. Every required opener
must be allowed. The required set must fit the line budget: one line cannot satisfy two openers.

## 3. Body form

Read the canonical body delimited by hail/1, after its stated sources and before its ask or end.
The body line number starts at 1. Sources, the header and the ask are not body lines. Every physical
body line counts, even an empty one. There is no line-character budget in this edition.

A prose line is exactly `Opener: said`, with one space after the colon and nonempty said after
trimming spaces. The canonical empty form `Opener:` is recognized as an empty statement at B5, because hail
canonicalization removes its trailing space; it is not also a delimiter fault. No leading space, opener case folding, multi-opener splitting or prefix guessing
is done. A second colon is part of said. An opener can occur several times unless a later edition
says otherwise; a required opener needs at least one nonempty permitted occurrence.

Structured `option A:` through `option E:` lines on an Owner `kind: decide` QUESTION are hail/1
form, not prose openers. They count toward the marker's line budget but skip opener and meaning
checks here; hail/1 checks their labels, order and use. A grammar cannot manufacture those lines
on another kind of hail. It may have too small a budget or require a prose opener incompatible with
that form: such a hail cannot be filed under that grammar. The starting grammars allow five QUESTION
lines and require no QUESTION prose opener, so the existing choice form can be used.

## 4. Procedure and diagnostics

First validate the grammar file, then check each body. An invalid grammar refuses filing before its
body rules can be applied. Run every independent rule, collecting faults; do not stop at the first.
Unknown or mistyped containers suppress only dependent child checks. A grammar-definition fault is
located at `grammar <field>`. Body faults use `body line n`; missing required openers use
`header marker`, since the missing line has no position. Codes below are stable in this draft.

F1 uses the literal location `grammar file`. F4 uses `grammar markers.<MARKER>.allow` or
`grammar markers.<MARKER>.require`, naming the list being checked, with no array index.
F5 and F6 use `grammar markers.<MARKER>.require`.

Print `location: [code] problem; repair.` on one physical line. Substitute actual values for variables.
Order grammar shape errors by UTF-8 field path, then common-set errors in common-set order, then
marker references in the marker order above (allow before require, array order), then conflicts,
then meaning pairs in opener-name order. Collect body faults in procedure order, then print them in hail/1 location order: header faults
first (re before marker), then body line numbers. At one body line, budget precedes form, permission,
empty statement and meaning. Required-opener faults at header marker follow require-array order. Preserve separate lines
for independent faults. A missing required opener remains a distinct fault when another line has
an unknown opener; it tells the writer what must be supplied, without guessing their intent.

| Step | Check and fault code | Printed line after the location |
|---|---|---|
| F1 | TOML cannot be decoded, `g-toml` | `[g-toml] TOML cannot be read; write a UTF-8 TOML file with unique keys.` |
| F2 | missing field, wrong type, unknown key or invalid range/name, `g-shape` | `[g-shape] field is missing or has an invalid value; write <the field's form from §2>.` For an unknown key: `[g-shape] key is not part of this edition; remove <key>.` |
| F3 | common set | checked by §5 |
| F4 | name in allow or require is undefined, `g-reference` | `[g-reference] opener <O> is not defined; define <O> or remove it from this list.` |
| F5 | a required opener is not allowed, `g-conflict` | `[g-conflict] required opener <O> is not allowed; add <O> to allow or remove it from require.` |
| F6 | required count exceeds lines, `g-capacity` | `[g-capacity] <R> required openers exceed <N> lines; increase lines to at least <R> or reduce require.` |
| F7 | same meaning under two openers | checked by §6 |
| B1 | more than N body lines, `g-lines` | `[g-lines] <MARKER> allows at most <N> body lines; remove or combine <excess> line(s) without dropping meaning.` Locate the first excess line, N+1; one budget fault, not one per excess line. Use singular `line` for one. |
| B2 | line has no exact opener delimiter, `g-opener` | `[g-opener] line has no exact opener; <choices repair>.` |
| B3 | opener not defined, `g-opener` | `[g-opener] opener <O> is not defined; <choices repair>.` |
| B4 | defined opener not in allow, `g-allow` | `[g-allow] <O> is not allowed on <MARKER>; <choices repair>.` |
| B5 | no statement after opener, `g-empty` | `[g-empty] <O> has no statement; write <O>: followed by one space and <M>.` |
| B6 | meaning and writer duties | checked by §§5–7 |
| B7 | missing nonempty allowed occurrence of a required opener, `g-required` | `[g-required] <MARKER> requires <O>; add <O>: followed by one space and <M>.` |

These templates fix the words, punctuation and spaces of every fault line; variables alone vary.
`M` is the exact declared meaning for O (the fixed table below for a common-set fault).
`Q(X)` encloses X in ASCII double quotes, escaping backslash and double quote with a backslash,
and U+0000 through U+001F as lower-case `\u00xx`. All other characters retain their UTF-8 bytes.
For unquoted substitutions use the same escapes without the enclosing quotes. Field paths use the
keys joined by dots, with no TOML quoting; the location of a missing field is that field's path.

The **choices repair** is deterministic. In the selected marker's allow-array order, join entries
`Q(O + ":") = Q(M)` with ` | `. Print `write one of <entries> with one space after the colon and
a statement of that meaning; if none fits, remove this line and preserve its meaning in a hail
whose marker allows it`. If allow is empty, print `remove this prose line and preserve its meaning
in a hail whose marker allows it; <MARKER> allows no prose opener`. The outer template supplies
the final period. Do not invent a different observation or commitment to fit a permitted opener.
For example, WILCO's only choice is Next with its exact next-act meaning; an observation cannot
be repaired merely by relabeling it Next. An optional observation can instead be preserved in an
INFORMATION hail. Empty and required-opener repairs also print M, never just a request for text.

For F2's `<the field's form from §2>`, substitute exactly the following text (the outer template
adds the final period). For an invalid opener key use the `openers.<opener>` row's name repair;
for its non-table value use its table repair. Unknown keys use F2's separate removal template.

| Field | Exact replacement form |
|---|---|
| hail-grammar | `the integer 1` |
| name | `a string of 2–32 lower-case letters, digits or hyphens, with a letter first` |
| edition or markers.<marker>.lines | `a nonnegative integer` |
| start | `true or false` |
| openers | `a nonempty table of opener tables, each with only a nonempty means string` |
| openers.<opener>, name | `an opener name matching [A-Z][a-z]*( [a-z]+){0,2}` |
| openers.<opener>, table | `a table with only a nonempty means string` |
| openers.<opener>.means | `a string containing a non-space character` |
| markers | `a table with INFORMATION, ADVICE, WARNING, QUESTION, ANSWER, REQUEST, STOP, RECEIVED, WILCO, DECLINED, DONE, START, END, CLAIM, RELEASE, FORCED END, AWAY, BACK and DECISION rows, each with lines, allow and require` |
| markers.<marker> | `a table with nonnegative integer lines and distinct opener-name arrays allow and require` |
| markers.<marker>.allow or .require | `an array of distinct opener names` |

For each line, read B2/B3 first. If its opener is known, check B4 and B5 independently; check B6 only
when there is a nonempty statement. A forbidden opener still has a meaning, so B6 can run there.
A missing statement does not count toward B7. F7 skips invalid or reserved opener definitions. F5 only compares defined openers; F6 can run when the
require list and lines are well formed. This limits cascades while retaining independent faults.

## 5. Common openers

Every grammar contains exactly these common opener names, byte for byte and case sensitive:
`Fact`, `Not done`, `Next`, `Correction`, `Say again`. Their `means` values are the exact strings
below, not a publisher's paraphrase. A grammar can add openers but cannot change a common meaning.
A marker need not allow every common opener; the common set belongs to the grammar, not every row.

Only the Owner changes this set by a new edition of hail-grammar, in batches. Earlier editions and
the files pinned to them remain readable. A grammar's own new edition cannot change the common set.

At F3, missing common opener O prints `grammar openers.O: [g-common-missing] common opener O is
missing; add [openers.<Q(O)>] with means = <Q(M)>.` A changed meaning prints `grammar openers.O.means:
[g-common-meaning] common meaning differs; write means = <Q(M)>.` Unknown child keys
are F2 shape faults; do not double-report them as changed meaning.

| Opener | Exact means string |
|---|---|
| `Fact` | `what was observed, with its value` |
| `Not done` | `what was left out or not checked, and why; nothing when nothing was left out` |
| `Next` | `the sender's own next act, or who acts next and on what condition` |
| `Correction` | `the wrong value and its replacement in the hail named by re` |
| `Say again` | `the wanted part of the hail named by re: a source number, body line number or header name` |

At B6, the reader checks the stated use against the declared meaning. This is a reading of the
statement, not a claim to prove observations true. Fact states the observation and its value. Next
states the sender's own next act, or identifies the next actor and the condition for that act.
Not done states the omission and why, or states nothing was omitted. A mismatch prints
`body line n: [g-use] O does not carry its declared meaning; write <the exact means string>.`

Correction gives the wrong value and the right one, and `re` must be exactly one valid hail id naming
the corrected hail. No `supersedes` is needed merely to correct a value; withdrawal still follows
hail/1. A missing value pair prints `body line n: [g-correction] correction lacks the wrong or right
value; name the wrong value and give its replacement.` A missing or non-single id prints
`header re: [g-correction-re] correction names no single hail; write the corrected hail's id.`
One re fault is printed per hail, even with several Correction lines. The value fault is distinct.
Do not also print g-use for these two specific faults.

Say again identifies exactly the wanted part by `source N` (1–9), `body line N` (positive integer),
or `header key` (a hail/1 header key), of the one hail named by `re`. It may add why that part is wanted.
It asks for a part of the earlier hail; it is not a citation in the current hail. If the received hail
is supplied, check that the part exists there. Otherwise report that existence was not checked and
do not guess. A missing or invalid part prints `body line n: [g-part] wanted part is not identified;
write source N, body line N or header key of the received hail.` A nonexistent part prints
`body line n: [g-part] named part does not exist; write an existing source number, body line or header name.`
A missing or non-single re prints `header re: [g-repeat-re] repetition names no single hail; write the
received hail's id.` Do not also emit g-use for a specific part/re fault.

An ANSWER holds no question, even one without a question mark or hidden after a statement. It may
quote a question as the object being answered if it asks the reader nothing. A question requesting
information prints `body line n: [g-answer] ANSWER asks a question; state the answer here and file
any new question as QUESTION.` A question in the body is a meaning check, not a punctuation test.
If two lines ask questions, each has its own fault. hail/1 already forbids an ask line on ANSWER.

For B6 faults on `re`, print them before body faults under hail/1's location order; within a body line,
check the opener's specific meaning first and the ANSWER rule second. Without comparison evidence,
report `meaning evidence not supplied` for checks that need it, rather than claiming a complete check.

## 6. Meanings in a grammar

In a grammar no two openers mean the same. At F7 the reader compares each pair of definitions, in
opener-name order, considering their meaning rather than only their bytes. A synonym or paraphrase
is not a new meaning. Overlapping subject matter is possible; the same kind of statement with the
same purpose under two names is refused. For example, Seen defined as an observed condition and its
measured value duplicates Fact, even though the definitions are not byte-identical.

Print `grammar openers.B.means: [g-meaning] B means the same as A; remove B or give it a distinct
meaning.` Here A is the earlier name in UTF-8 order and B the later one; one line per duplicate pair.
A schema cannot decide this. An accepting reader must perform and record the meaning review beside
schema validation. A program can flag identical strings, but that alone cannot claim this check
complete. If the meaning distinction cannot be judged, report it as not checked and do not accept
that grammar for filing until the review is supplied. No grammar-maker code is needed.

## 7. Writer duties

Every writer keeps these rules under every grammar:

- Never drop **not**, **no**, **never** or **only** when shortening a statement; keep their scope too.
- Numbers, paths, commit ids and code stay as they are. A correction explicitly identifies the old
  value and supplies the new one; shortening never silently changes it.
- Write STOP and WARNING in whole sentences after each opener.
- There is no Limit opener. A bound belongs in the line that states the work.
- There is no Do opener. The hail's marker already says what kind of request it is.

Do and Limit as opener keys are refused at F2 with `grammar openers.O: [g-reserved] O is not an
opener of this edition; remove O and state the work and its bounds in an allowed line.` Their use
in a body whose grammar does not define them is the ordinary B3 undefined-opener fault.

At B6, when an original statement is supplied for a shortening, compare the protected words and
literal values with it and read the scope of each negation or restriction. One lost or changed item
prints `body line n: [g-preserve] protected text X was lost or changed; restore X with its original
scope and bytes.` Emit one line per distinct protected item, in original occurrence order. An
unchanged token with changed scope also fails. Without the original, preservation is not checked;
a reader cannot certify it from the shorter statement alone. This rule neither proves the original
true nor forbids a separately declared Correction.

For STOP or WARNING, each prose line that is not a whole sentence prints `body line n: [g-sentence]
STOP or WARNING needs a whole sentence; write the statement as a complete sentence after its opener.`
Punctuation alone is insufficient. Imperative sentences are complete. Evaluate after opener meaning,
then preservation, then sentence completeness. These are reader reviews; a shape-only program must
report them not checked, never imply they are proved. The writer's duty remains even without an
original supplied to the check. No rule silently deletes a word to fit a budget.

## 8. Starting files and later work

[swe.toml](swe.toml) starts software engineering; [brand-comms.toml](brand-comms.toml) starts brand and
communications. Both are edition 0, `start = true`. Their names describe their work; they make no
claim about a registered name. Each REQUEST takes at most three body lines, excluding its ask and
sources. Use may change that budget by a new grammar edition. QUESTION allows five to accommodate
the existing Owner choice form. DONE starts at three lines with no required opener; this is a trial
choice, not a settled requirement for all later grammars.

The common meanings are identical in both files. Propose offers a course, while Next commits the
sender's next act or identifies who acts next under a condition. Verdict expresses a review judgment;
Fact expresses observation. In brand-comms, State expresses a publication stage and Basis identifies
materials a piece uses. They are related subjects, not alternative names for one meaning.
No word list constrains Verdict or State; their declared meanings still apply.

**Later, not grammar fields of this edition:** a REQUEST required to name its reason; a DONE required
to point to its result; word lists after an opener; values an answer must repeat; and a separate
length for one opener. Unknown fields trying to express these are refused by the closed file form.
A writer can already include such information voluntarily; a grammar file cannot require it here.

**Not decided; use decides:** the eventual line budgets; whether Fact should split; which openers
DONE should require; whether a marker should name its allowed answers; and whether hails should be
capped by elements. The last two wait for the written data-link rules to be read word for word.
The supplied research's data-link claims were held back by its critical read; they do not justify
this draft's budgets or answer sets. This draft makes no empirical claim that three lines is optimal.

## 9. Measurement

Stage B takes the shadow measure; this draft states it and claims no new measured saving.
Count totals per closed REQUEST: the request, every hail of its exchange through terminal DONE,
DECLINED or withdrawal, including correction/repetition side threads, and every file first added
for that request. Include a file made only to move words out of a body, even if no source ultimately
cites it. Count a shared file once, attributing it to the earliest request for which it was first
added; list the allocation. Existing files are excluded, but newly added versions and their cost
are reported separately so rewriting a short initial file cannot hide cost. Report the closure
rule, unmatched requests and unreadable sources, never silently drop them.

For text, count Unicode scalar values in the exact canonical hails and committed file contents,
including framing, headers, sources and final line ends. Count each hail id once; a retry's extra
transport cost is reported separately. For each new file use its complete final content at closure,
including content removed later in a separate churn total. Report bytes alongside characters for
all files, and binary bytes separately; do not call undecodable bytes characters.

Also count the full grammar text a bot reads in each session, including repeated common rules and
any procedure text actually read. Report per-session and aggregate characters, bytes and sessions.
Count hails containing Correction and hails containing Say again per hundred hails, separately and
as a union; a hail containing both increments both separate counts but only once in the union.
Use zero events over zero hails as undefined, never as a rate of zero.

Compare with the 26 November baseline and a plain `write briefly` control. Use matched tasks and
closure criteria, identify settings and grammar editions, and report failures, retries and unresolved
work alongside medians, p90 and totals. The control has no grammar file and still follows hail/1.
The supplied baseline has 179 closed requests, median 1,727 hail characters, p90 2,774 and a total
333,201 hail characters. Files first added occurred in 75 requests and totalled 1,480,796 bytes.
Its combined headline mixed hail characters with file bytes; it is not a character total. Preserve
those units and recompute comparable totals from retained baseline files before asserting savings.
The earlier blind rewrite trial reduced body text, but did not establish a reduction in total cost.
No threshold or optimal line budget is inferred from it.

## 10. Schema and cases

[grammar.schema.json](grammar.schema.json) is JSON Schema draft 2020-12. Parse TOML to its JSON
object model, then apply the schema without coercion: a boolean is not an integer. The schema closes
all objects and checks metadata, types, names, nonempty definitions, common names and exact meanings,
all marker rows, nonnegative budgets and unique lists. Its references are all local; no network is
needed. TOML decoding is F1, schema shape is F2, and common-set schema failures are reported at F3
with the common-specific codes. Reserved Do/Limit names use g-reserved, not a second g-shape line.

Beside the schema, the reader checks opener references, required-allowed membership, capacity and
pairwise meaning distinctions. The schema cannot hold the last rule: two different strings can
mean the same. The synonymous-opener case deliberately passes the schema and is refused by F7.
Body meanings, preservation, sentence completeness and body sufficiency also cannot be certified by
schema validation. No custom executable is embedded in a grammar.

The companion case suite in the vector repository at `hail-grammar/` supplies complete inputs,
expected fault lines and hand traces. Cases exercise both sides of budgets, missing requirements,
unknown/disallowed/empty openers, multiple faults, common meanings, semantic duplicates and writer
duties. Running by hand means executing F1–F7 and B1–B7 in order, recording the specific comparisons
and the complete emitted lines. It is not a run of the superseded prototype. Filing consequences
are hail/1's and tested in its current-edition suite.

## 11. Sources

These are the draft's one source list. Single words and procedures informed the design; no standard's
wording is copied. The research available for this draft was a supplied record and its critical read,
not a new retrieval of the linked works. The held data-link claims remain held under §8.

- Standard Marine Communication Phrases, resolution A.918(22), for distinct markers, correction,
  repetition and batched maintenance of a common set.
- [SERA.14045](https://regulatorylibrary.caa.co.uk/923-2012/Content/Regs/03440_SERA14045_Transmitting_technique.htm),
  for stable meanings of procedure words.
- [SERA.14075](https://regulatorylibrary.caa.co.uk/923-2012/Content/Regs/03620_SERA14075_Exchange_of_communications.htm),
  for correcting a stated value and requesting a named part again.
- [JSON Schema validation, draft 2020-12](https://json-schema.org/draft/2020-12/json-schema-validation),
  for closed data shapes, properties and bounds; semantics remain this draft's.
- TOML, for the grammar file's data notation.
- The retained 26 November baseline and blind rewrite trial, for whole-exchange measurement and the
  observed limits of short bodies. They supply observations, not normative wording or public identities.

Stage A specifies and hand-runs these cases. Stage B implements after 1 December and takes the shadow
measure. No claim of independent implementation readiness is made by this writer's own hand run.
