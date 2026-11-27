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
trimming spaces. No leading space, opener case folding, multi-opener splitting or prefix guessing
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

Print `location: [code] problem; repair.` on one physical line. Substitute actual values for variables.
Order grammar shape errors by UTF-8 field path, then common-set errors in common-set order, then
marker references in the marker order above (allow before require, array order), then conflicts,
then meaning pairs in opener-name order. Body order is budget first, then each line in order (form,
permission, meaning), then missing required openers in require-array order. Preserve separate lines
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
| B2 | line has no exact opener delimiter, `g-opener` | `[g-opener] line has no exact opener; write Opener: followed by one space and the statement.` |
| B3 | opener not defined, `g-opener` | `[g-opener] opener <O> is not defined; use a defined opener with the intended meaning.` |
| B4 | defined opener not in allow, `g-allow` | `[g-allow] <O> is not allowed on <MARKER>; use an allowed opener without changing the meaning.` |
| B5 | no statement after opener, `g-empty` | `[g-empty] <O> has no statement; write the value or act after the opener.` |
| B6 | meaning and writer duties | checked by §§5–7 |
| B7 | missing nonempty allowed occurrence of a required opener, `g-required` | `[g-required] <MARKER> requires <O>; add a nonempty <O>: line.` |

For each line, read B2/B3 first. If its opener is known, check B4 and B5 independently; check B6 only
when there is a nonempty statement. A forbidden opener still has a meaning, so B6 can run there.
A missing statement does not count toward B7. F5 only compares defined openers; F6 can run when the
require list and lines are well formed. This limits cascades while retaining independent faults.

## 5. Common openers

Every grammar contains exactly these common opener names, byte for byte and case sensitive:
`Fact`, `Not done`, `Next`, `Correction`, `Say again`. Their `means` values are the exact strings
below, not a publisher's paraphrase. A grammar can add openers but cannot change a common meaning.
A marker need not allow every common opener; the common set belongs to the grammar, not every row.

Only the Owner changes this set by a new edition of hail-grammar, in batches. Earlier editions and
the files pinned to them remain readable. A grammar's own new edition cannot change the common set.

At F3, missing common opener O prints `grammar openers.O: [g-common-missing] common opener O is
missing; add O with its edition-one meaning.` A changed meaning prints `grammar openers.O.means:
[g-common-meaning] common meaning differs; write <exact meaning from the table>.` Unknown child keys
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

The next stage measures complete exchanges, not body length alone.

## 10. Schema and cases

The companion schema checks data shapes. The procedure above also checks relations and meaning.
