# ASI FuSa Tools

Six sibling repositories in one folder, one naming scheme, one job: the functional-safety tooling
for the Wheel Loader programme. Every tool repo is called `asi_<surname>` after a person whose
life's work is the job the repo does; this repo, `asi_fusa_tools`, is the charter they all follow.
The name tells you the role before you open the folder.

| Repo | Named after | Because | Role | Package / command | Version |
|---|---|---|---|---|---|
| `asi_dewey` | **Melvil Dewey** (1851 to 1931), librarian, inventor of the Dewey Decimal Classification | one classification scheme, many views | **Primary tool.** Functional-safety working-knowledge store for the Wheel Loader programme: tasks, facts, machine functions, use cases, links, Jira tickets (cached from Jira through asi_hopper), QMS documents, and people; the safety plan rows it still carries are a cached copy edited in asi_kletz, and since 0.4.0 it shows no Jira or Safety Plan windows of its own. SQLite is the truth; Excel and JSON are generated views; a CLI and a tkinter window drive the same command path. | `asi_dewey` / `asi-dewey`, `asi-dewey-gui` | 0.8.1 |
| `asi_kletz` | **Trevor Kletz** (1922 to 2013), chemical engineer, father of inherently safer design and HAZOP | taught industry to ask of every line of a plan what could go wrong | **Primary tool.** The platform **Safety Plan** and nothing else: one row per deliverable with its lifecycle phase, location, QMS references, author, verifier, reviewers, deadline, and status. Same architecture as Dewey, split from it on 2026-10-27, shares nothing with it at runtime. | `asi_kletz` / `asi-kletz`, `asi-kletz-gui` | 0.11.0 |
| `asi_escher` | **M. C. Escher** (1898 to 1972), graphic artist of impossible geometry | he drew structure | **Generator.** Pure-Python diagram engine: activity, flowchart, state machine, and SysML BDD / IBD / REQ diagrams, laid out by a deterministic layered algorithm and rendered to PNG, SVG, Graphviz DOT, and Mermaid. No Graphviz, Node, or browser needed. | `asi_escher` / `asi-escher` | 0.1.1 |
| `asi_gutenberg` | **Johannes Gutenberg** (c. 1400 to 1468), inventor of the movable-type printing press | he made documents reproducible | **Generator.** Pure-Python document engine: specs, reports, plans, procedures, minutes, memos, safety cases, ICDs, analyses, checklists, and release notes, written as Markdown (the source of truth) and rendered from that Markdown to PDF and Word as a triplet that can never disagree. | `asi_gutenberg` (library only) | 0.2.1 |
| `asi_hopper` | **Grace Hopper** (1906 to 1992), rear admiral, compiler pioneer | she built the first compiler so people could instruct a machine in their own words | **Gateway.** Read-only, audited, replayable door into Jira for the Wheel Loader programme: a library, a CLI, and a fixture-replay gateway so siblings and tests never touch the network. OAuth 2.1 through Atlassian's Remote MCP Server, no API tokens, no writes. Feeds live issues to both primary tools. | `asi_hopper` / `asi-hopper` | 0.1.4 |
| `asi_fusa_tools` | nobody; it is the charter, not a tool | | **Charter.** This document, the conventions every repo tests against, and the plans index. No code. | none | dated |

## The ontology

Three tiers. **Primary tools** are what a human or an agent opens: each owns a database, a command
vocabulary, a CLI, and a desktop window. **Generators** are libraries: no database, no window, no
state; they take a plain-data model and produce files. **Gateways** are libraries with a session
and an evidence folder but no database of their own: they read an external system and hand plain
records up to the primary tools.

The rule that ties them together: **primary tools must be able to use every generator and every
gateway**, and the document generator must be able to use the diagram generator so that a document
can carry its own figures.

One diagram per primary tool. The generators and the gateway under each are the same; the tools
never appear in one diagram because they never depend on each other.

### asi_dewey

```mermaid
flowchart BT
  dewey["asi_dewey\nworking knowledge"]
  gutenberg["asi_gutenberg\ndocuments (md, pdf, docx)"]
  escher["asi_escher\ndiagrams (png, svg, dot, mmd)"]
  hopper["asi_hopper\nJira issues (read only)"]
  gutenberg -->|documents| dewey
  escher -->|diagrams| dewey
  hopper -->|issues| dewey
  escher -->|figures| gutenberg
```

For readers without Mermaid:

```
                 +-----------+
                 | asi_dewey |                        primary tool: working knowledge
                 +-+---+---+-+
                   ^   ^   ^
      documents   /    |    \   issues
                 /     |     \
   +-------------+-+   |   +-+--------------+
   | asi_gutenberg |   |   | asi_hopper    |      generator: documents / gateway: Jira, read only
   +------+--------+   |   +----------------+
          ^  figures   |  diagrams
          |            |
       +--+------------+--+
       |    asi_escher    |                        generator: diagrams (needs nothing)
       +------------------+
```

### asi_kletz

```mermaid
flowchart BT
  kletz["asi_kletz\nsafety plan"]
  gutenberg["asi_gutenberg\ndocuments (md, pdf, docx)"]
  escher["asi_escher\ndiagrams (png, svg, dot, mmd)"]
  hopper["asi_hopper\nJira issues (read only)"]
  gutenberg -->|documents| kletz
  escher -->|diagrams| kletz
  hopper -->|issues| kletz
  escher -->|figures| gutenberg
```

For readers without Mermaid:

```
                 +-----------+
                 | asi_kletz |                        primary tool: safety plan
                 +-+---+---+-+
                   ^   ^   ^
      documents   /    |    \   issues
                 /     |     \
   +-------------+-+   |   +-+--------------+
   | asi_gutenberg |   |   | asi_hopper    |      generator: documents / gateway: Jira, read only
   +------+--------+   |   +----------------+
          ^  figures   |  diagrams
          |            |
       +--+------------+--+
       |    asi_escher    |                        generator: diagrams (needs nothing)
       +------------------+
```

