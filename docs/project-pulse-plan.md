# Project Pulse implementation plan

## Summary

Build a small, static Project Pulse dashboard that gives Mona an at-a-glance view of project health. The experience should be useful without a backend or build step: the HTML provides the semantic application shell, CSS provides the responsive visual system, and `project-data.json` provides deterministic sample data that can be replaced or extended later. A VS Code launch configuration will make the dashboard easy to preview from the repository.

The initial release should communicate overall project status, key delivery metrics, active work, recent activity, and risks or blockers. It should work on desktop and narrow screens, use accessible semantic markup and keyboard-friendly controls, and remain understandable when data is empty, delayed, or unusually large.

## Ordered implementation steps

1. **Confirm the contract.** Planner and Orchestrator agree on the dashboard content, JSON schema, supported browser assumptions, preview command, and the boundaries of each assigned file.
2. **Define representative data.** Populate `app/project-data.json` with a coherent Project Pulse snapshot covering project metadata, health, metrics, work items, activity, and risks. Keep values deterministic and make the schema easy to consume from a static page.
3. **Design the visual language.** Designer establishes the information hierarchy, responsive layout, color tokens, typography, status treatments, card/table patterns, focus states, and empty-state treatment in `app/styles.css`, using the planned HTML structure as the integration contract.
4. **Implement the document shell.** Coder creates `app/index.html` with semantic landmarks, dashboard sections, stable IDs/classes, accessible labels, and a data-loading/rendering path appropriate for a static JSON file. Do not duplicate data in HTML when the page can render it from the JSON source.
5. **Implement the preview configuration.** Coder adds `.vscode/launch.json` as strict JSON with a deterministic launch name, a local static-server command or supported browser preview, a stable port, and `cwd` set to `${workspaceFolder}/app`. The default URL must open `index.html`, not a directory listing.
6. **Integrate the surfaces.** Orchestrator checks that HTML selectors match CSS selectors and data keys, that the launch configuration starts from the correct directory, and that no unassigned files are required for the core experience.
7. **Exercise normal and degraded states.** Review the page with the supplied data, with missing/empty collections, and at narrow viewport sizes. Confirm that missing optional values do not produce broken labels, misleading zeros, or inaccessible controls.
8. **Complete the handoff.** Record any unresolved product decisions, keep the plan and implementation aligned, and report the exact preview and validation steps for the learner.

## File assignments

### `app/index.html` - Coder

- Create the complete semantic page shell, including `header`, `main`, and section-level headings.
- Include a concise project header with project name, reporting period, overall health, and last-updated information.
- Provide regions for summary metrics, milestone or delivery progress, active work, recent activity, and risks/blockers.
- Use headings in a logical order, descriptive table headers where tabular data is used, and lists for collections of cards or activity items.
- Add accessible status text rather than relying on color alone; use visually hidden text where needed to clarify icons or compact labels.
- Load the local JSON data and render the dashboard deterministically. Handle fetch failures with an explicit, user-visible error state rather than a silent blank page.
- Keep content structure and presentation separate. Use stable class names and IDs documented through clear naming, without embedding layout styles in the HTML.
- Include responsive-friendly markup and a meaningful document title, language attribute, viewport metadata, and a skip link.

### `app/styles.css` - Designer, implemented by Coder

- Define a small set of reusable design tokens for colors, spacing, typography, borders, radii, elevation, and focus treatment.
- Establish the desktop layout and responsive breakpoints for cards, tables, activity feeds, navigation/header content, and risk panels.
- Distinguish healthy, caution, blocked, and neutral states through both color and text/pattern differences.
- Ensure sufficient contrast, visible keyboard focus, readable line lengths, sensible wrapping, and touch-sized interactive targets.
- Include `prefers-reduced-motion` behavior and avoid animation as the only way to communicate updates.
- Provide empty, loading, and error presentation styles even if the initial data set is complete.
- Keep the stylesheet self-contained and avoid external font, icon, CSS, or image dependencies unless explicitly approved.

### `app/project-data.json` - Coder with Planner input

- Define and document through naming a stable schema for:
  - project identity and reporting metadata;
  - overall health/status and explanatory summary;
  - metric cards with labels, values, units, trends, and optional targets;
  - milestones or delivery progress;
  - active work items with status, owner, priority, and due date;
  - recent activity with timestamp, actor, event type, and description;
  - risks/blockers with severity, owner, impact, and mitigation.
- Use valid JSON only: no comments, trailing commas, or executable expressions.
- Use realistic but clearly sample/project-neutral values, consistent date formats, and stable status vocabulary.
- Include enough records to exercise wrapping, sorting/display order, and responsive layouts without making the initial view noisy.
- Keep the data local and deterministic so the dashboard can be previewed offline.

### `.vscode/launch.json` - Coder

- Add a strict JSON launch configuration for the static dashboard.
- Set `cwd` to `${workspaceFolder}/app`.
- Open `index.html` directly at a fixed local URL and port.
- Use an existing repository-supported command or a broadly available static preview mechanism; do not add a dependency solely for launch configuration.
- Keep the configuration deterministic and easy for a learner to select from the Run and Debug view.
- Do not modify the existing `.vscode/tasks.json` unless integration proves it is necessary.

