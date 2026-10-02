# Project Pulse implementation plan

## Summary

Build Mona's Project Pulse as a small, static dashboard that lets contributors
scan active projects, owners, current status, recent activity, priority or risk,
and a short contributor-friendly summary. The repository is currently an
exercise template: the Project Pulse brief and custom agent definitions exist,
but the `app/` outputs and `.vscode/launch.json` do not. The existing
`.vscode/tasks.json` only starts the exercise terminal, so it does not provide
the dashboard preview.

The implementation should use plain HTML, CSS, and JSON with no application
framework or package installation. It must open the actual dashboard at
`app/index.html`, rather than a server directory listing, through the
**Run Project Pulse Dashboard** launch configuration.

## Ordered implementation steps

1. **Confirm contracts and scope — Planner/Orchestrator.** Read
   `.github/project-pulse-brief.md`, the agent definitions, and this repository's
   existing setup. Confirm the JSON shape, required visible fields, responsive
   and accessible UI expectations, and the preview command before delegating.
2. **Define the experience — Designer.** Establish the information hierarchy
   for the title, project collection, cards, status badges, priority treatment,
   recent activity, and summaries. Define semantic and accessible markup
   expectations, responsive behavior, visual states, and the CSS hooks needed
   by the page.
3. **Create the shared data contract — Coder.** Create
   `app/project-data.json` with a top-level `projects` array. Every project
   object must contain `name`, `owner`, `status`, `recentActivity`, and
   `priority`; include enough varied projects and statuses to exercise the
   card layout and priority styling.
4. **Implement the visual system — Designer.** Create `app/styles.css` using
   the agreed hierarchy and hooks, including `.dashboard` and
   `.project-card`. Provide polished cards, status/priority differentiation,
   readable spacing and contrast, `border-radius`, `box-shadow`, keyboard/focus
   visibility, and responsive behavior without adding a dependency.
5. **Implement and connect the page — Coder.** Create `app/index.html` with the
   exact title “Project Pulse”, a semantic accessible structure, a reference to
   `styles.css`, and a reference to `project-data.json`. Render visible
   `.project-card` elements from the project data and show each project's name,
   owner, status, `recentActivity`, priority, and contributor-friendly summary.
   The implementation must handle a failed or malformed data load explicitly
   in the UI rather than silently presenting an empty successful state.
6. **Configure the preview — Coder.** Create `.vscode/launch.json` as strict
   JSON with no comments. Add **Run Project Pulse Dashboard**, serve with
   `python3 -m http.server 5500`, set the working directory to the `app`
   directory, and configure `serverReadyAction` to open
   `http://localhost:%s/index.html`.
7. **Integrate and review — Orchestrator with Designer and Coder.** Review all
   four assigned outputs together, resolve selector/schema/launch mismatches,
   and confirm that the first loaded view is the dashboard rather than a
   directory index.
8. **Validate and hand off — Orchestrator.** Run the checks below, manually
   preview the page, record any limitations, and then provide the final
   orchestration handoff. Git operations remain the learner's responsibility;
   agents must not stage, commit, or push.

## Ownership and file assignments

| Owner | Assigned files | Responsibilities |
| --- | --- | --- |
| **Designer** | `app/styles.css` | Own visual design, information hierarchy, responsive layout, accessibility-oriented states, card/status/priority treatment, and the required `.dashboard` and `.project-card` hooks. Supply the markup contract to Coder but do not edit Coder-owned files. |
| **Coder** | `app/index.html` | Own semantic page structure, exact `Project Pulse` title, stylesheet/data references, data loading and rendering, visible project cards, field labels, and explicit data-load error handling. Consume Designer's hooks and contract. |
| **Coder** | `app/project-data.json` | Own the deterministic fixture data and schema. Provide a top-level `projects` array with `name`, `owner`, `status`, `recentActivity`, and `priority` on every object, plus summaries needed by the UI. |
| **Coder** | `.vscode/launch.json` | Own strict launch JSON, the **Run Project Pulse Dashboard** configuration, `python3 -m http.server 5500`, `app` working directory, and the `index.html` server-ready URL. |
| **Orchestrator** | Integration review (no additional output file) | Coordinate Planner, Designer, and Coder, enforce non-overlapping ownership, verify the integrated result, and report validation and remaining risks. |

Designer and Coder should communicate the CSS hooks and JSON schema before
Coder finalizes `index.html`. Neither agent should modify the other agent's
assigned files; integration fixes should be coordinated by the Orchestrator.

## Dependencies and parallel work decisions

### Dependencies

- The brief and agent definitions are the source requirements; they must be
  read before implementation.
- `app/index.html` depends on both `app/styles.css` (selectors and visual
  states) and `app/project-data.json` (the `projects` schema and values).
