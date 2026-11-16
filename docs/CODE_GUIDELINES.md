# Code Guidelines

## 1. Module boundaries and dependencies

- Organize modules by responsibility and use a consistent structure for similar code.
- Keep code and assets that change together close together. Put genuinely shared behavior in an explicit shared module.
- Give each module a clear interface so callers do not depend on implementation details.
- Keep dependencies moving in one direction. General-purpose modules must not depend on individual pages, and circular dependencies should be avoided.
- When changing module boundaries, update imports, tests, and related documentation together.

## 2. Responsibilities and reuse

- Components handle presentation and interaction. Move complex business calculations out of rendering code so they can be understood and tested independently.
- Prefer calculations that take explicit inputs and return results. Keep data access and other side effects in clearly owned locations.
- Do not mutate data passed in by callers unless the interface contract explicitly allows it.
- Before extracting shared behavior, confirm that its use cases have the same behavior contract. Keep the resulting interface small.
- Split code when doing so makes it easier to understand and change. Avoid abstractions for hypothetical needs.

## 3. Types, data, and state

- Express data contracts with specific types. Do not use `any` or type assertions to hide uncertainty.
- Validate external data at the system boundary, and define how missing, invalid, and failed inputs are handled.
- Give each piece of information a clear source of truth. Derive values from existing data instead of storing duplicate state when possible.
- Keep state in the component or module that owns it; do not maintain independent copies of the same state.
- Give side effects explicit triggers, and handle cleanup, cancellation, and repeated execution where needed.

## 4. Naming, formatting, and styles

- Choose names that convey purpose and domain meaning, following the conventions of the surrounding module.
- Keep functions, components, and files focused. Use local variables or helper functions to make complex expressions easier to follow.
- Format nested JSX, multi-step functions, and style rules so their hierarchy and control flow are easy to inspect.
- Keep local styles with their modules; manage global styles and design tokens centrally.
- Scope selectors to prevent local changes from affecting unrelated modules.

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
