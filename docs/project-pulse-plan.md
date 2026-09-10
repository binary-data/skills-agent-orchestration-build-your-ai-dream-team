# Project Pulse implementation plan

## Objective

Build Mona's **Project Pulse** as a dependency-free static dashboard in `app/`. The dashboard loads project data from JSON, renders accessible project cards, uses responsive CSS, and launches through VS Code's **Run Project Pulse Dashboard** configuration. Use semantic HTML, CSS, browser JavaScript embedded in `app/index.html`, and a local Python HTTP server because fetching `project-data.json` can fail under `file://`.

## Shared architecture and contract

- `app/index.html` provides the shell, links `styles.css`, references `project-data.json`, and contains a semantic `<main class="dashboard">`.
- The page has the exact title **Project Pulse**, a contributor description, a project grid/list, and distinct loading, empty, and error states.
- Embedded browser JavaScript fetches `project-data.json`, validates the response and its `projects` array, then renders one `.project-card` per project.
- Each card displays `name`, `owner`, `status`, `recentActivity`, `priority`, and a contributor-friendly `summary`.
- Rendering uses `textContent` and other safe DOM APIs; project values are never injected as HTML.
- Fetch failures, invalid JSON, missing or non-array `projects`, and empty project data are surfaced to the user with an understandable state rather than silently ignored.
- The data contract is:

  ```json
  {
    "projects": [
      {
        "name": "string",
        "owner": "string",
        "status": "string",
        "recentActivity": "string",
        "priority": "string",
        "summary": "string"
      }
    ]
  }
  ```

- `name`, `owner`, `status`, `recentActivity`, and `priority` are required fields. `summary` is included for every representative fixture and is additive to the minimum required schema.
- Status and priority treatment must remain understandable without color alone. Unknown status or priority values must still render safely and legibly.

## File assignments

| Owner | File | Responsibilities |
| --- | --- | --- |
| Planner | `docs/project-pulse-plan.md` | Preserve this implementation plan, the shared contract, sequencing, validation expectations, assumptions, and edge cases. |
| Designer | `app/styles.css` | Define hierarchy, responsive layout, status and priority treatment, card styling, focus states, contrast, and reduced-motion behavior. Provide and style the `.dashboard` and `.project-card` hooks used by the shell and renderer. |
| Coder | `app/index.html` | Implement semantic markup, the exact Project Pulse title, contributor description, project grid/list, loading/empty/error states, embedded data loading, response/schema validation, safe rendering, and required card content. |
| Coder | `app/project-data.json` | Provide valid representative multi-project fixtures matching the agreed schema and including all required fields plus `summary`. |
| Coder | `.vscode/launch.json` | Provide strict JSON with a configuration named **Run Project Pulse Dashboard**, running `python3 -m http.server 5500`, using `cwd` `${workspaceFolder}/app`, and a `serverReadyAction` that opens `http://localhost:%s/index.html`. |
| Orchestrator | Integration review (no additional ownership change) | Review the combined implementation, resolve contract or integration issues with the owners, and confirm that no unrelated files changed. |

Designer and Coder must not edit each other's assigned files without coordination. The Planner assignment is limited to this plan file.

## Dependencies and work order

1. **Repository research and shared contract:** The Orchestrator and contributors first inspect the repository conventions and confirm the file scope, semantic structure, JSON shape, CSS hooks, launch requirements, and validation criteria. The data schema must be agreed before rendering is finalized.
2. **Parallel implementation after contract agreement:** Designer work on `app/styles.css` and Coder work on `app/index.html`, `app/project-data.json`, and `.vscode/launch.json` may proceed in parallel because their primary file scopes do not overlap. The Designer depends on the agreed `.dashboard` and `.project-card` hooks; the Coder depends on the agreed JSON field names and launch settings.
3. **Sequential integration review:** After both workstreams finish, the Orchestrator reviews the implementation in sequence. Check CSS hooks against markup, JSON fields against rendering, required card content, safe error handling, loading/empty/error state transitions, launch URL, and unrelated changes. Resolve any mismatch before validation.
4. **Sequential validation and handoff:** Run the validation checklist only after integration review passes, then hand off the complete dashboard with any remaining limitations stated explicitly.

The parallel decision is limited to non-overlapping implementation files after the contract is stable. Integration review and validation are sequential because they require the combined markup, styles, data, and launch configuration.

## Validation expectations

### Scope and static inspection

- Verify `app/index.html`, `app/styles.css`, `app/project-data.json`, `.vscode/launch.json`, and this plan exist.
- Inspect for the required strings and hooks: `Project Pulse`, `styles.css`, `project-data.json`, `.project-card`, `status`, `recentActivity`, `priority`, `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
- Verify the data file contains the expected JSON keys and representative multi-project fixtures.
- Verify launch configuration name **Run Project Pulse Dashboard**, `python3 -m http.server 5500`, `cwd` `${workspaceFolder}/app`, and `index.html` in the server-ready URL.
- Run `python3 -m json.tool` on both JSON files: `app/project-data.json` and `.vscode/launch.json`.
- Confirm there are no external dependencies and that only `docs/project-pulse-plan.md` is changed for this planning task.

### Runtime and UX

- Launch through VS Code using **Run Project Pulse Dashboard** and confirm `http://localhost:5500/index.html` opens the dashboard, not a directory listing.
- Confirm cards render from `project-data.json`, including name, owner, status, recent activity, priority, and summary.
- Exercise loading, empty, and error states.
- Check keyboard navigation and visible focus states, sufficient contrast, status/priority communication that is not color-only, responsive behavior at narrow widths, long text wrapping, and reduced-motion behavior.
- Confirm safe text rendering for values containing markup-like or otherwise unsafe text.
- Confirm no browser console errors during normal loading and expected error-state transitions.

## Edge cases to cover

- Missing, malformed, or non-2xx JSON responses.
- Missing `projects`, a non-array `projects` value, or an empty array.
- Missing, blank, unusually long, or unsafe field values.
- Unknown status or priority values.
- Opening the page through `file://` instead of the required local server.
- Port 5500 already being occupied.
- Narrow viewport layout, keyboard-only use, and reduced-motion preferences.

## Assumptions

- There is no framework, package manager, or browser test suite; the implementation remains dependency-free and uses browser APIs only.
- Representative fixture data and a neutral, accessible palette are acceptable.
- `summary` is additive to the minimum required schema and should be present in the supplied fixtures and rendered cards.
- The local Python server is the supported runtime path; `file://` is expected to be unsupported for JSON loading.