- `app/styles.css` can be authored without runtime data, but its card and field
  hooks must be agreed before the HTML is considered integrated.
- `.vscode/launch.json` depends on the chosen static app location (`app/`) and
  entry point (`index.html`), but does not depend on the data contents.
- Browser/runtime validation depends on all four outputs existing and being
  integrated.

### Work that can run in parallel

- After the Planner confirms the requirements, Designer can define the visual
  system in `app/styles.css` while Coder creates the independent fixture schema
  in `app/project-data.json`.
- Coder can create `.vscode/launch.json` in parallel with Designer's CSS work
  because it only needs the fixed `app/` directory, port, and entry point.
- Orchestrator can review the brief, agent ownership, and existing repository
  setup while these non-overlapping files are being prepared.

### Work that must be sequential

- Planner/Orchestrator scope confirmation precedes delegation.
- The final `app/index.html` implementation follows the Designer's hook and
  accessibility decisions and the Coder's JSON schema; otherwise the page can
  render data or styles inconsistently.
- Integration review follows completion of all four files.
- End-to-end preview and validation follow integration review; a launch test
  before the app files exist cannot prove the required dashboard behavior.

## Dependencies and tooling

Runtime dependencies are intentionally limited to a browser and Python 3's
standard `http.server`; no npm package, framework, build step, or external API
is required. Repository tooling already supplies GitHub Copilot CLI, the
Codespace environment, `.vscode/tasks.json`, and
`scripts/validate-exercise.sh`. The launch configuration is the new runnable
support file and should use the existing repository convention of strict JSON.

## Risks and edge cases

- **Schema drift:** Missing or renamed fields can produce incomplete cards.
  Validate every fixture object and make the renderer report malformed data.
- **Empty or failed data:** An empty `projects` array, invalid JSON, or a
  failed fetch must show a clear user-facing error/empty state, not a blank
  dashboard or an exception hidden in the console.
- **Local fetch behavior:** Loading JSON through `file://` may be blocked by
  browser CORS rules; use the configured HTTP server for preview.
- **Directory listing:** Serving the repository root or omitting `index.html`
  from `serverReadyAction` opens the wrong experience. Keep `cwd` at `app` and
  the URL suffix explicit.
- **Accessibility:** Status and priority must not rely on color alone; use
  text, semantic headings, labels, adequate contrast, visible focus, and a
  usable narrow-screen layout.
- **Long content:** Long owner names, activity text, summaries, or project
  names must wrap without overflowing cards or breaking the responsive grid.
- **Unexpected status/priority values:** Use readable fallback styling/text
  rather than assuming only the fixture values can ever occur.
- **Port conflicts:** Port 5500 may already be occupied. Report the conflict
  explicitly and stop the conflicting preview process before retrying; do not
  silently change the documented launch contract.

## Validation expectations

1. Confirm only the assigned outputs are created or changed for this phase.
2. Parse `app/project-data.json` and `.vscode/launch.json` with a JSON parser.
   Verify the data has a top-level `projects` array and that every project has
   `name`, `owner`, `status`, `recentActivity`, and `priority`.
3. Inspect `app/index.html` for the exact `Project Pulse` title, the
   `styles.css` and `project-data.json` references, semantic/accessibility
   attributes, the `project-card` class, and rendering of status, recent
   activity, priority, and summary values.
4. Inspect `app/styles.css` for `.dashboard`, `.project-card`,
   `border-radius`, `box-shadow`, responsive rules, contrast, and visible
   focus treatment.
5. Inspect `.vscode/launch.json` for strict JSON, the exact launch name,
   `python3 -m http.server 5500`, an `app` working directory, and
   `http://localhost:%s/index.html`.
6. Start **Run Project Pulse Dashboard** and verify in a browser or equivalent
   HTTP check that `/index.html` returns the dashboard entry point, the page
   loads its CSS and JSON, and multiple project cards are visible. Confirm
   that the browser does not show a directory listing.
7. Exercise at least a narrow viewport and keyboard focus order. Check that
   long content remains readable and that status/priority meaning remains
   available without color.
8. Run `scripts/validate-exercise.sh` when the complete exercise outputs are
   present, and address any relevant failure without changing unrelated
   template files.

## Open questions

- The brief requires a contributor-friendly summary but does not prescribe a
  JSON property name. Use a `summary` property consistently in the fixture
  data and UI unless Mona supplies a different schema.
- The brief does not define an authoritative set of status or priority values.
  Choose a small, clearly labeled fixture vocabulary and make unknown values
  render safely.
- No persistence, filtering, sorting, authentication, or live project API is
  requested. Treat this as a deterministic static preview unless a later
  requirement expands the scope.
