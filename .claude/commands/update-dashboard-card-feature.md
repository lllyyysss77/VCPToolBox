---
name: update-dashboard-card-feature
description: Workflow command scaffold for update-dashboard-card-feature in VCPToolBox.
allowed_tools: ["Bash", "Read", "Write", "Grep", "Glob"]
---

# /update-dashboard-card-feature

Use this workflow when working on **update-dashboard-card-feature** in `VCPToolBox`.

## Goal

Implements or optimizes a dashboard card feature, including code, build output, and documentation updates.

## Common Files

- `AdminPanel-Vue/src/components/dashboard/*.vue`
- `AdminPanel-Vue/src/dashboard/core/*.ts`
- `AdminPanel-Vue/src/api/*.ts`
- `AdminPanel-Vue/src/composables/*.ts`
- `AdminPanel-Vue/src/types/*.ts`
- `routes/admin/*.js`

## Suggested Sequence

1. Understand the current state and failure mode before editing.
2. Make the smallest coherent change that satisfies the workflow goal.
3. Run the most relevant verification for touched files.
4. Summarize what changed and what still needs review.

## Typical Commit Signals

- Edit or add Vue component in src/components/dashboard (e.g., CpuCard.vue, CalendarCard.vue)
- Update dashboard/core files (e.g., builtinCards.ts, builtinComponentMap.ts)
- Modify or add related API files (e.g., src/api/*.ts, routes/admin/*.js, routes/*.js)
- Update composables or types if needed (e.g., useDashboardState.ts, types/api.*.ts)
- Update or add documentation in docs/ (e.g., DOCUMENTATION_INDEX.md, feature-specific docs)

## Notes

- Treat this as a scaffold, not a hard-coded script.
- Update the command if the workflow evolves materially.