# TraceTable

TraceTable displays trace records and tells the page which record the user selected. It depends on trace data and status semantics, so it is a domain component rather than a general-purpose table.

## Controlled selection example

```tsx
import { useState } from 'react';
import type { Trace } from '../../../src/types/observability';
import { TraceTable } from '../../../src/components/observability/TraceTable';

export function TraceTableExample({ rows }: { rows: Trace[] }) {
  const [selectedId, setSelectedId] = useState('');
  const selected = rows.find((trace) => trace.id === selectedId) ?? rows[0];
  return (
    <TraceTable
      rows={rows}
      selectedId={selected?.id ?? ''}
      onSelect={setSelectedId}
    />
  );
}
```

The parent passes either a valid selection or the first row to the table. The actual page also uses that same `selected` record for its detail view; see [TraceExplorer.tsx](../../../src/pages/TraceExplorer.tsx).

## Responsibilities and contract

See [TraceTable.tsx](../../../src/components/observability/TraceTable.tsx) for the complete props.

| TraceTable                                                   | Page                                                                 |
| ------------------------------------------------------------ | -------------------------------------------------------------------- |
| Renders records in the order received                        | Fetches, filters, sorts, and paginates the data                      |
| Marks the selected row with `selectedId` and `aria-pressed`  | Owns selection and finds a valid selection when results change       |
| Calls `onSelect(id)` when the row's action button is clicked | Updates selection, details, and related URL state                    |
| Shows an empty-result message for an empty array             | Keeps details in sync with visible records and handles empty details |
| Scrolls horizontally within its container                    | Prevents the outer layout from widening the viewport                 |

- Each record must have a unique `id`. The component does not mutate its input.
- If `selectedId` matches no record, no row is highlighted and no callback fires automatically.
- Timestamps and durations follow the existing Trace contract and formatting helpers. The component does not guess time zones or rewrite data.
- Trace Explorer limits pages to five records. TraceTable renders every record it receives and has no built-in row limit.
