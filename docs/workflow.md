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
- Choose and run checks using the [validation guide](validation.md). Distinguish implementation failures, environment blockers, and expected visual changes. The guide also defines screenshot approval and delivery reporting.
- Make sure verification covers the final implementation. After further changes, rerun checks affected by those changes.

## 5. Hand off for review

For a PR, put the change summary and verification evidence in its description. For local collaboration, provide a task delivery report.

State unresolved issues, verification gaps, and items awaiting review. Code completion, passing checks, approval, and merge are separate states; report only the states actually reached. Create a PR, commit, or merge according to the task's authorization, not automatically as a consequence of this workflow.

## 6. Phase progress and build-log maintenance

For feature work or phase recovery, read the [roadmap](plan.md), [build log](features/build-log.md), and relevant phase file before implementation. Select the phase by the user's task or observed work; the latest numbered phase is not automatically active. A read-only answer or routine typo fix does not need a new log entry.

- **Ownership:** the roadmap contains phase goals, dependencies, and execution-plan links; phase/component documents contain scope and behavior contracts; the log contains observed progress and verification evidence. Keep maintenance rules here rather than copying them into the log.
- **Summary:** keep implementation and acceptance status separate. Identify active feature work only when it is underway, and distinguish confirmed defects/blockers from verification gaps. Mark missing historical metadata as unknown.
- **Activity:** append material phase progress, changes to this development workflow, review outcomes, or unresolved handoff items. Include a timestamp with timezone, phase or documentation scope, branch and revision/working-tree context, material changes, exact check commands/results, applicable review state, limitations, and the next authorized action. Link to detailed evidence rather than copying full plans, code, or command output.
- **Evidence:** distinguish observed, reported, and historical results and explain skipped checks. Documentation-only changes do not invalidate historical feature results or require unrelated runtime tests. Claim acceptance passed only with the required checks and applicable approvals for the relevant implementation.
- **Corrections:** update the summary when evidence changes and append corrections identifying superseded claims. Preserve earlier failures and their resolution. Do not infer completion from existing code or treat a next-action note as permission to start another phase.
