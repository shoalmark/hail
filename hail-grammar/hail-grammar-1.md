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

The common set and its meaning rules are specified here, not chosen by the file's publisher.

## 6. Meanings in a grammar

Meaning review is part of accepting a grammar, alongside its shape and references.

## 7. Writer duties

Writer duties apply to every grammar. A structural pass alone is not a meaning review.

## 8. Starting files and later work

Starting files and the questions left to use are specified below.

## 9. Measurement

The next stage measures complete exchanges, not body length alone.

## 10. Schema and cases

The companion schema checks data shapes. The procedure above also checks relations and meaning.
