---
name: update-documentation-image
description: Workflow command scaffold for update-documentation-image in VCPToolBox.
allowed_tools: ["Bash", "Read", "Write", "Grep", "Glob"]
---

# /update-documentation-image

Use this workflow when working on **update-documentation-image** in `VCPToolBox`.

## Goal

Updates or fixes an image in the documentation, often with repeated quick fixes.

## Common Files

- `docs/image/*.jpg`
- `docs/image/*.png`

## Suggested Sequence

1. Understand the current state and failure mode before editing.
2. Make the smallest coherent change that satisfies the workflow goal.
3. Run the most relevant verification for touched files.
4. Summarize what changed and what still needs review.

## Typical Commit Signals

- Add or replace image file in docs/image/
- Commit fix if the image was incorrect or needs further adjustment

## Notes

- Treat this as a scaffold, not a hard-coded script.
- Update the command if the workflow evolves materially.