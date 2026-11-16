# Development Workflow

This workflow takes contributors and AI agents from a new task through review handoff.

## 1. Understand the task and acceptance criteria

- Read the relevant implementation, component contracts, and tests. Check the working tree and preserve changes outside the task.
- Turn the request into observable behavior or layout criteria. For example, at a 390px viewport, only the table container scrolls horizontally, the page itself does not overflow, and status filters still work.
- Resolve ambiguity from the implementation and request where possible. Ask when an ambiguity would materially affect behavior, interaction, scope, or cost; otherwise, follow the project's existing approach and state the assumption.

## 2. Choose an approach and scope

Identify the affected pages, domain components, and shared resources. Break complex work into reviewable steps; implement small changes directly.

| Change                                     | Where to implement it                                              | Regression scope                                       |
| ------------------------------------------ | ------------------------------------------------------------------ | ------------------------------------------------------ |
| Spacing in one chart                       | Local styles for that page or domain component                     | The affected page                                      |
| Radius of every card                       | Existing shared token                                              | Every page using that token                            |
| Trace selection and detail synchronization | Trace Explorer business state                                      | Search, filters, pagination, selection, and deep links |
| The same loading state in several places   | Consider a shared component after confirming the behavior contract | Every caller                                           |

## 3. Implement and get feedback

1. Make focused changes against the acceptance criteria, without unrelated refactors.
2. When behavior changes, update relevant tests for the normal path and important edge cases.
3. For UI changes, inspect appearance and interaction and adjust based on the results. For documentation-only or comment-only changes, run the checks appropriate to that scope.
4. If the scope or an assumption changes during implementation, revisit the approach and acceptance criteria before expanding the work.
5. Update the relevant standard when a stable shared rule emerges. Put one-off run results in the delivery report or PR.

## 4. Review the diff and verify

- Review the final diff against the acceptance criteria. Keep unrelated formatting changes out and preserve existing work.
- Remove temporary files, debug code, and unused imports introduced by this task. Leave unrelated existing files and changes alone.
- When an interface, component contract, or feature behavior changes, check callers and update component and feature docs.
- Choose and run checks using the [validation guide](VALIDATION.md). Distinguish implementation failures, environment blockers, and expected visual changes. The guide also defines screenshot approval and delivery reporting.
- Make sure verification covers the final implementation. After further changes, rerun checks affected by those changes.

## 5. Hand off for review

For a PR, put the change summary and verification evidence in its description. For local collaboration, provide a task delivery report.

State unresolved issues, verification gaps, and items awaiting review. Code completion, passing checks, approval, and merge are separate states; report only the states actually reached. Create a PR, commit, or merge according to the task's authorization, not automatically as a consequence of this workflow.
