# MetricCard

MetricCard summarizes a metric with a label, formatted value, and icon. It can show a comparison with the previous period or a plain note. It does not calculate metrics or provide click and filter behavior.

## Example and metric meaning

```tsx
import { MetricCard } from '../../../src/components/ui/MetricCard';
import { Icon } from '../../../src/components/ui/Icon';

export function MetricCardExample() {
  return (
    <MetricCard
      label="Avg. Duration"
      value="1.84s"
      delta="-8.2%"
      tone="good"
      icon={<Icon name="clock" size={19} />}
    />
  );
}
```

This is a fixed example, not a performance measurement. A shorter duration may be an improvement, so a negative delta can use `good`; a higher error rate would use `bad`. The caller chooses `tone` based on what the metric means. MetricCard does not interpret the delta string.

## Contract and states

See [MetricCard.tsx](../../../src/components/ui/MetricCard.tsx) for all props and defaults.

- `value` and `delta` are display strings supplied by the caller, which also decides their units, precision, and sign.
- `tone` is `good`, `neutral`, or `bad` and defaults to `good`. The current `neutral` style uses the warning color; it does not mean "no change" was detected automatically.
- For a previous-period comparison, pass `delta` and optionally `tone`. The comparison label is fixed as `vs. previous period`.
- Without a previous-period comparison, pass `note` and do not pass `delta` or `tone`. For example, if overall P95 lacks request-level data, use `value="—"` and `note="Requires request-level data"` rather than inventing a value or change.
- The card is not interactive and should not add an unnecessary Tab stop.
- Values stay on one line and long values may overflow. Format them sensibly for the metric's units rather than hiding important values with overflow clipping. Check long labels and localized content separately.
