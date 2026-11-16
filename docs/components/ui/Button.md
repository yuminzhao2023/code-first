# Button

Use Button for actions such as resetting filters or changing pages. Use links for navigation. The component preserves native button semantics; callers own business state and asynchronous requests.

## Variants and example

- `primary`: The main action in the current area.
- `secondary`: A supporting action and the default variant.
- `ghost`: An action with less visual emphasis.

The caller supplies real reset and pagination handlers in this example:

```tsx
import { Button } from '../../../src/components/ui/Button';
import { Icon } from '../../../src/components/ui/Icon';

export function ButtonExample({
  onReset,
  onPrevious,
  isFirstPage,
}: {
  onReset: () => void;
  onPrevious: () => void;
  isFirstPage: boolean;
}) {
  return (
    <>
      <Button variant="primary" onClick={onReset}>
        Reset filters
      </Button>
      <Button disabled={isFirstPage} onClick={onPrevious}>
        Previous
      </Button>
      <Button variant="ghost" aria-label="Reset filters" onClick={onReset}>
        <Icon name="filter" />
      </Button>
    </>
  );
}
```

## Contract and states

See [Button.tsx](../../../src/components/ui/Button.tsx) for all props and defaults. The default is `type="button"`; set `type="submit"` explicitly for form submission. Native events and ARIA attributes pass through.

| State          | Usage and check                                                                              |
| -------------- | -------------------------------------------------------------------------------------------- |
| Default        | Label the action clearly and use the variant to show its priority                            |
| Hover          | The primary button has a hover style; do not assume every variant has a distinct hover color |
| Keyboard focus | Tab shows the shared focus ring; Enter or Space activates the button                         |
| Disabled       | Use `disabled` so the browser prevents activation and removes it from the normal Tab order   |
| Icon only      | Provide an `aria-label`; a decorative Icon does not name the button                          |

There is no built-in loading state or duplicate-request prevention. If an action becomes asynchronous, the caller must manage it and define the feedback.
