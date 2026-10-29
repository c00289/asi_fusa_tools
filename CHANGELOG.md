# Changelog

All notable changes to the ASI FuSa Tools charter are recorded here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/); entries are dated, not versioned,
because this repo holds a document and no code.

## [2026-10-29, the people in a role]

### Added
- asi_kletz 0.18.1 (`gui.log`, the window's trace) and 0.19.0 on the ladder. 0.19.0, the owner, 2026-10-29: "each
  safety deliverable can have multiple authors, so for any safety case field, instead of the parameter being a string
  it is now an array of human names sorted alphabetically; make all updates necessary; also must include email
  alongside name." A role parameter holds people (name and email each, alphabetical, validated; schema 9); a legacy
  name with no email is kept and printed verbatim until people are saved; the export renders a role inline as
  `Name (email), ...`.

### Changed
- The title table: asi_kletz 0.19.0 and asi_gutenberg 0.7.2 (it said 0.18.0 and 0.7.1 while 0.18.1 and 0.7.2 were
  current); the asi_standards row says what the blanks hold (the vehicle type, the people per role, the per-row
  values Kletz supplies). The tags line: kletz 0.15.1 to 0.19.0 and gutenberg 0.7.1 and 0.7.2 committed, untagged.

## [2026-10-29, the sprint law]

### Changed
- The sprint rule, superseded by the owner on 2026-10-29: "what we know as sprint 3 is actually sprint 31, renumber appropriately, and then ensure that sprints always go Tuesday to Tuesday; this supersedes anything I said before; I also included a picture from systems engineering that is law for our sprints." The Policies bullet and the Sprints section
  now say a sprint is 14 calendar days, Tuesday to the Monday 13 days later, 8 points, numbered from Sprint 31
  (2026-10-27 to 2026-11-09), holidays never moving a sprint; the picture's seven sprints are the table there;
  the rule of 2026-10-28 (10 business days, holidays stretching the dates) is quoted as superseded.

### Added
- asi_kletz 0.17.0 and 0.18.0 on the ladder; 0.18.0 in the title table: the cadence guard and `sprint align`.

## [2026-10-29, later the same day]

### Added
- asi_kletz 0.15.1 and 0.15.2 on the ladder and in the title table: a plan out of phase order no longer breaks the
  stacked screens; the mouse wheel over a combobox never picks a value.
- The Policies section: Jira write side closed (2026-10-27), Confluence reads only, controlled documents private,
  the sprint rule (10 business days, 8 points, holidays stretch the dates), the privacy wording (v.00 and the
  biographies deleted for PII).
- `asi_onboarding` in the title table and the layout tree; `asi_standards` in the layout tree.

### Changed
- The 0.7.0 ladder row says shipped (it said planned); the hopper integration row names the Kletz side's
  `jira_key` and ticket picker as implemented; the Kletz-to-Hopper edge no longer says "later"; the tracker
  plan's row says 8 points per sprint, not ten.

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