Arrows point from the generator or gateway to what it serves: Escher feeds diagrams up to Gutenberg
and to the tool, Gutenberg feeds documents up to the tool, and Hopper feeds Jira issues up to the
tool. Nothing flows back down.

Edges, spelled out:

| Consumer | Uses | For |
|---|---|---|
| `asi_dewey` | `asi_escher` | diagrams of the lifecycle, the plan, tasks, and the schema itself |
| `asi_dewey` | `asi_gutenberg` | the safety plan document, status reports, the working-knowledge handbook |
| `asi_kletz` | `asi_escher` | diagrams of the lifecycle, each phase's flow, the status board |
| `asi_kletz` | `asi_gutenberg` | the Safety Plan document, status reports, phase checklists |
| `asi_dewey` | `asi_hopper` | live Jira issues: the JIRA Live Data window, `jira sync` into the ticket cache |
| `asi_kletz` | `asi_hopper` | live Jira issues: the JIRA Live Data window; Jira links on deliverables later |
| `asi_gutenberg` | `asi_escher` | a `diagram` block that renders to a figure inside the document |
| `asi_escher` | nothing | the bottom of the stack |
| `asi_hopper` | nothing from the tools | a gateway: it reads Jira and knows nothing about the tools |

Non-edges matter as much:

- **Dewey and Kletz never import each other.** They share an architecture by copying, not a
  package. No shared database, workbook, or config file. That is a deliberate property, checked by
  Kletz's test suite. The one thing they share is a **convention**, not code: the family lock
  below, including its single environment variable.
- **Generators never import a primary tool.** Escher and Gutenberg know nothing about SQLite,
  commands, or windows. A primary tool builds the plain-data model and hands it over.
- **Gateways never import a primary tool, and never write.** Hopper reads Jira and hands plain
  records up; each tool decides what to keep in its own database, and Jira stays the truth for
  tickets the way the database stays the truth for everything else.
- **Generated files are views, never truth.** Exactly like the Excel workbook: a diagram or a
  document is regenerated from the database on demand and is never read back into it.

## House rules every repo follows

