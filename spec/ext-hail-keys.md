# hail/1, third edition: `ext.hail` keys

This is the normative key table cited by [hail/1 §8](hail-1.md#8-who-may-hail-whom). All paths below are relative to
`roster.ext.hail`. It uses sealanes/1 edition one's TOML subset. Only the Owner's counted roster commit changes it.
`<team>` is an exact team key, `<bot>` an exact bot id, and `<channel>` and `<class>` are non-empty opaque names of
ASCII letters, digits, `.`, `_` and `-`. Quote bot keys in TOML. `[]` and `{}` mean empty arrays and tables.

Every row states its scope: team (shared by all teams where marked), team-specific, or bot-specific. Containers grant
nothing. No setting here restricts another namespace. A `sessions` value, signing key or record path stays in the core
roster; no second copy lives here.

Unless a row says otherwise, a missing required field, wrong type, out-of-range value, unknown key or invalid nested
entry makes the hail reader refuse every `hail` act at that head, and nothing outside that namespace. Sealanes/1's Table
1 still decides state; an extension refusal does not make its tree unsound. Other namespace readers pass over hail's
settings after the three shared metadata checks of sealanes/1 §7.2. A resolving hail reader at a pin behind changed hail
restrictions refuses hail acts until the pin moves; a bot reader never resolves.

| Key, including every child key | Scope | Type | Required or default | What refuses it or its use |
|---|---|---|---|---|
| `spec` | team, all | string | required, `"hail/1"` | unknown spec: hail reader; missing or wrong type: every roster reader |
| `edition` | team, all | integer | required, `3` | unknown edition: hail reader; missing or wrong type: every roster reader |
| `extends` | team, all | table | required | every roster reader refuses missing table, wrong shape or a mismatch with the core roster |
| `extends.spec` | team, all | string | required, `"sealanes/1"` | every roster reader refuses another spec or a missing value |
| `extends.edition` | team, all | integer | required, `1` | every roster reader refuses another edition or a missing value |
| `publishers` | decider | map of person ids to exact bot ids | `{}` | hail/1 §8 defines the publisher checks; no grant is created |
| `records` | record | table keyed by team | `{}` | each team's record settings have the single definition in hail/1 §8; undeclared teams with a whole roster are refused |
| `records.<team>` | record | table | defaults in §8 | only `repositories` and `source-hashes` are allowed; types, lookup and carrier rules are defined once in §8 |
| `teams` | team | table keyed by `<team>` | `{}` | hail reader refuses malformed names and, with the whole roster, an undeclared team |
| `teams.<team>` | team | table of the team keys below | omitted means all defaults | hail reader refuses unknown children or a non-table |
| `teams.<team>.signing-required` | team | boolean | `true` | false or a non-boolean is refused; omission means true because a governing roster requires signing (hail/1 §9) |
| `teams.<team>.mode` | team | string, `"shadow"` or `"enforce"` | `"shadow"` | hail reader refuses another value, and refuses deployment `enforce` while §13's six cases are open; isolated conformance fixtures may use enforce |
| `teams.<team>.sealing` | team | table keyed by `<channel>` | `{}` | hail reader refuses bad names or non-table; a sender must identify its actual channel before applying a policy |
| `teams.<team>.sealing.<channel>` | team | string, `"plain"` or `"sealed"` | when absent: plain for git and local socket, sealed for every other channel | hail reader refuses another value; sender refuses or holds a hail if required sealing cannot be done. A per-hail opt-in can seal a plain channel, never unseal a sealed one |
| `teams.<team>.record-classes` | team | table keyed by `<class>` | `{ default = "K1" }` if omitted | hail reader refuses an empty table or one with no `default`, bad class names or bad values; a supplied table replaces the default table whole |
| `teams.<team>.record-classes.<class>` | team | string, `"K1"`, `"K2"` or `"K3"` | required for each declared class | hail reader refuses another value; sender refuses an unknown selected record class, a lower per-hail class, or a minimum with too few eligible oversight keys (§9a) |
| `teams.<team>.private-id-prefixes` | team | array of strings | `[]` | non-array or non-string element: hail reader. A string outside `[A-Za-z]+-` is reported; `hail send` then refuses every public-bound hail from that team until fixed, without blocking private hails on that ground (§11) |
| `teams.<team>.public-destinations` | team | array of repository URLs | `[]` | hail reader refuses a malformed URL, duplicate origin or, with the whole roster, an origin not in any declared place or record; origins compare as sealanes/1 §11.3 says |
| `oversight-keys` | team, all | array of inline tables | `[]` | hail reader refuses malformed entries, unknown children or duplicate recipient bytes. No keys blocks sealing, not plain hails. Every team reads the same Owner recipients |
| `oversight-keys[].recipient` | team, all | string, public age recipient | required | hail reader refuses an invalid encoding, K1 or R not X25519, or K2/K3 not `age1tag` or `age1tagpq`; no private material is allowed |
| `oversight-keys[].class` | team, all | string, `"K1"`, `"K2"`, `"K3"` or `"R"` | required | hail reader refuses another value; R is a reading recipient, never a minimum class or signing role |
| `oversight-keys[].survives-loss` | team, all | boolean | required | hail reader refuses another type; a sealing minimum needs two eligible keys, including one declared to survive loss of the main machine; R must be true. Hardware custody remains a declaration, not proof |
| `bots` | bot | table keyed by `<bot>` | `{}` | hail reader refuses malformed bot ids and, with the whole roster, an undeclared bot |
| `bots.<bot>` | bot | table of the interval keys below | omitted means all defaults | hail reader refuses unknown children or a non-table |
| `bots.<bot>.watcher-interval` | bot | integer seconds, at least 0 | `0` | hail reader refuses negative or non-integer; used for the git first-response window, plus 900 seconds; for FORCED END the filer uses the value at STOP filing and the two filing commit times (§10) |
| `bots.<bot>.wake-interval` | bot | integer seconds, at least 0 | `0` | hail reader refuses negative or non-integer; used for a hosted bot message, plus 900 seconds; declare 0 only where the message wakes it or no interval is declared |
| `bots.<bot>.notice-interval` | bot | integer seconds, at least 0 | `0` | hail reader refuses negative or non-integer; used for the relay's hosted `shown` window, plus 900 seconds |

Intervals name the longest gap, never a mean; an omitted interval's zero is a timing default, not a promise that a bot
is awake. The sender's explicit inputs name the channel, its transport and host category, the real destination, and the
record class (default `default`). The checker does not infer them from prose. A declared channel policy applies on that
channel; when absent, the actual transport and host decide the default above. Local configuration binds channel names to
transports; it changes no grant. A required policy at either end of a cross-team route is kept; the stronger class and
sealing requirement wins. A roster requires signing (§9).

For a minimum K1, K2 or K3, eligible keys are every oversight key of that class or better, plus R. There must be at
least one eligible non-R key and at least two keys in total, with a survivor. Each sealed hail names all of them in
`sealed-to`, plus every intended reader's public recipient. The Owner confirms K1 is used only where no chip exists and
that the stated survival and chip custody are true. A checker can check the declarations, not the hardware. `seen-by`
gives no recipient key and substitutes for none of these checks.

Sealing a decider's hail is open (§13): a decider's hail is not sealed, and a `seen-by` overseer reads it whole. No
extension or core key is added for a decider or an overseer.

FORCED END uses the watcher interval at STOP filing and the event, response, retry and replay rules
in hail/1 §10.

The public-text test uses the sending bot's team settings and the actual destination repository's origin, not the
repository a source happens to name. If any actual destination is public under that policy, the public scan
applies. No prefixes means no private-id refusal; it relaxes no rule on secrets. A person's hail is signed regardless of
these flags; when a tool sends it through a team's record it applies that team's content and sealing policy as well.

A lock carries `ext` whole, even when its slice lacks a named team's or bot's core section. A slice-holding reader
validates the extension's keys, types and name forms, but does not call a name undeclared merely because the slice omits
it; it applies settings only to subjects it can read. Whole-roster validation checks references. Nothing here allows
checking or acting on a cross-team hail with only a slice.
