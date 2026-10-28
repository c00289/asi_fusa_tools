# ASI FuSa Tools — Plan: the family document becomes its own micro repo

**Status:** Implemented 2026-10-27
**Written:** 2026-10-27
**Owner:** FuSa engineer
**Target version:** none (a document repo; dated revisions, no semver). Sibling edits ride with each
sibling's next release and bump nothing on their own.
**Family context:** [ASI_FUSA_TOOLS.md](../ASI_FUSA_TOOLS.md), the file this plan moved and renamed
(it was the loose `ASI_FAMILY.md`; section 2 describes that state).
**Sequencing:** independent of the tool versions; the owner slots it anywhere after the 0.3.0 theme
work and before the 0.7.0 automation server. Every plan written on 2026-10-27 links to the old path
with a note, and phase 3 below sweeps whichever of them exist at execution time.

---

## 1. The ask

Decided by the owner on 2026-10-27:

1. **Update the family document** as already noted: `asi_hopper` is no longer an empty reserved
   folder but a real 0.1.0 repo (a read-only Jira door), the plans table is missing every plan
   written today, and the version column is stale.
2. **Rename it, file and content, to "ASI FuSa Tools"**, because "family" says nothing about what
   the tools are for. The collection is the functional-safety tooling for the Wheel Loader
   programme, and its charter should say so in its name.
3. **Give it its own micro repo.** Today the file sits loose in the OneDrive folder, outside any git
   repository, so the one document that every repo's tests and plans treat as the authority has no
   history and no diff. It becomes `asi_fusa_tools`, a sibling folder like the others.

## 2. What exists today (verified 2026-10-27)

| Fact | Where |
|---|---|
| `ASI_FAMILY.md` is a loose file in `C:\Users\rgomezme\OneDrive - Capgemini`, which is not a git repository. Eight `##` sections: The ontology, House rules every repo follows, One primary tool at a time (the family lock), The family palette, How a sibling consumes a generator, Where the integration plans live, Layout on disk, plus the title table of five repos. | `ASI_FAMILY.md` |
| The title table still says asi_hopper is "Empty folder today … none yet"; the layout tree says `asi_hopper/ <- reserved, empty`. asi_hopper is in fact a committed 0.1.0 repo (`b172cfc`): read-only Jira through Atlassian's Remote MCP Server, OAuth 2.1, replay fixtures, `whoami/projects/get/search/snapshot/diff/verify…` verbs. | `ASI_FAMILY.md` lines 13 and 238; `asi_hopper/README.md`, `asi_hopper/src/asi_hopper/version.py` |
| The version column says dewey 0.2.0, kletz 0.2.0. Both working trees are at 0.3.0 (theme palette, uncommitted). escher 0.1.0 and gutenberg 0.2.0 are current. | `*/src/*/version.py` |
| The plans table lists five plans. Missing: both theme-palette plans (done), `asi_hopper/docs/integration-plan.md`, `asi_dewey/docs/onboarding-ingest-plan.md`, and the six plans written on 2026-10-27 (prune-windows, all-columns, two jira-live-data, two automation-server) plus this one. | `ASI_FAMILY.md` "Where the integration plans live" |
| Both primary tools' test suites read the file by path: `FAMILY_FILE = Path(__file__).resolve().parents[2] / "ASI_FAMILY.md"` and assert the palette digest literal appears in it when the file exists. | `asi_dewey/tests/test_theme.py:26`, `asi_kletz/tests/test_theme.py:20` |
| `gui/palettes.py` is byte-identical in dewey and kletz and its docstring names `ASI_FAMILY.md` as the authority. The catalog digest covers the colour data only, not the docstring. | `asi_*/src/asi_*/gui/palettes.py:4` |
| 36 further references to the file name or path, all documentation: READMEs (dewey, kletz, gutenberg), CHANGELOGs (dewey, kletz: historical entries), `docs/plan.md` (dewey, kletz), every `docs/*-plan.md` in dewey, kletz and gutenberg (`[ASI_FAMILY.md](../../ASI_FAMILY.md)` in the header block and in the body). | `grep -rn ASI_FAMILY` over the five repos |
| The phrases "the family lock" and "the family palette" are the names of two conventions. They appear in section headings, plan titles, CHANGELOG entries, test names, the `palettes.py` docstring and the lock plan's code names (`ASI_PRIMARY_LOCK_DIR`, `asi\locks`). | same grep, 100+ hits |
| The Desktop `.code-workspace` files do not reference the family file. | `Desktop/asi_*.code-workspace` |
| House rules require every repo to have `uv`, `ruff`, `pytest`, a `CHANGELOG.md` and tags. Those rules are written for code repos. | `ASI_FAMILY.md` "House rules every repo follows" |

