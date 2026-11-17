# PulseTrace · Agent Observability UI Lab

PulseTrace is a React and TypeScript demo for investigating AI agent runs through trace inspection, model metrics, error analysis, and run comparison. It also demonstrates how a project-specific AI development harness helps AI generate visually consistent pages and reuse existing components. That harness combines [agent instructions](AGENTS.md), [UI guidelines](docs/ui-guidelines.md), [design tokens](src/styles/tokens.css), [component contracts](docs/components/), and [tests](tests/).

**[Live demo](https://code-first.vercel.app/)** · **Built with:** React 19, TypeScript, Vite, and Playwright

## Explore the app

| Page                                                   | What you can do                                                                                                        |
| ------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------- |
| [Trace Explorer](https://code-first.vercel.app/)       | Search, filter, and page through traces; inspect execution steps and details; open trace links that survive a refresh. |
| [LLM Metrics](https://code-first.vercel.app/metrics)   | View metrics by model, sort costs, and explore sample trend charts.                                                    |
| [Error Analysis](https://code-first.vercel.app/errors) | Review error counts by failed run, filter events, and jump to the related trace.                                       |
| [Trace Compare](https://code-first.vercel.app/compare) | Compare two runs of the same operation, including their metrics and matching steps.                                    |

For a closer look at the decisions behind two pages, see [Error Analysis](docs/features/done/error-analysis.md) and [Trace Compare](docs/features/done/trace-compare.md).

## Engineering highlights

- **Consistent UI:** [AppShell](src/components/layout/AppShell.tsx) provides shared navigation and layout; [UI primitives](src/components/ui/) and [observability components](src/components/observability/) keep controls and data views reusable.
- **Focused domain logic:** [Error aggregation](src/pages/ErrorAnalysis/analysis.ts) and [step alignment](src/pages/TraceCompare/comparison.ts) are separate from rendering and use the same [fixture data](src/data/) as the pages.
- **Verifiable behavior:** [Playwright behavior tests](tests/behavior.spec.ts) cover filtering, selection, deep links, and comparison edge cases; [visual tests](tests/visual.spec.ts) cover shared layout and page content.

## Run locally

Use Node.js 22.13+ within the 22.x line, or Node.js 24+. No environment variables are required.

```bash
npm ci
npm run dev
```

Open `http://127.0.0.1:5173`. To check the production build and behavior:

```bash
npx playwright install chromium
npm run check
```

`npm run check` runs documentation, lint, formatting, type, build, and behavior checks. For reproducible visual comparisons, install Docker and run `npm run test:visual:docker`. See the [validation guide](docs/validation.md) for screenshot baseline updates and review.

## Scope and further reading

All traces, requests, costs, and performance figures are **fixtures, not production observability data**.

See the [development workflow](docs/workflow.md), [UI guidelines](docs/ui-guidelines.md), [code guidelines](docs/code-guidelines.md), and [deployment guide](docs/deployment.md).

This is a portfolio project with no open source license. Contact the author before redistributing it or using it commercially.