- Python 3.13, [uv](https://docs.astral.sh/uv/) with `uv_build`, `src/` layout, `ruff`, `pytest`.
- `tool.uv.link-mode = "copy"` in every `pyproject.toml`, because OneDrive refuses the hardlinks
  uv makes from its cache.
- Semantic versioning, a `CHANGELOG.md` in Keep a Changelog form, a `v<x.y.z>` tag per release.
- Factories and registries over edits to core code: a new entity, verb, exporter, screen, diagram,
  or document is added by registration.
- Everything a window can do, a command can do. The window is a client of the command path.
- No person is named inside Dewey or Kletz; the tools are for a role, not for someone.
- A documentation-only repo (`asi_fusa_tools`) carries a `README.md`, a `CHANGELOG.md` with dated
  entries, and git history, and nothing else: no `pyproject.toml`, no `uv`, no tests.
- Conventions keep their names once tests refer to them: *the family lock* and *the family
  palette* stay so named.

## Identity: `id` and `uid`

Decided 2026-10-28, implemented in asi_dewey 0.8.0 and asi_kletz 0.9.0 (the two
`docs/hidden-identity-plan.md`). Every row of every entity in both tools has two identities:

- **`id`**, the integer primary key: the handle. It is what a command takes (`update task 2 ...`),
  the row iid of every tree, the audit log's `entity_id`, the status bar's "Task 2 selected" and
  the details frame's title. It is typed by people and agents all day, so it stays short. It is
  stable inside one database only and is **shown in no table**.
- **`uid`**, a canonical lowercase UUID4 (36 characters): the trace. Assigned once on creation,
  never changed (`update` refuses it: `uid is immutable`), unique per table, hidden from every table
  and form (`FieldSpec.hidden`, `EntitySchema.visible_fields`), carried by every file the tool
  writes (the last key of a JSON row; the last column of every workbook sheet, headed `uid` and
  hidden in the sheet) and honoured by every import: a row whose uid is known is an update of that
  row whatever its id in the file; otherwise an id that is known is an update by id in merge mode,
  and the stored uid takes the file's uid when they differ (the one deliberate identity
  reconciliation, which is how Dewey's cached plan rows take Kletz's); otherwise an insert that keeps
  the file's uid. A workbook sheet carries no `id`, so a workbook is reconciled with `--mode replace`
  (the sheet's uids win), never merged by id. A database from before the rule gains the column, the index and a uid on every
  row the first time it is opened (`stamp_uids`, audited per table that had rows).
- `get`, `update` and `delete` accept a uid where an id is expected; `list --filter uid=...` works
  as for any field. A document or a sibling that must point at a row points at its uid.
- **No table shows `id` or `order`.** The order of the elements is kept by each entity's `order`
  field (editable in the form) and `order_by`, and by the stack on the two Safety Plan screens.

## Sprints (asi_kletz)

Decided by the owner 2026-10-28, for the sprint planner, the deadline calculation and the point
estimates:

- **A sprint spans 10 business days.** Monday to Friday count; weekends and holidays do not, so a
  sprint that contains a holiday ends one calendar day later per holiday. Dates move as holidays
  become real; the cadence does not.
- **Capacity is 8 points per sprint, company policy** (8 productive days of the 10). Holidays never
  reduce points. Since asi_kletz 0.10.0 this is a fixed global (`SPRINT_CAPACITY = 8`), not a setting:
  every sprint that is not Closed is 8 on open, and only a Closed sprint keeps another number as history.
- Sprints are consecutive with no gaps, numbered across the programme (`SPRINT n`); the chain
  continues from Sprint 2 (2026-10-27 to 2026-11-10). Every agent that touches sprints, estimates or
  deadlines carries this rule in its brief.

## One primary tool at a time (the family lock)

Decided 2026-10-27, enforced from asi_dewey 0.3.0 and asi_kletz 0.3.0:

1. **At most one primary tool runs at a time.** While any process of Dewey or Kletz holds an open
   `Brain`, no other process of either tool may open one. Zero or one, never two.
2. **The same tool never runs twice.** Implied by rule 1, enforced separately.

The rule is about processes. One process may hold several `Brain` objects; the slot is released
when the last one closes. It gives every SQLite database exactly one writer and makes "who has the
database open" answerable. It does not reach across machines or user accounts, and it does not
change how OneDrive treats files.

Because the two tools share no code, they agree through this convention, which both implement
independently and both test against a simulated sibling:

| Item | Convention |
|---|---|
| Lock directory | `$ASI_PRIMARY_LOCK_DIR` if set; else `%LOCALAPPDATA%\asi\locks` on Windows; else `$XDG_STATE_HOME/asi/locks`; else `~/.local/state/asi/locks`. Created on demand. Never inside a repo or a synced folder. |
| Family slot | `primary.lock`: held while a primary tool is running. |
| Database slot | `db-<sha1>.lock`, `<sha1>` = SHA-1 of the resolved absolute database path (lower-cased on Windows): held while that database is open. `:memory:` takes none. |
| Primitive | Non-blocking OS advisory lock on byte 0: `msvcrt.locking(fd, LK_NBLCK, 1)` on Windows, `fcntl.flock(fd, LOCK_EX \| LOCK_NB)` elsewhere. The OS releases it on process exit, so no stale locks. |
| Holder info | `primary.json` / `db-<sha1>.json` beside the lock, written best-effort by the holder: `tool`, `version`, `pid`, `started_at` (UTC ISO 8601), `db`, `cwd`, `host`, `user`. Informational; the lock is the truth. |
| Order | Family slot first, then database slot; a failed database slot releases the family slot. |
| Refusal | CLI exit code **3** (1 still means "the command ran and failed"); a JSON `Result` with `ok: false`, `errors[0]` naming the holder, `data = {"slot", "holder"}`. The window shows a Retry / Quit dialog. |
| Waiting | `--wait <seconds>` on every CLI and GUI entry point polls every 250 ms. |
| Override | None. `ASI_PRIMARY_LOCK_DIR` pointed at a private directory is the only escape, and it is what test suites do. This is the one environment variable the primary tools deliberately share. |

Consequences: the CLI is refused while a window is open, so the local automation server is the
way an agent drives a running session; data moves between Dewey and Kletz sequentially through
`dump` and `load` files; agents that drive both tools run them one after the other with `--wait`.

## The family palette

Decided 2026-10-27, implemented in asi_dewey 0.3.0 and asi_kletz 0.3.0:

1. **Both tools keep the grid look, but never look the same.** Each opens in its own house
   default, and each latches its own choice (`%LOCALAPPDATA%\asi\<tool>\prefs.json`, override
   `$ASI_<TOOL>_PREFS`); a pick in one never reaches the other.
2. **One shared catalog.** Both tools offer the same 32 palettes under the same names, so a colour
   learned in one tool means the same thing in the other. The catalog is a convention, carried as
   plain data in each repo's `gui/palettes.py`, never as a shared import.

| Item | Convention |
|---|---|
| Catalog | 8 hue families x 4 tones = 32 palettes. Families in catalog order: Cyan, Blue, Violet, Pink, Red, Amber, Gold, Green. Tones darkest first: `noir`, `neon`, `dusk`, `day`. |
| Key and title | `<family>-<tone>` and `<Family> <Tone>`: `pink-noir` is "Pink Noir". |
| Slots | Every palette fills the same 12 slots: `bg panel field grid stripe primary primary_dim accent text muted on_primary selection`. |
| House default | Both tools open in `cyan-neon` (the former `tron`) until a theme is latched; the two latches are independent (revised 2026-10-27, the draft gave Kletz `amber-neon`). |
| Alias | `tron` -> `cyan-neon` in both tools. `plain` (native look) is a theme, not a palette. |
| Precedence | `--theme` argument, then `$ASI_<TOOL>_THEME`, then the latched preference, then the house default. Only a pick latches. |
| Contrast floors | `text/bg`, `text/field`, `primary/panel`, `primary/bg`, `on_primary/primary`, `on_primary/accent` >= 4.5; `muted/bg`, `muted/panel`, `accent/panel`, `text/selection`, `primary_dim/bg` >= 3.0 (WCAG 2 relative luminance). |
| Digest | SHA-1 of `json.dumps({key: {slot: hex}}, sort_keys=True, separators=(",", ":"))` over the 32 entries: **`befcfc0c8dca5479620badf1d23e2448db757910`**. Each repo's test suite hashes its own catalog and compares it with this value, so a catalog edit in one tool fails until this file, and therefore the sibling, is updated. |

The table below is the authority. Both repos carry it byte for byte.

| Key | Title | bg | panel | field | grid | stripe | primary | primary_dim | accent | text | muted | on_primary | selection |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| `cyan-noir` | Cyan Noir | `#03090a` | `#061113` | `#0b191c` | `#14363d` | `#0d1f23` | `#12c0e2` | `#0f758a` | `#ec7813` | `#b3d4db` | `#508a95` | `#051214` | `#17464f` |
| `cyan-neon` | Cyan Neon | `#040e10` | `#081b1f` | `#0b2328` | `#114550` | `#0e2a2f` | `#1ad9ff` | `#0d88a0` | `#ff851a` | `#c9f0f7` | `#609da9` | `#03171c` | `#125a68` |
| `cyan-dusk` | Cyan Dusk | `#152123` | `#1d2d30` | `#243538` | `#365c63` | `#2a3d41` | `#55d7f1` | `#2d9db4` | `#f19e55` | `#dfeaec` | `#8babb1` | `#0a2429` | `#326b76` |
| `cyan-day` | Cyan Day | `#f2f9fb` | `#e1f1f4` | `#ffffff` | `#b3d4db` | `#edf6f7` | `#0b7286` | `#2b98ad` | `#b35909` | `#152528` | `#627e84` | `#fafcfc` | `#c2e8f0` |
| `blue-noir` | Blue Noir | `#03050a` | `#060b13` | `#0b111c` | `#14233d` | `#0d1523` | `#3277ee` | `#1657c8` | `#ecb613` | `#b3c2db` | `#506a95` | `#050b14` | `#172c4f` |
| `blue-neon` | Blue Neon | `#040910` | `#08101f` | `#0b1628` | `#112850` | `#0e1a2f` | `#2977ff` | `#1157d0` | `#ffc61a` | `#c9daf7` | `#607ba9` | `#030c1c` | `#123268` |
| `blue-dusk` | Blue Dusk | `#151a23` | `#1d2430` | `#242b38` | `#364663` | `#2a3241` | `#558ef1` | `#3066c2` | `#f1ca55` | `#dfe4ec` | `#8b99b1` | `#0a1529` | `#324b76` |
| `blue-day` | Blue Day | `#f2f5fb` | `#e1e8f4` | `#ffffff` | `#b3c2db` | `#edf1f7` | `#0f4fbd` | `#3b71ce` | `#8d6b07` | `#151c28` | `#626e84` | `#fafbfc` | `#c2d3f0` |
| `violet-noir` | Violet Noir | `#06030a` | `#0c0613` | `#130b1c` | `#27143d` | `#170d23` | `#9d53f1` | `#7d20e6` | `#ecc813` | `#c6b3db` | `#725197` | `#0c0514` | `#31174f` |
| `violet-neon` | Violet Neon | `#0a0410` | `#12081f` | `#190b28` | `#2e1150` | `#1d0e2f` | `#a14fff` | `#7e1ded` | `#ffd91a` | `#dfc9f7` | `#8260a9` | `#0e031c` | `#3a1268` |
| `violet-dusk` | Violet Dusk | `#1c1523` | `#261d30` | `#2d2438` | `#4b3663` | `#352a41` | `#ab6df3` | `#8543d0` | `#f1d755` | `#e5dfec` | `#9d8bb1` | `#180a29` | `#523276` |
| `violet-day` | Violet Day | `#f6f2fb` | `#eae1f4` | `#ffffff` | `#c6b3db` | `#f2edf7` | `#600fbd` | `#803bce` | `#836e07` | `#1e1528` | `#726284` | `#fbfafc` | `#d7c2f0` |
| `pink-noir` | Pink Noir | `#0a0306` | `#13060d` | `#1c0b13` | `#3d1429` | `#230d18` | `#ec1380` | `#b51465` | `#13daec` | `#dbb3c7` | `#955073` | `#14050d` | `#4f1733` |
| `pink-neon` | Pink Neon | `#10040a` | `#1f0813` | `#280b1a` | `#501130` | `#2f0e1f` | `#ff1a8c` | `#b80f63` | `#1aecff` | `#f7c9e0` | `#a96085` | `#1c030f` | `#68123d` |
| `pink-dusk` | Pink Dusk | `#23151c` | `#301d26` | `#38242e` | `#63364d` | `#412a36` | `#f155a3` | `#ba2e74` | `#55e4f1` | `#ecdfe6` | `#b18b9e` | `#290a1a` | `#763254` |
| `pink-day` | Pink Day | `#fbf2f6` | `#f4e1eb` | `#ffffff` | `#dbb3c7` | `#f7edf2` | `#bd0f66` | `#ce3b85` | `#077d88` | `#28151f` | `#846273` | `#fcfafb` | `#f0c2d9` |
| `red-noir` | Red Noir | `#0a0304` | `#130608` | `#1c0b0d` | `#3d141a` | `#230d10` | `#ed2641` | `#ba152b` | `#13c8ec` | `#dbb3b9` | `#95505a` | `#140507` | `#4f171e` |
| `red-neon` | Red Neon | `#100406` | `#1f080b` | `#280b0f` | `#501119` | `#2f0e12` | `#ff1a38` | `#bd0f26` | `#1ad9ff` | `#f7c9d0` | `#a9606a` | `#1c0306` | `#68121e` |
| `red-dusk` | Red Dusk | `#231517` | `#301d1f` | `#382426` | `#63363c` | `#412a2d` | `#f1556a` | `#c03043` | `#55d7f1` | `#ecdfe1` | `#b18b90` | `#290a0e` | `#76323b` |
| `red-day` | Red Day | `#fbf2f3` | `#f4e1e4` | `#ffffff` | `#dbb3b9` | `#f7edef` | `#bd0f26` | `#ce3b4f` | `#087a91` | `#281518` | `#846266` | `#fcfafa` | `#f0c2c8` |
| `amber-noir` | Amber Noir | `#0a0603` | `#130c06` | `#1c130b` | `#3d2714` | `#23170d` | `#e27312` | `#934d10` | `#13c8ec` | `#dbc6b3` | `#957050` | `#140c05` | `#4f3117` |
| `amber-neon` | Amber Neon | `#100a04` | `#1f1208` | `#28190b` | `#502e11` | `#2f1d0e` | `#ff851a` | `#a0520d` | `#1ad9ff` | `#f7dfc9` | `#a98260` | `#1c0e03` | `#683a12` |
| `amber-dusk` | Amber Dusk | `#231c15` | `#30261d` | `#382d24` | `#634b36` | `#41352a` | `#f19e55` | `#b46c2d` | `#55d7f1` | `#ece5df` | `#b19d8b` | `#29180a` | `#765232` |
| `amber-day` | Amber Day | `#fbf6f2` | `#f4eae1` | `#ffffff` | `#dbc6b3` | `#f7f2ed` | `#a0520d` | `#c87832` | `#087c94` | `#281e15` | `#847262` | `#fcfbfa` | `#f0d7c2` |
| `gold-noir` | Gold Noir | `#0a0803` | `#131106` | `#1c180b` | `#3d3514` | `#231f0d` | `#e2b912` | `#8a710f` | `#397cef` | `#dbd3b3` | `#958750` | `#141105` | `#4f4417` |
| `gold-neon` | Gold Neon | `#100e04` | `#1f1a08` | `#28220b` | `#504311` | `#2f290e` | `#ffd11a` | `#a0830d` | `#337eff` | `#f7eec9` | `#a99b60` | `#1c1703` | `#685712` |
| `gold-dusk` | Gold Dusk | `#232015` | `#302c1d` | `#383424` | `#635a36` | `#413d2a` | `#f1d255` | `#b4992d` | `#558ef1` | `#ece9df` | `#b1aa8b` | `#29230a` | `#766832` |
| `gold-day` | Gold Day | `#fbf9f2` | `#f4f0e1` | `#ffffff` | `#dbd3b3` | `#f7f5ed` | `#7f680a` | `#a58c29` | `#0b51cb` | `#282415` | `#847d62` | `#fcfcfa` | `#f0e7c2` |
| `green-noir` | Green Noir | `#030a06` | `#06130c` | `#0b1c12` | `#143d25` | `#0d2316` | `#12e269` | `#0f8a42` | `#ec13b6` | `#b3dbc4` | `#50956d` | `#05140b` | `#174f2e` |
| `green-neon` | Green Neon | `#041009` | `#081f11` | `#0b2817` | `#11502b` | `#0e2f1c` | `#1aff79` | `#0da04a` | `#ff1ac6` | `#c9f7dd` | `#60a97e` | `#031c0d` | `#126836` |
| `green-dusk` | Green Dusk | `#15231b` | `#1d3025` | `#24382c` | `#366349` | `#2a4134` | `#55f196` | `#2db465` | `#f155ca` | `#dfece4` | `#8bb19b` | `#0a2917` | `#32764f` |
| `green-day` | Green Day | `#f2fbf5` | `#e1f4e9` | `#ffffff` | `#b3dbc4` | `#edf7f1` | `#0a7b39` | `#28a15b` | `#cb0b9b` | `#15281d` | `#628470` | `#fafcfb` | `#c2f0d5` |

## The JIRA Live Data window

*Removed from the Dewey window in asi_dewey 0.8.1 by the owner's instruction of 2026-10-28; the `jira`
verbs and the Jira ticket cache stay. The convention below now describes the Kletz window alone.*

Decided 2026-10-27, implemented in asi_dewey 0.5.0 and planned for asi_kletz under its own
`docs/jira-live-data-plan.md`. One window, common to both tools, right below the dashboard: a
Refresh button and a big table of every issue past and present, with status, local filters, and a
`[Comments]` button on every row. The window shows Jira and never changes it or stores what it
shows. Same convention, implemented twice, never shared as code.

| Item | Convention |
|---|---|
| Screen | key `jira_live`, title `JIRA Live Data`, module `gui/jira_live_screen.py`, class `JiraLiveScreen`, registered immediately after `dashboard` (second in the navigation list and in `View`) |
| Verbs | `jira live [--jql <jql>] [--limit <n>]` and `jira comments <key>`; the windows never call Hopper themselves |
| JQL default | `--jql`, else `jira_live_jql` in `<tool>.toml`, else `project = HWL AND (assignee = currentUser() OR reporter = currentUser() OR watcher = currentUser()) ORDER BY updated DESC` |
| Limit default | 2000; a longer result is cut and reported as a `Result` warning with `truncated: true` |
| toml keys | `jira_live_jql` (text), `jira_live_autofetch` (bool, default `true`) |
| Columns | Comments (the per-row button), Key, Type, Summary, Status, Category, Priority, Assignee, Reporter, Labels, Parent, Created, Updated, Due, Resolution |
| Row | `key issue_type summary status status_category resolution priority assignee reporter labels components parent_key created updated due url`; lists for labels and components, strings or `null` elsewhere |
| Filters | text on key or summary; Status, Category, Type, Priority, Assignee; all local, never a re-query |
| Comments view | `gui/jira_comments_window.py`, `JiraCommentsWindow(tk.Toplevel)`, one per ticket, title `<KEY> — Comments`, newest first, fed by `jira comments` (`id author created updated body`) |
| Event | `jira.live` with `jql`, `count`, `truncated`, `fetched_at`, `actor`; `jira comments` publishes nothing; neither writes the database or the audit log |
| Storage | none: the last fetch lives on the window object for the life of the process |

## The automation server

Decided 2026-10-27, implemented in asi_dewey 0.7.0 and asi_kletz 0.7.0. The family lock refuses the
CLI while a window is open; the window therefore carries a server, and the CLI forwards to it.

| Item | Convention |
|---|---|
| Transport | HTTP/1.1 JSON over loopback only (`127.0.0.1`, ephemeral port), standard library, a daemon thread owned by the window; every job runs on the Tk thread through a queue drained every 50 ms. |
| Session file | `%LOCALAPPDATA%\asi\<tool>\session.json` beside `prefs.json` (override `$ASI_<TOOL>_SESSION`): `tool`, `version`, `pid`, `started_at`, `host`, `port`, `address`, `token`, `db`. Written when the window opens, removed when it closes, ignored when its pid is dead. |
| Token | A random 32-hex bearer token in the session file; every request carries it; a wrong one is `401 bad token`; a request with an `Origin` header is `403`. The `session` verb never prints it. |
| Endpoints | `GET /status`, `POST /execute` (`{"verb", "args", "actor"}` in, the command's `Result` JSON out, byte-equal to the CLI), `GET /screens`, `POST /screen` (`{"show": "<key>"}`), `GET /events?since=<n>` (events carry `seq`), `POST /quit`. HTTP status is transport only: a failed command is `200` with `ok: false`; `504` when the window does not answer within 60 s. |
| Forwarding | When the lock refuses the CLI and the holder is a window of the SAME tool with a live session, the CLI forwards the verb and exits by its `Result` (0 or 1), printing it untouched; the other tool's window stays exit 3; `--no-forward` restores the plain refusal. |
| `session` verb | `asi-<tool> session` reports the file, whether the pid is alive and the server reachable, and `/status`; `session --quit` asks the window to close. It is the one verb that opens no database. |
| Off switch | `ASI_<TOOL>_AUTOMATION=off` or `--no-server` on the GUI entry points: the window opens without a server and the CLI gets exit 3 with a hint. |
| Window | `◉ ONLINE :<port>` and a one-word theme badge (`CYAN NEON`, `PLAIN`) in the status bar; `theme` through the server recolours the running window (the first remote verb); a remote pick never prompts: it is refused with `ok: false` when the open screen is dirty. |


## How a sibling consumes a generator

Each consumer declares a uv **path source** to the sibling checkout, editable, so pulling a newer
generator is picked up without reinstalling:

```bash
cd asi_dewey          # or asi_kletz, or asi_gutenberg
uv add --editable ../asi_escher
uv add --editable ../asi_gutenberg
```

which writes, with the consumer's own relative paths:

```toml
[project]
dependencies = ["asi-escher", "asi-gutenberg"]

[tool.uv.sources]
asi-escher = { path = "../asi_escher", editable = true }
asi-gutenberg = { path = "../asi_gutenberg", editable = true }
```

One trap: uv honours `tool.uv.sources` only for the root project. When Dewey depends on Gutenberg,
and Gutenberg depends on Escher through *its* path source, Dewey does **not** inherit that source
and would look for `asi-escher` on PyPI. So every consumer declares **both** sources itself, even
though it may only import one directly. Once the repos have remotes, the same lines become pinned
git sources (`{ git = "<url>", tag = "v0.1.0" }`).

## Version ladder

Decided 2026-10-27. The siblings stay in step: the same version number means the same feature
set relative to the charter. Each plan's last phase updates this table and the title table.

| Version | asi_dewey | asi_kletz | Plan | Status |
|---|---|---|---|---|
| 0.3.0 | theme palette | theme palette | `docs/theme-palette-plan.md` | shipped (in the working trees; tagged from 2026-10-28 on, see Releases and tags) |
| 0.4.0 | prune windows | all 63 columns on the Safety Plan screen | `docs/prune-windows-plan.md` / `docs/all-columns-plan.md` | shipped (in the working trees; tagged from 2026-10-28 on, see Releases and tags) |
| 0.5.0 | JIRA Live Data window | JIRA Live Data window | `docs/jira-live-data-plan.md` | shipped (in the working trees; tagged from 2026-10-28 on, see Releases and tags) |
| 0.6.0 | the family lock | the family lock | `docs/single-instance-plan.md` | shipped (in the working trees; tagged from 2026-10-28 on, see Releases and tags) |
| 0.7.0 | automation server, theme badge | automation server, theme badge | `docs/automation-server-plan.md` | planned |
| 0.7.1 | every table column fits its widest text, heading and lookup values included; JIRA Live Data filters as a grid | same | `CHANGELOG.md` | shipped in the working trees (2026-10-27) |
| 0.7.2 | broken-windows sweep: README, CHANGELOG links, plan statuses, docstrings | frozen identity columns on both Safety Plan screens; JIRA Live Data filters as a grid | `CHANGELOG.md` | shipped in the working trees (2026-10-28) |
| 0.7.3 | JIRA Live Data fetches on a worker thread, never on its own; house JQL scoped to the person running the tool | broken-windows sweep; draggable split between pinned and scrolling columns | `CHANGELOG.md` | shipped in the working trees (2026-10-28) |
| 0.7.4 | URL cells and fields are clickable on the Links, QMS Documents and Jira Tickets screens | JIRA Live Data fetches on a worker thread, never on its own; house JQL scoped to the person running the tool | `CHANGELOG.md` | shipped in the working trees (2026-10-28) |
| 0.8.0 | | the Weekly Activity Tracker and sprints: `tracker` and `sprint` verbs, three entities, the one-table rule retired (phases 1 to 3) | `docs/weekly-tracker-plan.md` | shipped in the working tree (2026-10-28) |
| 0.8.1 | | the tracker in the window: the Dashboard's sprint strip, the Sprint Board and Weekly Status screens | `docs/weekly-tracker-plan.md` | shipped in the working tree (2026-10-28) |
| 0.8.2 | | two sprints numbered across the programme: Sprint 1 10/12 to 10/27 (Phase 2, closed), Sprint 2 10/27 to 11/10 (Phase 3, active); `FINAL SPRINTS` retired by owner decision, there is no HARA sprint | `CHANGELOG.md` | shipped in the working tree (2026-10-28) |
| 0.8.3 | | Safety Plan Matrix stacked: PDP phase > safety lifecycle phase > safety deliverable as one indented column beside `order` | `CHANGELOG.md` | shipped and tagged v0.8.3 (2026-10-28) |
| 0.8.4 | | Safety Plan table stacked the same way; the stacking shared by the two screens in `gui/screens.py` | `CHANGELOG.md` | shipped and tagged v0.8.4 (2026-10-28) |
| 0.8.0 (dewey) / 0.9.0 (kletz) | hidden `uid` on every entity, no `id` / `order` column on any table, files carry the uid, imports match by uid, a uid where an id is expected | same | `docs/hidden-identity-plan.md` in each repo | shipped and tagged v0.8.0 / v0.9.0 (2026-10-28) |
| 0.8.1 | the JIRA Live Data screen removed from the window; `jira` verbs kept; the uid merge rule aligned with Kletz | | `CHANGELOG.md` | shipped and tagged v0.8.1 (2026-10-28) |
| 0.9.1 | | the frozen left pane of the Safety Plan and the Matrix opens at its full fitted width, the longest row; `RIGHT_MIN` 120 px for the scrolling pane | `CHANGELOG.md` | shipped and tagged v0.9.1 (2026-10-28) |
| 0.10.0 | | the sprint planner: a `holiday` entity seeded with the US calendar 2026 to 2029, business-day arithmetic, `sprint plan` three years out in 10-business-day sprints that stretch over holidays, `SPRINT_CAPACITY = 8` fixed and enforced on open (closed sprints keep their history), the TIMELINE names the next planned sprint only | `docs/sprint-planner-plan.md` | shipped and tagged v0.10.0 (2026-10-28) |
| 0.11.0 | | points on the Safety Plan row; `default_points` from the four-agent estimation panel (`docs/deliverable-points.md`, 174 points); `sprint` and Expected Duration/Deadline calculated by a pure scheduler over the sprint chain, recomputed after every change; the form shows the default and says so when the typed number differs | `docs/plan-points-plan.md` | shipped and tagged v0.11.0 (2026-10-28) |

### Releases and tags

Decided 2026-10-28. A version exists when three things agree: `version` in `pyproject.toml` (and
`version.py` where the package has one), a dated `## [x.y.z]` section in `CHANGELOG.md`, and an
annotated git tag `vx.y.z` on the commit that made the bump. The tag message is the repo, the
version and the first line of the CHANGELOG entry; `git push origin vx.y.z` publishes it, and a
GitHub Release of the same name carries the CHANGELOG section as its notes, so the release page and
the file never say different things. Tags are never moved; a mistake is a new patch version. The
histories were restarted on 2026-10-28, so versions before the first tag of each repo exist in
`CHANGELOG.md` only. Current tags: asi_dewey v0.8.1, asi_kletz v0.11.0, asi_escher v0.1.1,
asi_gutenberg v0.2.1, asi_hopper v0.1.4; asi_fusa_tools and asi_onboarding carry dates, not versions.

## Where the integration plans live

One row per plan in every repo. Status is one of Implemented x.y.z, Decided, Proposed, Parked.

| Plan | Scope | Status |
|---|---|---|
| [asi_dewey/docs/escher-gutenberg-integration-plan.md](../asi_dewey/docs/escher-gutenberg-integration-plan.md) | Dewey gains `diagram` and `document` verbs, a diagram and a document catalog, and a Generate screen. | Implemented 0.2.0 |
| [asi_kletz/docs/escher-gutenberg-integration-plan.md](../asi_kletz/docs/escher-gutenberg-integration-plan.md) | Kletz gains the same verbs and catalogs for the Safety Plan, inside its fixed four screens (the four-screen rule was retired in 0.5.0). | Implemented 0.2.0 |
| [asi_gutenberg/docs/escher-integration-plan.md](../asi_gutenberg/docs/escher-integration-plan.md) | Gutenberg gains a `diagram` block that Escher renders into a numbered figure. | Implemented 0.2.0 |
| [asi_dewey/docs/theme-palette-plan.md](../asi_dewey/docs/theme-palette-plan.md) | Dewey gains the family palette (32 palettes as plain data, digest-checked against this file) and the latching theme picker; house default `cyan-neon`. | Implemented 0.3.0 |
| [asi_kletz/docs/theme-palette-plan.md](../asi_kletz/docs/theme-palette-plan.md) | Kletz gains the same palette and picker, implemented independently; house default revised to `cyan-neon`. | Implemented 0.3.0 |
| [asi_dewey/docs/prune-windows-plan.md](../asi_dewey/docs/prune-windows-plan.md) | Dewey removes the JIRA, Safety Plan and Safety Plan Matrix screens; the entities, sheets, verbs and the dashboard tile stay. | Implemented 0.4.0 |
| [asi_kletz/docs/all-columns-plan.md](../asi_kletz/docs/all-columns-plan.md) | Kletz's Safety Plan table shows every source column (63 + id) with a Columns selector per flag group; the eight-flag cap is gone. | Implemented 0.4.0 |
| [asi_dewey/docs/jira-live-data-plan.md](../asi_dewey/docs/jira-live-data-plan.md) | Dewey gains the JIRA Live Data window right below the dashboard (`jira live`, `jira comments`, the `[Comments]` column and the comments window), the convention in "The JIRA Live Data window" above. | Implemented 0.5.0 |
| [asi_kletz/docs/jira-live-data-plan.md](../asi_kletz/docs/jira-live-data-plan.md) | Kletz gains the same JIRA Live Data window, the `jira live` and `jira comments` verbs and the asi_hopper dependency; the screen-count rule is retired. | Implemented 0.5.0 |
| [asi_dewey/docs/single-instance-plan.md](../asi_dewey/docs/single-instance-plan.md) | Dewey enforces the family lock: one primary tool at a time, exit code 3, `--wait`. Re-targeted from 0.3.0. | Implemented 0.6.0 |
| [asi_kletz/docs/single-instance-plan.md](../asi_kletz/docs/single-instance-plan.md) | Kletz enforces the same convention, implemented independently. Re-targeted from 0.3.0. | Implemented 0.6.0 |
| [asi_dewey/docs/automation-server-plan.md](../asi_dewey/docs/automation-server-plan.md) | Dewey gains a local automation server (JSON over localhost) so an agent can drive a running window, and the theme badge; its phase 5 adds a section "The automation session" to this file. | Implemented in 0.7.0 |
| [asi_kletz/docs/automation-server-plan.md](../asi_kletz/docs/automation-server-plan.md) | Kletz gains the same automation server and theme badge, implemented independently. | Implemented in 0.7.0 |
| [asi_hopper/docs/integration-plan.md](../asi_hopper/docs/integration-plan.md) | Hopper (read only) into both primary tools: Dewey's `jira_ticket` cache and `jira sync`; Kletz's `jira_key` on deliverables with `jira link` and `jira reconcile`. | Decided; the Dewey side is implemented, the Kletz side is open |
| [asi_hopper/docs/proposal.md](../asi_hopper/docs/proposal.md) | Hopper itself: the audited, replayable Jira door. The read side shipped as 0.1.0; the write side (sandbox, plans, apply) is a design, not a plan with a date. | Read side implemented in 0.1.0; write side delayed indefinitely by owner decision (2026-10-27), not to be raised again |
| [asi_dewey/docs/onboarding-ingest-plan.md](../asi_dewey/docs/onboarding-ingest-plan.md) | Dewey ingests functional-safety knowledge from the ASI onboarding transcripts into `dewey.db` and the generated workbook. | Proposed, not yet executed |
| [asi_kletz/docs/weekly-tracker-plan.md](../asi_kletz/docs/weekly-tracker-plan.md) | Kletz holds the engineer's Weekly Activity Tracker: sprints of ten points, sprint items and weeks; the status text imported once and built back out, whole or for the current week; the one-table rule retired. | Phases 1 to 3 implemented 0.8.0, the screens 0.8.1; the burndown and velocity diagrams and the sprint report open |
| [asi_dewey/docs/hidden-identity-plan.md](../asi_dewey/docs/hidden-identity-plan.md) | Every row carries a hidden `uid`; no table shows `id` or `order`; files carry the uid and imports honour it. | Implemented 0.8.0 |
| [asi_kletz/docs/hidden-identity-plan.md](../asi_kletz/docs/hidden-identity-plan.md) | The same convention for the plan, the sprints, the items and the weeks, implemented independently. | Implemented 0.9.0 |
| [asi_kletz/docs/sprint-planner-plan.md](../asi_kletz/docs/sprint-planner-plan.md) | Holidays, business days, three years of empty 8-point sprints, the capacity rule enforced. | Implemented 0.10.0 |
| [asi_kletz/docs/plan-points-plan.md](../asi_kletz/docs/plan-points-plan.md) | Points per deliverable with panel-estimated defaults; sprint and deadline calculated from the chain. | Implemented 0.11.0 |
| [asi_fusa_tools/docs/fusa-tools-plan.md](docs/fusa-tools-plan.md) | The family document becomes this repo and this file: retitled, hopper and the version ladder added, every sibling's links swept. | Implemented 2026-10-27 |

Existing design documents: [asi_dewey/docs/plan.md](../asi_dewey/docs/plan.md) and
[asi_kletz/docs/plan.md](../asi_kletz/docs/plan.md).

## Layout on disk

```
OneDrive - Capgemini/
├── asi_fusa_tools/      <- charter: this file, its plan, no code
├── asi_dewey/           <- primary tool: working knowledge
├── asi_kletz/           <- primary tool: safety plan
├── asi_escher/          <- generator: diagrams
├── asi_gutenberg/       <- generator: documents
├── asi_hopper/          <- gateway: read-only Jira door, feeds both primary tools
└── Roy's Brain/         <- notes, corpus, datasets (not one of the tools, not a repo)
```

The OneDrive folder itself is not a repository; each `asi_*` folder is one, and `asi_fusa_tools`
is the one repo with no code. Every relative link from a sibling's `docs/` to this file is
`../../asi_fusa_tools/ASI_FUSA_TOOLS.md`, and from a sibling's root `../asi_fusa_tools/ASI_FUSA_TOOLS.md`.

*Written 2026-10-27.*
*Revision 2026-10-27: moved from ASI_FAMILY.md, retitled, hopper and the version ladder added.*
*Revision 2026-10-28: broken-windows sweep across every repo (escher 0.1.1 with a back-edge label fix, gutenberg 0.2.1, hopper 0.1.4 with a self-healing MCP gateway after a rotated token and one-minute call waits, dewey 0.7.3, kletz 0.7.4); frozen identity columns and fitted column widths in the primary tools; a toolchain bootstrap script in Roy's Brain; JIRA Live Data never connects on its own, fetches off the UI thread and is scoped to the person running the tool.*
*Revision 2026-10-28, later the same day: dewey 0.7.4 (URL cells and fields are clickable on the Links, QMS Documents and Jira Tickets screens); kletz 0.8.2 (two sprints numbered across the programme, Sprint 1 10/12 to 10/27 in Phase 2 and Sprint 2 10/27 to 11/10 in Phase 3; the label FINAL SPRINTS retired by owner decision: one dated interval is one sprint, there is no HARA sprint).*
*Revision 2026-10-28, evening: Releases and tags, the first annotated tags and GitHub Releases of every versioned sibling.*
*Revision 2026-10-28, night: kletz 0.8.3, the Safety Plan Matrix stacked by owner request.*
*Revision 2026-10-28, night, later: kletz 0.8.4, the Safety Plan table stacked too.*
*Revision 2026-10-28, late: Identity (`id` and `uid`) and Sprints sections; dewey 0.8.0 and kletz 0.9.0 on the ladder, their plans in the index.*
*Revision 2026-10-28, later still: dewey 0.8.1 (no JIRA Live Data window), kletz 0.9.1 (the frozen pane at its full width); workbooks reconcile by replace.*
*Revision 2026-10-28, night: kletz 0.10.0, the sprint planner and the fixed capacity.*
*Revision 2026-10-28, last: kletz 0.11.0, points and calculated deadlines.*