## 3. Design

### 3.1 The micro repo

```
asi_fusa_tools/
├── ASI_FUSA_TOOLS.md      the document (moved from ../ASI_FAMILY.md, retitled, updated)
├── README.md              five lines: what this repo is, link to the document, how to propose a change
├── CHANGELOG.md           Keep a Changelog form, dated entries, no version numbers
├── .gitignore             the OneDrive and editor noise the siblings ignore
└── docs/
    └── fusa-tools-plan.md this plan
```

No `pyproject.toml`, no `uv`, no tests: there is nothing to run. The house rules gain one line
saying that a documentation-only repo carries README, CHANGELOG and git history and nothing else.
`git init`, one initial commit "ASI FuSa Tools: the family document moves into its own repo",
tag left to the owner as usual. The repo is a sibling folder, so every relative link from a
sibling's `docs/` is `../../asi_fusa_tools/ASI_FUSA_TOOLS.md` and from a sibling's root
`../asi_fusa_tools/ASI_FUSA_TOOLS.md`.

The loose `ASI_FAMILY.md` is deleted, not stubbed: two copies of the authority is the failure this
plan exists to prevent, and every link is rewritten in phase 3.

### 3.2 The content

The document keeps its structure and gains what is missing. In order:

1. **Title** `# ASI FuSa Tools`. First paragraph: "Six sibling repositories in one folder, one naming
   scheme, one job: the functional-safety tooling for the Wheel Loader programme. Every tool repo is
   called `asi_<surname>` after a person whose life's work is the job the repo does; this repo,
   `asi_fusa_tools`, is the charter they all follow." The collective noun throughout becomes
   "ASI FuSa Tools" (or "the tools") where the text today says "the family" or "the ASI family".
2. **The title table** gains the hopper row as a real entry and this repo as the last row:

   | Repo | Named after | Because | Role | Package / command | Version |
   |---|---|---|---|---|---|
   | `asi_hopper` | Grace Hopper (1906 to 1992), rear admiral, compiler pioneer | she built the first compiler so people could instruct a machine in their own words | **Gateway.** Read-only, audited, replayable door into Jira: a library, a CLI, and a fixture-replay gateway so siblings and tests never touch the network. OAuth 2.1 through Atlassian's Remote MCP Server, no API tokens, no writes. | `asi_hopper` / `asi-hopper` | 0.1.0 |
   | `asi_fusa_tools` | nobody; it is the charter, not a tool | | **Charter.** This document, the conventions every repo tests against, and the plans index. No code. | none | dated |

   Version cells are read from each `version.py` at execution time, never typed from memory.
3. **The ontology** gains a third tier. "Primary tools" and "Generators" stay as written; add
   **Gateways**: "libraries with a session and an evidence folder but no database of their own; they
   read an external system and hand plain records to the primary tools. Primary tools must be able
   to use every gateway. A gateway consumes nothing from the family." The Mermaid and ASCII diagrams
   gain `hopper -->|issues| dewey` and `hopper -->|issues| kletz`, with hopper drawn beside the
   generators, below the primary tools.
4. **House rules** gain the documentation-repo line from 3.1 and one more: "Conventions keep their
   names once tests refer to them: *the family lock* and *the family palette* stay so named."
5. **The family lock** and **The family palette** sections: unchanged except the collective noun.
6. **A new section "The JIRA Live Data window"** is added by the jira-live-data plans' phase 4, not
   by this plan; this plan leaves a placeholder sentence under "Where the integration plans live"
   so the two do not collide.
