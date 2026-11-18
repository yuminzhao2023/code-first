# Code Guidelines

## 1. Module boundaries and dependencies

| Location                        | Responsibility                                                                          |
| ------------------------------- | --------------------------------------------------------------------------------------- |
| `src/pages/`                    | Page composition, business state, and page-private components, calculations, and styles |
| `src/components/ui/`            | General UI primitives without new domain dependencies                                   |
| `src/components/observability/` | Shared trace and metrics views with domain semantics                                    |
| `src/components/layout/`        | Shared workspace shell and navigation layout                                            |
| `src/router/`                   | Router setup; `routes.ts` drives page registration, navigation, and breadcrumbs         |
| `src/data/`                     | Deterministic fixtures, including related error and comparison records                  |
| `src/types/observability.ts`    | Shared domain contracts such as Trace, Span, Model, and TraceStatus                     |
| `src/lib/format.ts`             | Shared display formatting                                                               |
| `src/styles/`                   | Semantic tokens and global styles; page-private styles stay beside their page           |

- Keep code that changes together close together, as with `ErrorAnalysis/analysis.ts` and `TraceCompare/comparison.ts`. Shared modules must not import page-private modules; avoid circular dependencies.
- `StatusBadge` currently lives in `ui/` and imports the domain-specific `TraceStatus` union. This existing exception does not authorize more domain dependencies or an unrelated move.
- When changing module boundaries, update imports, tests, and related documentation together.

## 2. Responsibilities and reuse

- Check existing components, helpers, and callers before adding equivalents. Extract shared behavior when real uses share a contract and a small interface reduces maintenance; keep one-page reuse local. Do not unify different charts or tables behind many business-case switches.
- Move complex calculations into plain functions with explicit inputs and results, independent of React and runtime fixture imports, as in `analysis.ts` and `comparison.ts`. Keep data access and other effects clearly owned.
- Extract a custom Hook for reusable stateful or effectful behavior when needed; pure calculations remain plain functions. Do not create a Hooks, service, or global state layer for hypothetical use.
- Do not mutate caller-owned data or fixtures; use `readonly` inputs where appropriate and copy arrays before sorting them.

## 3. Types, data, and state

- Reuse shared domain types and keep private types local. Use specific unions for finite choices and mutually exclusive props, as in `TraceStatus` and `MetricCard`. Do not hide uncertain data with `any` or unchecked assertions; narrow or validate it.
- TypeScript, ESLint, and Prettier configuration own compiler, Hooks, and formatting rules. Preserve those checks; use `import type` for type-only imports under `verbatimModuleSyntax`.
- Validate external data at the system boundary, and define how missing, invalid, and failed inputs are handled.
- Keep filters and selections with their page, transient popup state with its picker, and pass controlled values and callbacks to children. Derive visible rows, selected records, and summaries instead of storing duplicate results.
- Preserve each page's filter, sorting, pagination, selection, and URL/history behavior, including empty results. Use the relevant feature/component document and acceptance tests for page-specific rules; do not generalize one page's row limit to every table.
- Use effects for external synchronization rather than pure derivation; clean up listeners and support StrictMode's repeated setup/cleanup. If a task introduces requests, define ownership, response validation, loading, failure, retry, and stale-response or cancellation handling.
- Keep units, missing values, and zero denominators explicit in calculations. Model P95 values cannot be averaged into an overall P95; current fixtures lack request-level samples or a mergeable histogram.

### Performance

- Pure work on fixed fixtures can run outside rendering when it does not depend on props or state, as in Error Analysis. Derive state-dependent results during rendering; consider memoization for costly repeated work.
- Do not blanket-add `memo`, `useMemo`, or `useCallback`. Tie optimizations to an observed cost, verify the affected interaction, and measure claimed speedups. Correctness must not depend on memoization.

## 4. Naming, formatting, and styles

- Follow surrounding naming conventions and let Prettier handle mechanical formatting. Keep functions and files focused; use local helpers to clarify complex expressions.
- Scope local selectors so they cannot affect unrelated pages. Styles for Portal content must also work outside the page's DOM ancestor.

## 5. Comments and interface documentation

Names, types, and structure should explain the code first. Document the following near the implementation when the code alone cannot make them clear:

- Business rules, edge cases, and invariants that must be preserved.
- Data formats, units, sources, and assumptions that affect correctness.
- Unusual algorithms, tradeoffs, and the reasons for them.
- Constraints around state synchronization, side effects, and resource lifetimes.
- Why a compatibility workaround or temporary solution exists, when it applies, and when it can be removed. Link a related issue when one exists.

For interfaces used by other modules whose behavior is not obvious, use JSDoc to describe input constraints, return behavior, errors, and side effects. Do not repeat information already conveyed by the types.

Keep comments current with the implementation and focus on reasons and constraints. Avoid line-by-line narration, empty file headers, and stale explanations. Keep detailed requirements and design decisions in their relevant documents, with a code reference when useful.

## 6. Documentation and references

- Use lowercase kebab-case Markdown filenames throughout `docs/` (for example, `code-guidelines.md`). Files under `docs/components/` retain the matching component names (for example, `Button.md`).
- Keep standards in the `docs/` root and component and feature guides in their respective subdirectories. Maintain each rule in one place; link to it at the step where it is needed rather than copying it or repeating a list of standards.
- AGENTS is the required entry point for AI agents. Standards must not direct readers back to AGENTS or end with a generic related-standards list.
- Reading requirements between standards must flow in one direction. Use explicit Markdown links for required dependencies, not plain text or inline code. Before adding a link, confirm that the destination supplies a rule needed at that step and does not lead back to the current document. Do not require material that is already in context.
- The README is navigation for people and may list standards and component docs. A navigation, source, or example link does not itself require readers to follow every linked document.
- Read component and feature docs as the task requires. Keep useful links to types, implementations, and specific examples without turning the entire docs tree into a required reading list.
- Use repository-relative links. A standard must not link to the same file more than once; refer to an already linked standard by name if needed.
- Run `npm run docs:check` after adding, moving, or editing Markdown. It checks local targets used by Markdown links, duplicate file links from standards, links from standards back to the entry point, and cycles between standards. For images, it only checks that local targets exist. Navigation and examples are not execution dependencies, but direct links between standard documents are checked as dependencies; do not use them for navigation alone.

The checker parses CommonMark. It does not inspect code examples, unused reference definitions, raw HTML, anchors, or the availability of external links. It also cannot interpret reading instructions in prose or decide whether a link is necessary. Run `npm run test:docs` after changing the checker; prose reading requirements and link necessity still need human review.

## 7. Changes and review

- Keep changes within the task's scope; avoid unrelated refactors or whole-file formatting changes.
- When behavior changes, check callers, tests, and documentation together, including the normal path and relevant edge cases.
- Add a dependency only for a clear purpose and only if it works in the project's runtime environment.
- Review correctness, readability, module boundaries, and verification evidence. Comments cannot replace clear code or necessary tests.