## Designer responsibilities

- Translate the dashboard goals into a clear hierarchy: health first, then actionable metrics, then delivery details and exceptions.
- Specify the visual states and responsive behavior before implementation, including how the layout collapses on narrow screens.
- Prioritize readability and scanability over decorative elements.
- Define accessibility expectations for color, focus, headings, labels, tables, and status announcements.
- Provide the Coder with the intended component/selector structure and any content-length assumptions that affect layout.
- Review the integrated result against the intended hierarchy and identify visual regressions without taking ownership of application/data wiring.

## Coder responsibilities

- Implement only the assigned application and preview files, following the Designer's structure and the agreed data contract.
- Wire the page to local JSON with explicit loading, success, empty, and error states.
- Preserve type/shape assumptions through defensive validation of optional fields and safe text rendering.
- Keep the implementation dependency-free unless the repository already provides a required runtime.
- Verify semantic HTML, keyboard access, responsive behavior, valid JSON, and the launch path.
- Resolve integration issues with the Orchestrator and report any assumptions that affect future API or data-source work.

## Dependencies

- `app/index.html` depends on the schema in `app/project-data.json` and the selector/layout contract in `app/styles.css`.
- `app/styles.css` depends on the semantic elements and class names chosen for `app/index.html`; Designer input should precede final CSS implementation.
- `.vscode/launch.json` depends on the final location of `index.html` and on an available static preview command.
- The browser must be able to fetch a local JSON file. Because many browsers block `fetch()` from `file://` pages, preview through the configured local server rather than opening the HTML directly from the file system.
- No remote API, database, authentication, package installation, or external asset host is required for the initial release.

## Parallel work decisions

- Planner can finalize the data contract and acceptance criteria in parallel with Designer defining the visual hierarchy and responsive rules.
- Once the contract and markup outline are agreed, Designer can refine `app/styles.css` while Coder creates the HTML shell and representative JSON in parallel, provided both honor the shared names and states.
- The launch configuration can be prepared in parallel with the static page implementation once the preview command and entry point are known.
- Integration, cross-file selector/data checks, and final validation must happen after the parallel work is available; these are not parallel-safe activities.

## Sequential dependencies

1. Agree on content, states, schema, and preview approach before implementation begins.
2. Establish the HTML structure and selector/data contract before CSS is considered final.
3. Create coherent JSON before validating rendering and empty/error behavior.
4. Set the final launch URL and working directory after the entry file exists.
5. Run integration and responsive/accessibility review only after all four assigned files are present.

## Edge cases

- JSON fetch or parsing fails: show a clear error panel with a recovery instruction; do not leave an empty dashboard.
- A collection is empty: show a useful empty state with context instead of an empty card or broken list.
- Optional fields such as owner, target, trend, mitigation, or due date are absent: omit the field cleanly and preserve alignment.
- A metric has no denominator, target, or trend: avoid displaying misleading percentages, arrows, or zero values.
- Status/severity values are unknown: fall back to a neutral visual treatment while retaining the original text.
- Long project names, owner names, activity descriptions, or risk text wrap without overlapping controls or changing the meaning.
- Dates are malformed, missing, or in a different time zone: display a safe fallback and avoid claiming a relative time that cannot be trusted.
- Large work/activity/risk collections: preserve a readable layout and clear ordering; do not rely on a fixed card height that clips content.
- A narrow viewport or zoomed text: content remains usable without horizontal scrolling except where a genuinely wide data table requires it.
- Reduced-motion preference: transitions and animated indicators are disabled or minimized.
- Keyboard-only and screen-reader use: all meaningful information is available without hover, color, or pointer-only interaction.
- Local preview is opened incorrectly via `file://`: the launch configuration and visible error guidance should direct users to the local server.

## Validation expectations

- Parse `app/project-data.json` with a JSON parser and confirm it has no comments or trailing commas.
- Open the configured launch target and confirm it serves `app/index.html` at the expected local URL.
- Confirm the page renders all planned regions from the supplied JSON and that the visible values match the data.
- Exercise fetch failure and empty collection states without producing uncaught console errors or silent failures.
- Check desktop and narrow viewport layouts, browser zoom, long text, and keyboard tab order.
- Confirm every interactive element has an accessible name, focus is visible, headings are ordered, and status meaning is not conveyed by color alone.
- Confirm the default page has no horizontal overflow, clipped text, broken assets, or directory-listing landing page.
- Review the final four-file integration for selector mismatches, missing data keys, invalid launch JSON, and reliance on undeclared dependencies.

## Open questions

- What exact Project Pulse metrics and thresholds should define healthy, caution, and blocked status in the product version?
- Should the initial dashboard display a single project, or should the data model reserve space for switching among multiple projects?
- Which browser(s) and VS Code preview extension/command are guaranteed in the learner's environment?
- Should relative timestamps be computed in the browser, or should the source data provide already formatted display strings?
- Is sorting/filtering required for the first release, or is a deterministic curated view sufficient?
- What is the desired refresh model once the static sample data is replaced by a live source?
- Should risk and blocker details link to issues, pull requests, or another project-management system in a later iteration?
