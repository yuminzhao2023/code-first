# UI Guidelines — PulseTrace Lab

These guidelines define visual, interaction, and UI reuse rules shared across pages for AI agents and contributors. Page-specific behavior belongs in the relevant page, tests, and `docs/features/`.

## 1. Scope and design goals

- **Audience:** Developers debugging LLM and agent executions.
- **Information density:** Support dense data exploration without sacrificing the readability of rows, labels, or actions.
- **Visual direction:** A dark developer-tool interface with a consistent accent palette and semantically consistent status colors.
- **Source of truth:** Use [tokens.css](../src/styles/tokens.css) for shared visual values. Refer to existing UI components and accepted pages for component usage. Figma is optional.
- **Scope of these rules:** These are requirements, not a claim that every existing page already meets them. Known exceptions are called out below; they do not automatically authorize broader refactoring.

## 2. Visual foundations

### Tokens and color

| Token                                                              | Use                                                         | Avoid                                                |
| ------------------------------------------------------------------ | ----------------------------------------------------------- | ---------------------------------------------------- |
| `--color-bg` / `--color-sidebar`                                   | Page canvas and navigation backgrounds                      | Creating a separate background theme for one page    |
| `--color-surface` / `--color-surface-raised`                       | Panels and layered surfaces                                 | Adding arbitrary panel colors at the same level      |
| `--color-surface-hover` / `--color-surface-selected`               | Hover and selected states                                   | Treating hover as a persistent selection             |
| `--color-text` / `--color-text-muted` / `--color-text-faint`       | Primary content, secondary information, and supporting text | Using faint text for essential content               |
| `--color-accent` and its variants                                  | Current navigation, primary actions, and emphasis           | Using the accent color to signal errors              |
| `--color-success` / `--color-warning` / `--color-danger`           | Success, warning, and error states                          | Conveying status through color alone                 |
| `--color-chart-agent` / `--color-chart-llm` / `--color-chart-tool` | Distinguishing span types                                   | Changing type colors between views                   |
| `--color-border` / `--color-border-soft`                           | Boundaries and content separators                           | Giving equivalent boundaries different colors        |
| `--radius-card` / `--shadow-card`                                  | Shared panel appearance                                     | Overriding every card's radius or shadow on one page |
| `--space-*`                                                        | Reusable spacing scale                                      | Choosing inconsistent spacing for the same purpose   |
| `--focus-ring`                                                     | Visible keyboard focus                                      | Removing the focus indicator without a replacement   |

Prefer existing tokens for new styles. `app.css` still contains literal colors and spacing values; that is an existing inconsistency, not a request to migrate them all during every task. One-off values do not all need new tokens.

### Typography and data display

- Use `--font-ui` for body text and controls. Use `--font-mono` or the existing `.mono` class when technical identifiers such as IDs and span labels need monospaced text.
- Preserve the hierarchy of page titles, section headings, card titles, body text, and supporting text. Reuse existing styles instead of duplicating font-size values here.
- Left-align table names and descriptions; keep numeric columns right-aligned as in existing tables. Use tabular numerals for values that readers compare, and show units and precision clearly.
- Reuse the existing `Icon` component. Decorative icons do not replace text labels, and icon-only actions still need accessible names.

## 3. Layout and responsiveness

- Desktop 1440×900 and mobile 390×844 are reference sizes for checking, not fixed canvases. Layouts should also adapt at widths between them.
- Reuse the layouts for AppShell, page headers, metric grids, and cards. Let action areas wrap as the viewport narrows, and reduce multi-column layouts at existing breakpoints.
- Wide tables and waterfalls may scroll horizontally inside their own containers; they must not widen the entire page. Keep navigation and primary actions available on mobile.
- Wrap long text or allow local scrolling as appropriate. If text is truncated, provide a way to read it in full; do not simply hide key identifiers or errors.

## 4. Shared interactions and states

### Actions and inputs

- Use real links for navigation and cross-page jumps, and buttons for selections and actions. Do not rely on a clickable `tr` alone.
- Use primary buttons for prominent actions and lower visual weight for secondary actions. Labels should name the action.
- Express status through both text and color, using the same semantic mapping throughout.
- Keep explicit labels on search and filters. Existing React forms use controlled inputs; placeholders are not labels.

Component-specific purposes, examples, and limits belong in `docs/components/`. Keep page-private component behavior with the relevant feature document. Read these docs when the task calls for them, and update them when changing a component contract. The component source defines its API types; do not duplicate prop tables in these general guidelines.

### States and feedback

- Hover, keyboard focus, selection, and disabled are distinct states. Preserve their meanings and reuse existing styles, including `aria-pressed` on selection buttons, `aria-current` on navigation, and `aria-sort` on sortable table headers.
- Make every action keyboard accessible and keep focus visible. Use the actual disabled attribute for unavailable actions rather than changing appearance alone.
- Explain empty results. Keep details synchronized with the visible results and show an empty-detail message when no record can be selected.
- Pagination, filtering, sorting, and selection interact according to each page's behavior contract. Do not apply one page's row limit to every table.
- Define loading, request failure, and retry behavior when asynchronous data is actually introduced. Do not add fake interactions for features that are not implemented.

## 5. Component ownership and extension

| Location                        | Responsibility                                                                           |
| ------------------------------- | ---------------------------------------------------------------------------------------- |
| `src/components/ui/`            | General UI primitives; new components must not depend on domain models                   |
| `src/components/layout/`        | Shared workspace and navigation layout                                                   |
| `src/components/observability/` | Domain views such as TraceTable, TraceWaterfall, and LatencyChart                        |
| `src/pages/`                    | Page composition, data selection, business state, and page-private components and assets |
| `src/styles/`                   | Shared tokens and global or base styles                                                  |

Page-specific components may live alongside their page; using React does not require moving them to `components/`. Reuse within one page is still local reuse. When the same need appears on multiple pages, assess the behavior contract and maintenance cost: put shared domain components in `components/observability/` and domain-independent primitives in `components/ui/`. Shared components must not import page-private modules.

**Existing exception:** `StatusBadge` lives in `ui/` but depends on the domain-specific `TraceStatus` enum. Keeping it there does not justify adding similar dependencies. Move it only as part of a clearly scoped refactor.

Extract shared UI components only when all of the following apply:

1. At least two use cases share the same behavior and visual contract.
2. The interface can stay small and stable without many switches for different business cases.
3. Extraction reduces maintenance work rather than creating a general abstraction for one case.

Otherwise, keep the implementation local. TraceWaterfall and LatencyChart, for example, have different data semantics and should not be merged simply because both draw charts.

New tokens should represent reusable visual meaning and have multiple real uses; keep one-off needs in local styles. When a shared rule changes, explain why and check every affected page, not just the first two. Update these guidelines only when a stable, reusable rule emerges.
