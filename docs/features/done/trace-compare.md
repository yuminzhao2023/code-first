# Trace Compare

The implemented `/compare` page places two runs of the same operation side by side so developers can inspect duration, token use, and the steps behind a difference. All values are fixtures.

## At a glance

- **Deliberate selection:** A baseline determines which comparison runs are eligible; changing or clearing it never leaves stale results.
- **Explicit calculations:** Differences use the original trace totals, and a zero baseline never produces a misleading percentage.
- **Stable alignment:** Steps pair by type, name, and occurrence order, without claiming to reconstruct call relationships.

[TraceCompare.tsx](../../../src/pages/TraceCompare/TraceCompare.tsx) owns the page state; [comparison.ts](../../../src/pages/TraceCompare/comparison.ts) contains the independent calculations. The rules and behavior checklist below document the current implementation.

## Page and entry point

Visible text, placeholders, and accessible names use English throughout the page. Example labels below describe their meaning and may differ from the exact UI copy.

- The OBSERVE section of the sidebar links to `/compare` as **Trace Compare**.
- The page is titled Trace Compare, marks its data as fixed samples, and includes run selection, overall metrics, and step comparison sections.
- Direct visits and refreshes work, and navigation indicates the current page.
- Selection stays in page state rather than the URL. A refresh or new visit starts with neither run selected.

## Choosing runs

- The baseline and comparison pickers have explicit labels and start empty.
- Baseline options come from the full trace dataset in source order, showing operation, Trace ID, and status.
- The comparison picker is disabled with an explanation until a baseline is chosen.
- Comparison options have exactly the same `operation` as the baseline and a different ID. They retain source order; service, model, and status may differ.
- Choosing a baseline does not select a comparison automatically. When no candidates exist, the comparison picker stays disabled and explains why.
- Changing the baseline retains a comparison only if it remains eligible; otherwise, it clears the comparison.
- "Clear selection" in either popup clears that picker. Clearing the baseline also clears the comparison, while clearing the comparison leaves the baseline intact.
- "Swap runs" is available only when both choices are valid and recalculates every difference.
- Until both runs are selected, the metrics and steps sections show guidance instead of stale results.

### Picker display and keyboard behavior

- Search matches operation names or Trace IDs, ignoring case and surrounding whitespace. It filters visible candidates without changing the selected run.
- The popup starts at the input's width, stays inside the viewport, and is at most 320px tall. It shrinks or opens upward when needed, with internal scrolling for options.
- Options distinguish operation, Trace ID, and status. Long text wraps without widening the popup; full information remains available in run details and reopened options.
- The arrow sits 16px from the right edge with space reserved for text. Clicking anywhere in the input opens the picker.
- Arrow keys move the active option; Enter selects; Escape cancels and closes; Tab closes and continues navigation. Opening the picker activates the current choice and scrolls it into view. An empty search result is explained and still permits clearing.

## Overall metrics

The page shows both runs side by side with operation, Trace ID, service, model, status, total duration, and token count. Status appears in both text and color without an automatic interpretation.

It shows absolute and percentage changes for total duration and tokens:

| Case                 | Rule                                                                                                                                |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| Absolute change      | Comparison value minus baseline value                                                                                               |
| Percentage change    | (Comparison value minus baseline value) ÷ baseline value × 100%                                                                     |
| Zero baseline        | Show "not available" for the percentage, even when both values are zero; still show the absolute change                             |
| No change            | Show "no change" and, for a nonzero baseline, 0.0%                                                                                  |
| Increase or decrease | State the direction and magnitude in words; color or arrows alone are insufficient                                                  |
| Precision            | Show duration in ms, tokens as integers, and percentages to one decimal place. Calculate from raw values and format only at the end |

Direction comes from the raw difference. A nonzero percentage that would round to 0.0% appears as "less than 0.1%" while retaining its direction.

A longer duration is not automatically an error, nor are fewer tokens automatically a quality improvement. Total trace duration comes from `durationMs`; spans can overlap, so their durations are not summed to infer the total.

## Aligning steps

### Matching rules

- Spans match exactly by `kind` and `label`, not by span ID, `depth`, or duration.
- Repeated matches pair in their original `spans` array order without extra sorting.
- Output follows baseline order for baseline steps and their matches, then comparison order for unmatched comparison steps.
- Alignment does not mutate either input array or the original fixtures.
- The page explains that alignment uses type, name, and occurrence order rather than inferred call relationships.

For example, with baseline `A₁, B, A₂` and comparison `A₁, A₂, C`, where both A steps share a type and name, the output is `A₁ matched, B removed, A₂ matched, C added`. The subscripts only explain occurrence order; they are not match keys.

### Display rules

| Field               | Content                                                                                                                                              |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| Step                | Full name and type; number repeated steps with the same name so readers can distinguish them                                                         |
| Baseline duration   | The step's `durationMs`, or `—` if absent                                                                                                            |
| Comparison duration | The step's `durationMs`, or `—` if absent                                                                                                            |
| Duration change     | For matched steps, comparison duration minus baseline duration, using the same directional wording as overall metrics and units of ms; no percentage |
| Alignment status    | Matched, added (comparison only), or removed (baseline only)                                                                                         |

