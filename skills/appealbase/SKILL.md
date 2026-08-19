---
name: appealbase
description: >
  Use this skill whenever the user asks about UK planning appeals, appeal decisions, appeal
  outcomes, planning inspectors, PINS, the Planning Inspectorate, refused planning permission,
  dismissed or allowed appeals, Green Belt appeals, inspector reasoning, main issues, appeal
  precedent, comparable cases, LPA appeal records, householder appeals, enforcement appeals,
  listed building appeals, written representations, hearings, or public inquiries. Also activate
  for questions about how to research planning policy application, officer recommendation
  reversals, costs awards, or any search of the Appealbase database.
---

# Appealbase — UK Planning Appeals

Appealbase indexes over 100,000 planning appeal decisions issued by the Planning Inspectorate (PINS) for England. Each record contains the full inspector's decision letter plus structured metadata extracted from PINS case files.

## The appeal system

A planning appeal arises when an applicant disagrees with a Local Planning Authority (LPA) decision — usually a refusal, but sometimes conditions, non-determination, or an enforcement notice. Appeals are decided by an inspector appointed by PINS, independently of the LPA.

**Three procedures** determine how an appeal is heard:

- **Written Representations** — the most common procedure. Evidence is exchanged in writing; there is no hearing. Decisions tend to be shorter and faster.
- **Hearing** — an informal session where the inspector leads a round-table discussion. Used for more complex cases where some oral exchange is needed but a full inquiry is disproportionate.
- **Inquiry** — a formal, quasi-judicial proceeding with evidence and cross-examination, used for major or contentious appeals. Decisions are typically longer and contain more detailed policy analysis.

The procedure matters when reading a decision: inquiry decisions carry more developed reasoning and are more reliably citable as precedent. Written representations decisions are more numerous and better for pattern analysis.

## Reading a decision outcome

The `decision` field records the inspector's formal outcome:

- **Allowed** — the appeal succeeds; planning permission is granted (or the enforcement notice is quashed).
- **Dismissed** — the appeal fails; the LPA's original decision stands.
- **Split decision** — in appeals involving multiple grounds (common in enforcement cases), some grounds may be allowed and others dismissed. The `decision` field records the overall outcome; the full text contains the split.

A decision can also be **withdrawn** before it is issued (the appellant abandons the appeal) or **invalid** if procedural requirements were not met.

**Costs** are a separate matter. Either party can apply for a costs award if the other has behaved unreasonably. The `costs_applied_for` flag records whether a costs application was made; the `costs_text` field contains the costs decision if one was issued. An appeal can be dismissed but costs awarded against the LPA if it defended unreasonably.

**Recovered appeals** are cases called in by the Secretary of State for their own determination. The inspector holds the inquiry or hearing and writes a report with a recommendation, but the Secretary of State issues the final decision. The `recovered_date` field records when recovery happened.

## Main issues

The "Main Issues" section is the most analytically useful part of a decision letter. The inspector identifies, usually near the start, the specific questions they will determine — for example: "the main issue is whether the proposed development would preserve or enhance the character and appearance of the conservation area." Everything that follows in the decision is the inspector's answer to those questions.

Searching `main_issues_only: true` scopes the full-text search to the extracted Main Issues section rather than the full decision letter. This is the right approach when the user wants cases where a topic was the central question, not merely mentioned in passing. It returns fewer results but much higher signal.

`main_issues_text` is extracted by deterministic pattern-matching (no AI). It is `null` in a minority of older or shorter decisions that omit a formal Main Issues section. When `main_issues_only` is set, those decisions are excluded from results.

## Filters and when to use them

| Filter | When to use |
|---|---|
| `lpa` | Researching a specific authority's appeal record; checking how one LPA's decisions compare to others |
| `inspector` | Reviewing a specific inspector's body of decisions; checking an inspector's track record on a policy |
| `decision` | Allowed/Dismissed split analysis; finding comparable allowed cases to support an appeal |
| `procedure` | Limiting to inquiry decisions for higher-quality precedent; written representations for volume analysis |
| `development_type` | Limiting to a development category (e.g. Major dwellings, Householder developments, Renewable energy) |
| `appeal_type` | Planning, Householder, Enforcement, Commercial, Lawful Development, Listed Building |
| `date_from` / `date_to` | Restricting to decisions made under a specific version of national or local policy |
| `date_preset` | Relative ranges (last_month, last_year, etc.) for recent decisions |
| `site_green_belt` | Boolean flag — use in combination with keyword search, not as a standalone filter |

Inspector names are stored in PINS format: surname then initials, e.g. `Hartley D` or `Leigh CJ`. Partial matching works: `Leigh C` matches `Leigh CJ`.

## Common research patterns

**Comparable-site research (supporting an appeal)**
Search for allowed decisions on similar sites. Combine a keyword query with `decision: "Allowed"`, `lpa` (same or comparable authority), `development_type`, and a date range that captures current policy. Use `main_issues_only: true` if the comparison is about a specific policy test.

**Policy application analysis**
Search for the policy reference or policy phrase as a keyword (e.g. "paragraph 11d", "very special circumstances", "less than substantial harm"). Filter by `development_type` or `appeal_type` to narrow. Use `main_issues_only: true` to find cases where the policy was the central question.

**Officer recommendation overturn**
There is no direct filter for this. Search decision text for phrases like "officer recommendation" combined with `decision: "Allowed"` and the LPA in question. The pattern "contrary to the officer's recommendation" appears in some decision letters but not consistently.

**Inspector track record**
Filter by `inspector` (PINS name format) and optionally `decision`. Use `date_from` to limit to a recent period. Combine with a keyword query to find their decisions on a specific topic.

**Checking how a policy has been applied**
Quote the policy phrase in the `query` field with `exact_match: true` (the default). This finds decisions where the inspector quoted or applied that specific wording. Set `main_issues_only: true` to restrict to cases where it was a main issue.

**Costs patterns**
Filter `costs_applied_for: true` is not available as a direct filter. Search for "unreasonable behaviour" or "costs award" in the full text, or use `get_appeal_full_text` on specific cases to read the `costs_text` field.

## What the corpus contains and does not contain

**Contains:**
- Planning appeal decisions for England only (not Scotland, Wales, or Northern Ireland, which have separate systems)
- All appeal types: Planning, Householder, Enforcement, Commercial, Lawful Development, Listed Building
- Decisions back to the 1970s for older cases; comprehensive from the 1990s onwards
- Full inspector decision letters including the Main Issues section and reasoning
- Structured metadata: LPA, inspector, procedure, development type, site designation flags, dates

**Does not contain:**
- Called-in applications decided by the Secretary of State without a PINS inspector (some large or contentious applications go directly to the Secretary of State)
- Local plan examinations or development consent orders (nationally significant infrastructure projects)
- Planning appeals in Scotland (handled by DPEA), Wales (PEDW), or Northern Ireland (PAC)
- The planning application itself, the LPA's decision notice, or any representations made during the appeal — only the inspector's decision letter

## Citation and reuse

Decision letter text is Crown Copyright, published by PINS under the Open Government Licence v3.0. Enrichment fields (main_issues_text and computed fields) are subject to the Appealbase commercial licence.

When presenting results, cite each decision by its Appealbase URL (included in every result as `url`). Verbatim quotes of decision text should note they are PINS material under OGL v3.0. Summaries and paraphrases do not require a PINS attribution. Do not systematically extract or republish substantial portions of the database.
