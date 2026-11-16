# Error Analysis

## At a glance

The implemented `/errors` page helps developers find frequent failures, narrow their scope, and inspect the runs behind them.

- **Accurate counts:** Multiple error events from one trace count as one failed run.
- **Connected views:** Search and filters update the overview, distribution, list, and details together.
- **Direct investigation:** Each related run links to its selected trace in Trace Explorer, including after a refresh.

The aggregation rules live in [analysis.ts](../../../src/pages/ErrorAnalysis/analysis.ts); [behavior tests](../../../tests/behavior.spec.ts) cover filtering, deduplication, selection, and trace links.

## Page layout

The OBSERVE section of the sidebar links to **Error Analysis**. The page contains:

| Area               | Content                                                                                      |
| ------------------ | -------------------------------------------------------------------------------------------- |
| Error overview     | Failed-run count, number of affected services, and the most common error type with its count |
| Search and filters | Keyword search, service and model filters, and reset                                         |
| Error distribution | Horizontal bars showing failed-run counts by service or model                                |
| Error list         | Error type, service, model, failure count, and most recent occurrence                        |
| Error details      | The selected error's message and related runs                                                |

The list and details sit side by side on desktop and stack on mobile.

## Interaction rules

### Search and filters

- Search matches the error type, message, service name, or Trace ID, ignoring case and surrounding whitespace.
- Service and model default to All and intersect with the search query. Their options always come from the full dataset.
- Every data area reflects the current filtered results.
- Reset restores all data, default sorting, and selection of the first group.

### Overview and distribution

- Failure totals count distinct failed runs, so multiple failed spans in one trace contribute only one failure.
- The overview counts distinct affected services and aggregates the most common error by type. Ties go to the type whose name sorts first.
- The distribution defaults to service and can switch to model. Bars show their counts in descending order, with names ascending on ties.
- Switching the distribution dimension preserves filters and the selected error.

### List and details

- Events are grouped by error type, service, and model. Each group's count and latest occurrence come from the currently matching events.
- Groups sort by descending count by default, with a toggle for ascending order. Count ties use the most recent occurrence, then the group key in ascending order.
- Clicking an error type selects its group. Details show the group information, the full message of its latest matching event, and the currently matching related runs.
- Related runs appear in reverse chronological order with time, operation, Trace ID, and a "View Trace" link.
- If a group contains multiple events for one trace, only the latest is kept. Tied timestamps use the message that sorts first; related runs with the same time sort by Trace ID so arrival order cannot affect the result.
- The first group is selected initially. Sorting preserves selection; if filtering removes it, the first remaining group becomes selected.
- With no matches, the page shows 0 failures and 0 affected services, "No exceptions" for the most common type, guidance to adjust or reset filters in the list, and no details.

### Opening a trace

- "View Trace" links to `/?trace=<traceId>`. Trace Explorer then shows only that trace in its table and selects it; the search field contains its ID, and the details and waterfall show the same run.
- Editing the search or resetting filters removes the `trace` parameter and restores ordinary search and filtering, while preserving other URL parameters.
- Direct visits and refreshes locate the trace. A missing or invalid parameter uses the original default selection.

## Data and scope

- Fixed error fixtures cover several services, models, error types, and repeated occurrences within a group.
- Every error event points to an existing trace with status `error`. Its service, model, and operation come from that trace; `warning` does not count as a failure.
- Each event has a type, message, and full timestamp with a time zone. The page shows a fixed time range without a time filter.
- The page analyzes failures and links to traces. It does not send alerts, retry runs, or repair failures.