An added or removed step has no duration change; show `—` rather than treating the missing side as zero. If one run has no steps, show every step from the other run. If both have none, explain that there are no steps to compare while still showing overall metrics.

## Links to traces

- Each valid choice has a real "View baseline Trace" or "View comparison Trace" link to `/?trace=<traceId>`.
- Trace Explorer opens the corresponding record with synchronized details and waterfall, including on direct visits and after refresh.
- Every comparison fixture also exists in Trace Explorer's dataset, so these links can locate their runs.
- Trace Explorer has no "compare" entry point, and comparison selections do not survive cross-page navigation.

## Layout and accessibility

- The page follows the existing dark UI and reuses AppShell, Card, Button, StatusBadge, and semantic tokens. The domain-specific picker stays local rather than expanding a shared component API for one page.
- At the 1440×900 desktop reference size, the runs sit side by side with a vertical divider and padding. At 390×844 on mobile, baseline stacks above comparison with a horizontal divider.
- The step table scrolls horizontally within its container without causing page-level overflow. The layout also adapts between the reference sizes.
- Long operation names, Trace IDs, and step names wrap or scroll locally so essential information remains available.
- Inputs have explicit labels; all actions support keyboard access and visible focus. Unavailable actions use real disabled attributes with an explanation.
- Empty results, incomplete selections, and runs without steps have text explanations instead of blank panels.

## Data and scope

- [Comparison fixtures](../../../src/data/comparisons.ts) use the existing Trace and Span types and join the common trace dataset. Existing fixture fields, values, and meanings remain unchanged.
- The fixtures cover three runs of one operation, another operation, a run without a same-operation candidate, different models or statuses, repeated same-name steps, same-name steps of different types, added and removed steps, zero duration or tokens, empty steps, and long names.
- IDs are globally unique and numeric values are valid and nonnegative. The examples support checking both the baseline direction and the direction after swapping.
- Data and metrics are fixed examples, not production measurements or performance benchmarks.
- The page has no backend, asynchronous loading, live updates, cost estimation, automated root-cause analysis, export, new dependencies, or general-purpose chart system.

## Behavior checklist

| Scenario                                     | Observable result                                                                                                        |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| First visit or refresh                       | Neither run is selected; comparison and swap are disabled; comparison sections show selection guidance                   |
| Choose a baseline                            | Comparison offers only runs of the same operation with different IDs; an empty candidate set is explained                |
| Change or clear a selection                  | Keep valid comparisons, clear invalid ones, and remove stale metrics and steps                                           |
| Baseline 1000 ms, comparison 1500 ms         | Duration increases by 500 ms and 50.0%                                                                                   |
| Swap those runs                              | Duration decreases by 500 ms and 33.3%; added and removed step directions reverse                                        |
| Baseline tokens are zero                     | Absolute change is correct and percentage is unavailable, including when both values are zero                            |
| Both values are equal and nonzero            | Show no change and 0.0%                                                                                                  |
| Very small nonzero percentage                | Keep its direction and show a magnitude of less than 0.1%, not no change                                                 |
| Repeated names or the same name across types | Match by type, name, and occurrence in a stable order                                                                    |
| A step exists on one side only               | Mark it added or removed; show `—` for the missing value and change                                                      |
| One or both runs have no steps               | Mark all one-sided steps added or removed; show an empty-step message for two empty runs while retaining overall metrics |
| Open either trace                            | Show its details and waterfall in Trace Explorer, including after refresh                                                |
| Keyboard and mobile                          | Selection, swap, clear, and trace links work; focus is visible and the page does not overflow horizontally               |
| Existing pages                               | Navigation, search, filters, pagination, selection, and existing trace deep links keep working                           |

## Implementation boundaries

- [TracePicker](../../../src/pages/TraceCompare/TracePicker.tsx) is a page-private domain component that depends on the Trace model and its status display. Both pickers on this page reuse it. It stays beside the page until a cross-page use case justifies moving it into shared domain components; it is not a general UI primitive.
- The page owns selected IDs and available trace collections. TracePicker owns only its popup, search, and active option. It calls `onChange` when the user selects a run and passes an empty string when cleared. Searching, canceling, or closing the popup does not change selection. See the component source for prop types.
- The popup mounts through a Portal under `document.body` to avoid card clipping. Its styles remain in the page's [TraceCompare.css](../../../src/pages/TraceCompare/TraceCompare.css), so selectors must work without a page ancestor.
- [comparison.ts](../../../src/pages/TraceCompare/comparison.ts) calculates differences and pairs steps without owning page interaction state.

## Verification references

- [Behavior tests](../../../tests/behavior.spec.ts) cover calculation boundaries, stable pairing without input mutation, picker selection, swapping, clearing, empty states, and trace links.
- [Visual tests](../../../tests/visual.spec.ts) define desktop and mobile page and picker screenshots. The [validation guide](../../VALIDATION.md) explains how to compare and approve baselines; this feature document does not assert that a particular test run passed.