7. **New section "Version ladder"**, the table decided on 2026-10-27, placed before "Where the
   integration plans live":

   | Version | asi_dewey | asi_kletz | Plan |
   |---|---|---|---|
   | 0.3.0 | theme palette | theme palette | `docs/theme-palette-plan.md` |
   | 0.4.0 | prune windows | all 63 columns on the Safety Plan screen | `docs/prune-windows-plan.md` / `docs/all-columns-plan.md` |
   | 0.5.0 | JIRA Live Data window | JIRA Live Data window | `docs/jira-live-data-plan.md` |
   | 0.6.0 | the family lock | the family lock | `docs/single-instance-plan.md` |
   | 0.7.0 | automation server, theme badge | automation server, theme badge | `docs/automation-server-plan.md` |

   with the sentence: "The siblings stay in step: the same version number means the same feature
   set relative to the charter. Each plan's last phase updates this table and the title table."
8. **Where the integration plans live** is rewritten as the full index, one row per plan in every
   repo, with a Status column (Implemented x.y.z / Decided / Proposed / Parked), including
   `asi_hopper/docs/integration-plan.md`, `asi_hopper/docs/proposal.md` (write side: parked by
   owner decision), `asi_dewey/docs/onboarding-ingest-plan.md`, and this plan.
9. **Layout on disk** tree: `asi_hopper/` described as the gateway, `asi_fusa_tools/` added,
   `ASI_FAMILY.md <- this file` removed; the sentence about the OneDrive folder not being a repo
   stays, now pointing out that the charter is the one repo with no code.
10. A trailing line `*Revision 2026-10-27: moved from ASI_FAMILY.md, retitled, hopper and the
    version ladder added.*` The CHANGELOG carries the same entry.

### 3.3 The sweep of the siblings

Every reference found in section 2 is rewritten, grouped by repo so each becomes one commit for
the owner:

| Repo | Files | Change |
|---|---|---|
| asi_dewey | `tests/test_theme.py` | `FAMILY_FILE = … / "asi_fusa_tools" / "ASI_FUSA_TOOLS.md"`; the skip reason names the new path |
| asi_dewey | `src/asi_dewey/gui/palettes.py` | docstring line 4: the authority is the section "The family palette" in `ASI_FUSA_TOOLS.md` |
| asi_dewey | `README.md`, `docs/plan.md`, `docs/*-plan.md` | links and names to the new path; header blocks keep their wording |
| asi_dewey | `CHANGELOG.md` | historical entries left verbatim; one line under `[Unreleased] > Changed`: "The family document is now `asi_fusa_tools/ASI_FUSA_TOOLS.md`." |
| asi_kletz | the same five groups | the same changes; `palettes.py` must end byte-identical to dewey's (same edit, LF kept); the share-nothing test still finds no `asi_dewey` |
| asi_gutenberg | `README.md`, `docs/escher-integration-plan.md` | links only |
| asi_hopper | `docs/integration-plan.md` | gains a "Family context" line pointing at the charter (it has none today) |
| asi_escher | nothing | it never referenced the file |

The two convention names are not renamed (rule 4 in 3.2). No `version.py` or `pyproject.toml`
changes anywhere: these are documentation and test-path edits that ship with each repo's next
release (dewey and kletz 0.4.0 at the earliest; gutenberg whenever it next releases).

### 3.4 Order inside a single working session

The move must be atomic from the tests' point of view: between deleting `ASI_FAMILY.md` and
editing the two `test_theme.py` files, the digest sub-assertion is simply skipped (the test guards
on the file existing), so the suites never go red. The order is: create the repo and the new file
(phase 1), edit the content (phase 2), sweep the siblings and run both suites (phase 3), delete
the old file last and run both suites again (phase 3, last step).

## 4. Delivery phases

