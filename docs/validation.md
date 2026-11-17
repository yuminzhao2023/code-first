# Validation Guide

This guide defines the checks and evidence required for each change. It is not a record of the project's current test status. Record the results of a particular run in its delivery report or PR so an old environment failure is not mistaken for a permanent limitation.

## 1. Choose checks by impact

Before finishing, decide whether the change affects runtime behavior, types, the build, or visuals. Attempt at least the checks in the relevant rows; combine rows for changes with multiple effects, and broaden coverage when the impact is unclear.

| Change                                                            | Checks to attempt                                                                                                  |
| ----------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Markdown, comments, or JSDoc only, with no type or runtime change | `npm run docs:check`; confirm the description matches the implementation; typecheck TSX examples in component docs |
| Types, component interfaces, or build configuration               | `npm run typecheck` and `npm run build`, plus checks below as needed for runtime or visual impact                  |
| Page behavior, business calculations, routing, or data handling   | Typecheck, build, and relevant behavior tests                                                                      |
| Layout, styles, copy, or visible interaction states               | Typecheck, build, and affected page behavior and visual tests; inspect pages as described in Section 2             |
| Shared tokens, runtime components, or global layout               | Typecheck, build, and full behavior and visual regression coverage across affected pages                           |
| Documentation checker or its rules                                | `npm run test:docs` and `npm run docs:check`                                                                       |
| Tests, validation configuration, or acceptance rules              | Run the changed or newly affected checks to confirm their commands and pass criteria work                          |

Full check commands:

```bash
npm run docs:check
npm run test:docs
npm run lint
npm run format:check
npm run typecheck
npm run build
npm run test:e2e
npm run test:visual:docker
```

`npm run check` runs documentation, lint, whole-repository formatting, and type checks, then builds and runs the full behavior suite against the production output. It does not require Docker. `npm test` runs only local behavior tests. To run behavior tests against the production output separately, use `npm run test:e2e:preview`.

Browser tests use the Chromium version bundled with Playwright. If the local OS does not support that version, run browser checks on a supported OS, in a compatible container, or in CI. If Docker is unavailable locally, leave the standardized visual comparison pending and complete it in CI or another Docker-capable environment. Passing local behavior tests does not count as passing the standardized visual comparison.

Formatting checks run `prettier --check .` and formatting runs `prettier --write .`. Both use `.prettierrc.json` and `.prettierignore`, regardless of Git history. Run `npm run lint` and `npm run format:check` for source, test, or configuration changes. Review the diff after `npm run format`; if formatting the whole repository for the first time, state that scope explicitly. Later feature changes should not include unrelated formatting.

You can narrow behavior tests with a command such as `npm run test:e2e -- --grep 'Trace Explorer'`. A filter that matches no tests is not a completed check. Use the full command for shared changes.

- Typecheck, build, behavior, and visual checks cover different risks. Parsing successfully does not prove type safety.
- To typecheck a TSX example in documentation, extract its code block into a temporary `.tsx` file beside the document so relative imports still work. Check it together with `src/` using a temporary configuration that extends `tsconfig.app.json`. Remove both temporary files afterward. Passing the typecheck does not mean the example was tested in a browser.
- Report the commands actually run, their scope, and why that scope was chosen. Mark checks omitted because they do not apply as "not run (not applicable)," not as passed.
- Preserve the original error when a check fails or the environment blocks it. Explain the cause without relaxing the requirements to conceal a failure.

## 2. Page and interaction checks

- Check relevant search, filter, pagination, selection, reset, routing, and empty-result states.
- For UI changes, inspect desktop at 1440×900 and mobile at 390×844, including labels, keyboard operation, visible focus, and page overflow.
- When shared tokens or components change, inspect every affected page rather than limiting checks to the first two pages.
- Report separately whether manual checks not covered by automation were completed.

### Screenshot boundaries

- Maintain separate desktop, compact-layout, and mobile screenshots for the shared navigation and header. Use a fixed route to show the same selected state. On desktop, capture navigation at a fixed viewport height so page length does not change its screenshot.
- Capture only each page's `.content-wrap` in its content baseline instead of repeating shared navigation and header in every page screenshot. Capture Portal overlays separately so tests do not depend on where they mount in the DOM.
- Splitting screenshots must not change the actual layout or hide regressions by concealing navigation, adding masks, or relaxing pixel thresholds. Behavior tests must still check the full page's selected navigation state, breadcrumbs, relative section positions, and horizontal overflow.
- A new navigation item usually needs review of only the shared navigation baseline. If it also changes content width, shared styles, or page data, the relevant content screenshots must still show those differences; do not suppress them to avoid failures.

## 3. Visual baselines and approval

`npm run test:visual` runs locally and can reveal screenshot differences on the current system. `npm run test:visual:docker` uses Docker Compose and the Linux Playwright image pinned in `Dockerfile.visual` to generate and compare the common baseline on macOS, Windows, and CI. Filters such as `npm run test:visual:docker -- --grep 'test name'` are passed through to Playwright in the container. Update the image version when updating Playwright. Screenshots produced by local Playwright runs cannot be used to approve the common baseline.

1. Run `npm run test:visual:docker` first and inspect the actual image, expected image, and PNG diff.
2. Decide whether a difference is a regression, an intended design change, or an environment difference involving the browser, fonts, or OS. Fix regressions first.
3. If a baseline is missing or an intended design change needs a new reference image, state the update scope and deliberately run `npm run test:visual:update`. This command updates every visual test, so inspect the diff for unrelated changes.
4. Review the generated PNGs and differences manually. Obtain approval before using a new baseline as acceptance evidence or committing it. Until approval, report the baseline as pending review rather than claiming visual regression passed.
5. After approval, rerun `npm run test:visual:docker` to confirm the comparison passes.

Do not accept differences in bulk just to turn tests green. Generating candidate screenshots and approving them are separate steps; even a passing screenshot comparison does not replace product and design review.

## 4. Delivery report

Include:

- The actual problem and final behavior, changed files and reasons, and why an implementation is local or shared.
- Commands actually run and whether each passed, failed, or was not run.
- Specific causes of failures, screenshot or test artifact locations, and verification gaps.
- Any human review or approval still needed, with the affected pages and differences identified.

Describe environment issues only as observed in this run; do not assume the user's machine or a future run will fail the same way.
