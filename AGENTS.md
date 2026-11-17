# AI Agent Working Agreement — Code-first Observability Lab

> Read this entry point before changing the project. Keep changes scoped to the task and preserve existing work in the working tree.

## 1. Priorities

1. Preserve correct behavior and type safety.
2. Reuse approved tokens and UI components before adding new ones.
3. Keep domain components separate from primitive UI components.
4. Prefer small, reviewable changes over wholesale rewrites.
5. Do not claim verification unless the commands actually ran.

## 2. Context required before making changes

Read the relevant implementation and tests before editing. For pages or shared UI, read in order:

- [UI Guidelines](docs/ui-guidelines.md) (design language + change rules)
- `src/styles/tokens.css` (canonical semantic tokens)
- `src/components/ui/` (reusable primitives)
- the specific page and any affected domain components
- `tests/behavior.spec.ts` and `tests/visual.spec.ts` (acceptance criteria)
- [Code Guidelines](docs/code-guidelines.md) (code organization, contracts, comments and documentation references)
- [Workflow](docs/workflow.md) and [Validation](docs/validation.md) (execution process and verification requirements)

For non-UI tasks, read Code Guidelines, Workflow, Validation, and the affected files. Read component and feature docs only as needed; keep detailed rules in their owning documents rather than duplicating them here.

## 3. Module responsibilities

| Location                           | Responsibility                                                                            |
| ---------------------------------- | ----------------------------------------------------------------------------------------- |
| `src/pages/`                       | Page composition, business state, and page-private components, calculations, and styles   |
| `src/components/ui/`               | General UI primitives without new domain dependencies                                     |
| `src/components/observability/`    | Shared trace and metrics views with domain semantics                                      |
| `src/components/layout/`           | Shared workspace shell and navigation layout                                              |
| `src/router/`                      | Router setup; `routes.ts` is the shared definition for pages, navigation, and breadcrumbs |
| `src/data/`                        | Deterministic fixtures, including related error and comparison records                    |
| `src/types/observability.ts`       | Shared domain contracts such as Trace, Span, Model, and TraceStatus                       |
| `src/lib/format.ts`, `src/styles/` | Shared display formatting; semantic tokens and global styles                              |

- Keep page-private code beside its page, as with `ErrorAnalysis/analysis.ts` and `TraceCompare/comparison.ts`. Shared modules must not import page-private modules.
- `StatusBadge` currently lives in `ui/` and imports `TraceStatus`; this existing exception does not authorize more domain dependencies or an unrelated move.

## 4. Reuse and abstraction

- Check existing primitives, domain components, formatting helpers, and their callers before adding equivalents. Reuse contracts, not just similar markup.
- Preserve ownership: `TraceTable` renders supplied rows and reports selection IDs; its page owns filtering, pagination, selection fallback, and details.
- Extract shared components or helpers when at least two real uses share a behavior contract and a small interface reduces maintenance. Keep one-page reuse local; do not merge different chart or table semantics behind many switches.
- There are currently no custom Hooks. Extract a Hook for reusable stateful or effectful behavior when needed; keep pure calculations as plain functions. Do not create a shared Hooks or service layer for hypothetical use.

## 5. State and data flow

- The app currently imports fixtures directly and uses local React state, with no global state store or network request layer. Follow this model unless the task requires a change; justify new dependencies or data infrastructure.
- Keep filters and selections with the owning page, and transient popup state with the picker. Pass controlled values and callbacks to children. Derive visible rows, selected records, and summaries instead of storing duplicate results.
- Preserve each page's filter, sort, pagination, and selection rules; details must correspond to visible results, including empty results. Do not generalize Trace Explorer's five-row limit to other tables.
- Trace Explorer's `trace` query parameter supports deep links. Preserve refresh and browser history behavior, invalid-ID fallback, and unrelated query parameters when changing URL synchronization.
- Use effects for external synchronization, not pure data derivation. Respect Hooks ordering and dependency lint rules; clean up listeners as `TracePicker` does. Effects must tolerate StrictMode's repeated setup and cleanup.
- If a task introduces real requests, define the data-access owner, validate responses at the boundary, and handle loading, failure, retry, and stale responses or cancellation. Do not present a new request layer as existing architecture.

## 6. TypeScript contracts

- Preserve the existing strict compiler options and Hooks lint checks. Use `import type` for type-only imports and reuse shared domain types instead of parallel definitions; keep page-private types local.
- Use specific unions for finite choices and props, following `TraceStatus` and `MetricCard`'s mutually exclusive comparison/note props. Do not hide uncertain data with `any`, unchecked assertions, or non-null assertions; narrow or validate it.
- Keep calculations independent of React and explicit about inputs, units, missing values, and zero denominators. Use `readonly` inputs when mutation is not intended; copy before sorting caller-owned arrays or fixtures.

## 7. Performance

- Keep pure fixture-only work outside rendering when it does not depend on props or state, as in Error Analysis. Derive state-dependent results during rendering; consider `useMemo` for costly repeated calculations, as in LLM Metrics.
- Do not blanket-add `memo`, `useMemo`, or `useCallback`. Tie performance changes to an observed cost and verify the affected interaction; correctness must not depend on memoization. Do not claim speedups without measurements.

## 8. Change policy

- **Accessibility**: retain labels, keyboard access, and visible focus states.
- **Data contracts**: never mutate mock data or alter service contracts merely to fix visual problems.
- **Do not regenerate unrelated code**, introduce dependencies without a reason, or overwrite visual snapshots silently.
- **Never convert an unimplemented feature into a fake functioning button**. Mark it static or implement actual behavior.

## 9. Verification and delivery

- Follow Workflow and Validation for check scope, baseline approval, and delivery. After Markdown-only edits, run `npm run docs:check` and confirm descriptions match code; runtime checks do not apply solely because instructions mention code.
- Behavior acceptance lives in `tests/behavior.spec.ts`, including direct tests of calculation modules. For behavior changes, cover the relevant normal path and edge cases, such as empty data, zero baselines, stable ordering, input immutability, and deep links. Reuse test helpers when their contract matches.
- Follow Validation for typecheck, build, lint, formatting, browser, and visual checks. Shared runtime or style changes require coverage across affected pages. `npm run check` includes production-preview behavior tests but not standardized visual regression; the latter uses `npm run test:visual:docker`.
- Preserve the separate shared-chrome, page-content, and Portal screenshot boundaries. Inspect intended differences and obtain baseline approval before treating new screenshots as acceptance evidence or committing them.
- Report changed files, reasons, commands actually run, results, and verification gaps. Distinguish skipped checks, environment blockers, and pending review from passing checks.

## 10. No invented metrics

All traces, requests, performance stats and costs in this repo are fixtures. Do not claim they are production observability measurements. Preserve statistical meaning: do not average model P95 values into an overall P95; the current fixtures lack request-level samples or a mergeable histogram.

## 11. Optional MCP tools

Figma MCP is NOT required for this repository. Browser Playwright automation is useful for inspection; prefer accessibility-based selectors for interactions and screenshots for visual diagnosis. If MCP tools are unavailable, rely on the included Playwright tests and explain the limitation.