| Phase | Content | Done when |
|---|---|---|
| 1. Repo | `asi_fusa_tools/` with `.gitignore`, `README.md`, `CHANGELOG.md`, `docs/fusa-tools-plan.md`; `git init`; `ASI_FAMILY.md` copied in as `ASI_FUSA_TOOLS.md` unchanged; first commit. | `git log` shows one commit; `diff ../ASI_FAMILY.md ASI_FUSA_TOOLS.md` is empty. |
| 2. Content | Section 3.2 items 1 to 10. Versions read from each `version.py`. Mermaid block still renders (check with the ASCII twin in sync). | The palette digest literal and the lock convention table are byte-for-byte as before (`grep` both); the title table has seven rows (six repos + header); the plans index lists every `docs/*plan*.md` found by `find`. |
| 3. Sweep | Section 3.3 across dewey, kletz, gutenberg, hopper; `uv run pytest` in dewey and kletz; `diff` of the two `palettes.py`; then delete `../ASI_FAMILY.md`; both suites again; `grep -rn ASI_FAMILY` over the folder returns only historical CHANGELOG lines and this plan's section 2. | Both suites green twice; the digest sub-assertion ran (not skipped) on the second run; palettes.py identical. |
| 4. Hand-over | CHANGELOG entry in the micro repo; a line in each sibling's `[Unreleased]`; commits left to the owner, one per repo. | The owner can `git diff` each of the five repos and see only the sweep. |

## 5. Files touched

```
asi_fusa_tools/ASI_FUSA_TOOLS.md         new: the document (moved, retitled, updated)
asi_fusa_tools/README.md                 new
asi_fusa_tools/CHANGELOG.md              new
asi_fusa_tools/.gitignore                new
asi_fusa_tools/docs/fusa-tools-plan.md   this plan
ASI_FAMILY.md                            deleted (last step)
asi_dewey/tests/test_theme.py            FAMILY_FILE path
asi_dewey/src/asi_dewey/gui/palettes.py  docstring (byte-identical with kletz afterwards)
asi_dewey/README.md, CHANGELOG.md, docs/plan.md, docs/*-plan.md   links, names, one Unreleased line
asi_kletz/  the same five groups
asi_gutenberg/README.md, docs/escher-integration-plan.md          links
asi_hopper/docs/integration-plan.md      Family context line
```

Not touched: any `version.py`, `pyproject.toml`, `uv.lock`, schema, storage, GUI code, the two
theme catalogs' data, the lock plans' convention text, the Desktop workspace files.

## 6. Tests

There is no test suite in the micro repo. The guards live in the siblings and already exist:

- `asi_dewey/tests/test_theme.py` and `asi_kletz/tests/test_theme.py`: the digest literal is found
  in `asi_fusa_tools/ASI_FUSA_TOOLS.md`; after phase 3 the file exists, so the sub-assertion runs
  rather than skips. Add one assertion to each: the file's first line is `# ASI FuSa Tools`, so a
  stale copy under the old name cannot satisfy the test by accident.
- `asi_kletz/tests/test_generate.py` share-nothing test: still no `asi_dewey` in kletz.
- Manual: `grep -rn "ASI_FAMILY" .` from the OneDrive folder lists only historical CHANGELOG
  entries and section 2 of this plan.

## 7. Rejected alternatives

- **Leave a stub `ASI_FAMILY.md` that points at the new file.** Two files claiming authority is the
  problem being solved; links are cheap to rewrite and section 2 lists every one.
- **Rename the conventions too ("the FuSa Tools lock", "the FuSa Tools palette").** Over a hundred
  hits across tests, CHANGELOG history, plan titles and a byte-identical module; the names are proper
  nouns now and renaming them would change released CHANGELOG text. Rule added instead.
- **Make `README.md` the document.** The owner asked for the file to carry the name; a README that
  is 240 lines long is also a poor landing page. The README points at the document.
- **Give the repo `uv`, `ruff` and a test that checks the version table against each `version.py`.**
  Tempting, but it would make the charter depend on every sibling's layout, and the siblings already
  test the one fact that matters (the digest). Open item below if drift becomes a nuisance.
- **Keep the file loose but rename it.** No history, no diff, no blame for the one file every test
  trusts; the whole point of the ask.

## 8. Open items

- Whether the version ladder table should be generated from the siblings' `version.py` by a
  ten-line script in `asi_fusa_tools/tools/` rather than edited by hand. Decide after two or three
  releases show how often it drifts.
- Whether `asi_hopper` should get a "Family context" header on its README like the others. Cheap;
  do it in the sweep if the owner says yes.
- A Desktop `asi_fusa_tools.code-workspace` alongside the existing dewey and kletz ones. Owner's
  habit, not a plan item.

*Written 2026-10-27.*
