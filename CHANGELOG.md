# Changelog

All notable changes to the ASI FuSa Tools charter are recorded here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/); entries are dated, not versioned,
because this repo holds a document and no code.

## 2026-10-29

### Added
- asi_kletz 0.15.0: the exported points are the weight the schedule used; a Jira ticket picker on every work product.
- asi_kletz 0.14.1 and asi_gutenberg 0.6.0: the `section` block; the export keeps the original's page orientations.
- asi_kletz 0.14.0 on the ladder: standards, `jira_key`, the `parameter` entity, `plan export`, the export button and the
  parameters box on the Safety Plan's details.
- asi_gutenberg 0.5.0 (`--params-file`); the QF-17 Safety Plan's 144 row parameters, filled by Kletz.
- asi_gutenberg 0.4.1 in the title table and the tags line (the `==mark==` highlight for filled parameters);
  the QF-17 Safety Plan's 20 role parameters.
- asi_gutenberg 0.3.1 in the title table and the tags line; the QF-17 Safety Plan's first parameter.
- `asi_standards` (private) in the title table: the parametrised standards, the QF-17 Safety Plan first; the
  controlled-document house rule; asi_gutenberg 0.3.0 (`from_docx`, parameters, the command line, landscape pages).
- The ladder: asi_kletz 0.11.4, 0.12.0 (charts, sprint report, roadmap), 0.13.0 (plan_uid, CI), 0.13.1 and asi_dewey
  0.8.2 (the scrollbar thumb); the tracker plan's row says fully implemented; the thumb note under the family palette.
- The continuous-integration house rule: ruff and pytest on windows-latest on every push, tag and pull request, in
  every Python repo (asi_kletz's workflow ships with its 0.13.0).

## [2026-10-28, later the same day]

### Changed
- asi_kletz 0.11.3 on the ladder: the Safety Plan screen is one table, the flags live on the Matrix.
- asi_kletz 0.11.2 on the ladder: Sprint start and Sprint end beside Sprint.
- asi_kletz 0.11.1 on the ladder: a Refresh on a rejected Atlassian session signs in; the JIRA Live Data section says so.
- asi_hopper 0.1.5 in the title table: the gateway reports the leaf of an exception group, `auth status` probes the session.
- asi_kletz 0.11.0 on the ladder and its plan in the index: points, panel defaults, calculated sprint and deadline.
- asi_kletz 0.10.0 on the ladder and its plan in the index: holidays, the sprint planner, the fixed capacity of 8.
- asi_dewey 0.8.1 (the JIRA Live Data window removed from Dewey; the convention section now describes Kletz
  alone) and asi_kletz 0.9.1 (the frozen pane at its full fitted width) on the ladder; workbooks reconcile by replace.
- New sections "Identity: `id` and `uid`" and "Sprints (asi_kletz)"; the hidden-identity plans in the index;
  asi_dewey 0.8.0 and asi_kletz 0.9.0 on the ladder.
- asi_kletz 0.8.4 on the ladder: the Safety Plan table stacked the same way as the matrix.
- asi_kletz 0.8.3 on the ladder: the Safety Plan Matrix stacked (PDP phase > lifecycle phase > deliverable).
- Releases and tags: a version is `pyproject.toml` + a dated CHANGELOG section + an annotated `vx.y.z` tag with a
  GitHub Release carrying that section; first tags on every versioned sibling.
- The version ladder and the title table: asi_dewey 0.7.4 (clickable URL cells and fields on Links, QMS
  Documents and Jira Tickets), asi_kletz 0.8.2 (two sprints numbered across the programme; `FINAL SPRINTS`
  retired by owner decision, there is no HARA sprint).

## [2026-10-27, later the same day]

### Changed
- The automation server convention (0.7.0 in both tools), the version ladder marked shipped through 0.7.0,
  asi_hopper 0.1.1 (event size limit, `LIST_FIELDS`, page size), the plans index statuses.

## 2026-10-27

### Changed
- Moved from the loose `ASI_FAMILY.md` in the OneDrive folder into this repo as
  `ASI_FUSA_TOOLS.md` and retitled "ASI FuSa Tools"; `asi_hopper` (the Jira gateway, 0.1.0) and
  this repo join the title table and the layout tree; the house rules gain the documentation-repo
  line and the rule that conventions keep their names; the version ladder and the full plans
  index are added. See `docs/fusa-tools-plan.md`.
