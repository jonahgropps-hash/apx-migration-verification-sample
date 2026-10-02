# APX — macro configuration verification sample

A sample deliverable for reviewing how a Confluence macro's existing settings map from Connect to Forge. The review uses an explicit field mapping and reproducible fixture checks to catch dropped settings, changed defaults and ambiguous conversions.

**This example uses original synthetic fixtures. It represents no customer migration or live Forge deployment.**

## A setting can look fine until the first save

Atlassian's [macro migration guide](https://developer.atlassian.com/platform/forge/adopting-forge-from-connect-migrate-macro/) explains that the initial configuration edit exposes legacy parameters; subsequent edits depend on what the Forge configuration saves. A manifest conversion does not establish that these settings retain their intended meaning.

For this example, the legacy contract explicitly maps each field. Ambiguous or unknown values block conversion rather than being guessed.

| Existing meaning | Stored legacy value | Deliberately broken conversion | Corrected fixture output |
| --- | --- | --- | --- |
| Legend disabled | `"false"` | `true` | `false` |
| Limit is zero | `"0"` | `10` | `0` |
| Heading intentionally blank | `""` | `"New heading"` | `""` |
| Label containing a comma | JSON text `["Red, green","東京","😀"]` | Corrupted fragments | Same three strings |
| Hidden revision state | JSON text with revision `7` | Dropped | Explicitly preserved under a reviewed target field |

The JSON array encoding above is part of this synthetic contract; a real vendor's Connect array encoding must be reviewed separately. Literal commas cannot safely be split by a guessed CSV policy.

## Observed checks

- Corrected adapter: 34 of 34 offline fixture contracts pass (7 continuity cases and 27 required blocking cases).
- Deliberately lossy control: 34 of 34 fail, demonstrating that the checks catch changed or missing settings.
- Five CLI, privacy and evidence-integrity checks pass.
- Independent review identified and resolved unsafe JSON numeric conversion and malformed-input diagnostic leakage. Opaque numeric strings are retained; unsupported numeric precision is rejected for vendor review.

Verified runtime: Node 24. Suite SHA-256: `dc040db0497a928b37b815384205ea307d5f05fea115fb64a8acc228504d5930`. Corrected adapter SHA-256: `9077d3e16fa2619b07ae9612bc65e88ed9ff743afe6a1b87305b35581a8f5682`.

## What a real review needs

One agreed macro configuration path, the legacy schema, authorized relevant source and sanitized representative fixtures. The deliverable includes a field disposition table, reproducible checks, findings and a checklist for live verification.

Passing fixtures establish those contracts only. Actual Confluence upgrade, first edit/save, publish/reopen, second edit, rendering/export, permissions and rollback still need an authorized development environment. This example's converter handles legacy string input once; it is not a complete Forge configuration UI. No certification, partnership or platform endorsement is represented.

For a small verification task: **jonahgropps@gmail.com** (Jonah / APX).
