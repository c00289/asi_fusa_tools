# The broken-windows sweep

A recurring task for the ASI FuSa tool family (asi_kletz, asi_dewey, asi_escher, asi_gutenberg, asi_hopper,
asi_standards, asi_fusa_tools). Written 2026-10-29 after the first two sweeps of the day, at the owner's word:
"do cleanup and broken windows policing for every repo individually, use agents to distribute load, then look
at all repos together and do systemic cleanup and broken windows policing". Run it at the end of any day that
shipped more than one release, and before any push.

The sweep has two passes. **Pass one** is per repository, one agent per repo, all in parallel: it finds and
fixes what is wrong inside that repo alone. **Pass two** is one agent across all repos: it finds what is wrong
between them (locks, versions, the charter, conventions, integration tests) and finishes what pass one listed as
"left for the integration reviewer". Then the commit, tag and push order below. In Claude Code the whole thing
is the slash command `/family-sweep` (the skill beside the repos in `.claude/skills/family-sweep`), which spawns
the agents with the briefs of section 3 and 4 and reports.

## 1. Before starting

- `git status` and `git log --oneline -3` in every repo. The owner may run a second Claude session in the same
  repos: uncommitted files that are not yours belong to it. Never revert them, never sweep them into your
  commit (`git add <your paths>` only; if a shared file such as a CHANGELOG holds both hunks, commit yours by
  rebuilding the file from HEAD plus your entry, then put the other hunks back in the working tree). If the
  other session seems finished with a file (its edit is coherent and complete), finishing it and committing it
  with a message that says so is fine.
- A Kletz or Dewey window open? `%LOCALAPPDATA%\asi\kletz\session.json` (dewey likewise) says so. The CLI
  forwards into an open window and runs its loaded code; verify new code with `--no-forward` and
  `ASI_PRIMARY_LOCK_DIR` pointed at a scratch directory, never by writing to the live database while the window
  is open, and never `session --quit` the owner's window.
- Never write to a live database (`kletz.db`, `dewey.db`) or a machine config (`kletz.toml`, `dewey.toml`,
  `hopper.toml`); rehearse on a copy (`--db <copy>`), back up first (`sqlite3 ... .backup`), apply after.
- The OneDrive trap: `uv sync` fails with "Access is denied" removing an old `*.dist-info` under
  `.venv/Lib/site-packages`, and leaves the package uninstalled. Clear the folder's read-only attribute, delete
  it, `uv sync` again. It recurs on every version bump.

## 2. What pass one looks for, in every repo

| Area | Check | Fix if small |
|---|---|---|
| Tests | `ruff check src tests` (and `ruff format --check` where the repo enforces it); the full suite three times when anything looks flaky; nothing skipped that should run here (Word, the OCR engine, the private shelf, the Hopper fixtures are present on the owner's machine) | failures, flakes with a real cause |
| Versions | pyproject, the version module, README's version line and "What is in", CHANGELOG's top entry, `uv.lock` all agree; the CHANGELOG footer's compare links cover every version; every heading dated; "Unreleased" lists only the unshipped | bump everywhere, add links in the footer's existing form |
| Locks | `uv lock --check` passes; sibling versions in the lock match the siblings' committed `main` | `uv lock`, note it in the CHANGELOG the way the repo does ("the lock takes X") |
| Docs | README describes the code as it is (verbs, screens, flags, config keys, layout tree, test files); docs/ plans carry a status line; nothing describes removed behaviour; the owner's words are quoted where a release came from an ask | rewrite the stale sentence, never delete history |
| Code | dead functions, duplicated helpers after a move, unused imports, stray `__pycache__` of removed modules, TODO/FIXME, `print` in src, docstrings naming a sibling that no longer applies | remove or re-aim |
| Hygiene | `.gitignore` covers venvs, caches, out/, live databases, workbooks, machine configs, while fixtures stay tracked; packaging builds a wheel with only the package; the CI workflow checks out the siblings the lock names and its comments are true | fix |
| Privacy and policy | no secret in a tracked file (report the location, never the value); controlled content from asi_standards nowhere in a public repo (grep the QF-17 body, the HARA, the control structure's names); real names of people in public repos reported, not edited; the privacy wording "deleted for PII"; Roy's name never in a longer form | report; fix wording only |
| Tk windows | the combobox/spinbox wheel unbind present with its test; unique Treeview iids; nothing slow on the Tk thread; `gui.log` written | fix with a regression test |
| Policies | Jira writes closed; Confluence reads only; the sprint law (Tuesday-to-Monday fortnights, Sprint 31 = 2026-10-27, 8 points); exports fill the Word original in place; STPA lives in Kletz, Dewey is experimental | report a drift as a finding, never "fix" a policy |

Fix what is small and safe in the repo's own conventions (Keep a Changelog entry under a patch version dated
today with the owner's framing; a regression test for any behaviour change; the repo's commit-message style;
every commit ending with the attribution line in use). Describe larger problems precisely and leave them for
pass two or the owner. Leave the tree clean.

## 3. The pass-one brief (one per repo, parallel)

Give each agent: the repo path and its venv; today's date and HEAD; what shipped today in that repo and by
whom; the consumers and producers among the siblings (read-only for it); the hard rules above; the table of
section 2; the conventions; and this report shape: tests before/after with counts, commits (hash and one
line), fixed, left with why and where, integration risks (file:line), anything needing the owner's decision.
asi_standards (private, documents) gets the hash verification of every `SHA256SUMS` instead of tests, and the
leak grep of the public siblings. asi_fusa_tools (the charter) is swept in pass two, because every other
repo's version moves during pass one.

## 4. Pass two, all repos together (one agent, after pass one)

1. Relock the consumers to the producers' committed versions (dewey and kletz on escher, gutenberg, hopper;
   gutenberg on escher); `uv lock --check` in every code repo.
