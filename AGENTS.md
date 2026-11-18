# Agent instructions — PulseTrace Lab

PulseTrace is a React 19, TypeScript, React Router, and Vite observability UI lab. All traces, requests, metrics, and costs are deterministic fixtures. The app currently uses local React state and direct fixture imports, with no backend or network request layer.

Use Node.js 22.13+ within 22.x, or 24+, and npm. Install with `npm ci`; start locally with `npm run dev`.

## Global constraints

- Keep changes within the requested scope and preserve existing working-tree changes. Add dependencies or infrastructure only for a concrete requirement.
- Reuse existing components, helpers, and semantic tokens before adding equivalents. Keep page-private logic local and general UI primitives independent of domain models.
- Preserve data contracts and fixture values when fixing visuals. Do not present sample statistics as production measurements or invent unsupported aggregates such as an overall P95 from model P95 values.
- Preserve labels, keyboard operation, visible focus, and explicit empty states. Unimplemented features must remain static or unavailable until real behavior exists.
- Do not disable checks, relax screenshot thresholds, or replace baselines just to hide regressions. New visual baselines require review and approval under Validation.

## Read according to the task

Start with the affected implementation, callers, and relevant tests. Read only the guidance needed for the change; follow required dependencies without rereading material already in context.

| Task                                                                              | Read                                                                                                                       |
| --------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| Change source code or technical documentation                                     | [Code Guidelines](docs/code-guidelines.md): ownership, reuse, types, state, performance, and documentation rules           |
| Change page layout, styles, or interactions                                       | [UI Guidelines](docs/ui-guidelines.md), `src/styles/tokens.css`, and the affected UI/domain components                     |
| Change a documented component contract                                            | The matching document in [component contracts](docs/components/) and its callers; otherwise use the component source       |
| Implement a feature or resume phase work                                          | [Roadmap](docs/plan.md), [build log](docs/features/build-log.md), and the relevant phase file under `docs/features/build/` |
| Execute staged work or make a durable handoff, including changes to this workflow | [Workflow](docs/workflow.md): execution, review, and build-log maintenance                                                 |
| Select checks, change tests/validation, or update baselines                       | [Validation](docs/validation.md), relevant acceptance tests, and affected scripts/configuration                            |
| Change hosting or deployment                                                      | [Deployment](docs/deployment.md) and the checked-in hosting configuration                                                  |

## Verification and delivery

- Choose checks by impact using Validation. Markdown-only changes require `npm run docs:check` and confirmation that descriptions match the implementation; do not run unrelated runtime tests solely because documentation mentions code.
- `npm run check` runs the quality checks and production-preview behavior suite. Standardized visual regression is separate: `npm run test:visual:docker`. Individual scripts and filtering are documented in Validation.
- Report changed files, reasons, exact commands actually run, outcomes, and remaining gaps or required review. Passing local behavior tests does not establish visual approval or deployment success.
- Keep material progress and verification evidence in the build log under Workflow; keep goals and dependencies in the roadmap and behavior contracts in phase/component documents.