2. Run every suite as CI does; confirm the Jira and Confluence replay tests ran and did not skip; confirm the
   consumers' import surfaces still exist in the producers at their current versions.
3. The charter (`ASI_FUSA_TOOLS.md`): the title table's versions, the "Current tags" line, the ladder rows for
   every release of the day, the plans index, the Policies section; its CHANGELOG entry.
4. Family conventions: the two GUIs share palette names, lock dir, session files, the wheel unbind, the trace
   log, by convention not code; `asi_standards/tools` still runs against Kletz's schema.
5. Everything pass one left "for the integration reviewer" that is small and safe; the rest listed for the
   owner.

## 5. Commit, tag, push

Tags are annotated, `v<version>`, message `<repo> <version>`, on the LAST commit that carries that version
(a release's docs phase, or the follow-up test commit), not on the next release's first-phase commit that still
carries the old number. Push in dependency order so each repo's CI finds the sibling versions its lock names:
asi_escher, asi_hopper, asi_gutenberg, then asi_dewey and asi_kletz, then asi_standards and asi_fusa_tools, with
`--follow-tags` on the code repos. Pushing is the owner's call: Claude Code's safety check treats it as a
publication and asks; nothing is pushed without that approval.

## 6. What the first sweeps found (so the next one knows where to look)

- Stale `uv.lock` entries three releases behind, in every consumer, every time.
- CHANGELOG compare links stopping several versions back; placeholder domains in some repos (owner's decision).
- README layout trees and test lists missing the day's new modules; "What is in" sections missing a release.
- CI workflow comments copied from a sibling (claiming Tk tests in a repo without a GUI).
- Dead helpers after a move (`issues_to_strings`, `_para_shading`, `bulk_insert`, `all_columns`).
- The OneDrive read-only dist-info, in every venv, every bump.
- A test expectation stale against a rule shipped the same day (the "2 unfilled" pane test).
- Real names in public repos (demo approvers, a tracker fixture, recorded Jira evidence), reported, not edited.
- asi_onboarding public while its corpus says internal (owner's decision).

What the first pass two found on top of that:

- A consumer relocked by its own pass one to the producer versions of the morning while a producer's pass one
  bumped it again in the afternoon (kletz 0.24.1 on escher 0.2.1 and gutenberg 0.9.0 against the committed 0.2.2
  and 0.9.1): pass two relocks last, every consumer, whatever pass one wrote.
- A fix made in one primary tool and never mirrored into the sister (kletz 0.18.1's `gui.log` trace, absent from
  dewey until 0.11.2): list the `gui/` modules of each and read the difference.
- A tool beside a controlled document two increments behind the document (`asi_standards/tools/roles_from_kletz.py`
  still matching the Deliverable cell's text after the cell became a link, and writing the Status cell without the
  Jira key): the test is a dry run on a copy of the source, with the rows of a copy of the owner's database, that
  must reproduce the copy byte for byte, line endings included (asi_standards is `autocrlf`: the working copy is
  CRLF, the blob LF).
- A venv whose stale `*.dist-info` still names the old sibling version while the editable install already runs the
  new code: check an import surface against the checkout, and clear the read-only folders and `uv sync` before
  trusting the metadata. The same stale folder, left without its `RECORD`, is what a consumer's suite reports as
  `DeprecationWarning: Implicit None on return values` from `importlib.metadata` (the producer's `version()`
  lookup finding the husk first); deleting the folder ends it.
- `pytest -q` on top of `addopts = "-q"` (escher, hopper, gutenberg) is `-qq`: no summary line, in CI's log
  either; the counts are the progress characters (`.`, `s`, `F`), or one run with `-o addopts=""`.
- `ruff format --check` with no path formats the code blocks of the Markdown too (hopper's README and
  `docs/integration-plan.md` would change); CI's `ruff format --check src tests` is the check that counts.
