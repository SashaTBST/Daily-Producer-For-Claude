# PRD to Issues — Reference

## Vertical Slice Rules

- Each slice delivers a narrow but COMPLETE path through every layer end-to-end
- A completed slice is demoable or verifiable on its own
- Prefer many thin slices over few thick ones
- HITL = requires human interaction · AFK = fully autonomous

## Issue Template

```
gh issue create --title "<slice title>" --body "$(cat <<'EOF'
## Parent PRD

#<prd-issue-number>

## What to build

Concise description of this vertical slice. End-to-end behaviour, not layer-by-layer.
Reference specific sections of the parent PRD rather than duplicating content.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2

## Blocked by

- Blocked by #<issue-number>
(or "None — can start immediately")

## User stories addressed

- User story 3
- User story 7
EOF
)"
```

## Notes

- Create issues in dependency order so you can reference real issue numbers
- Do not include specific file paths or line numbers in issue bodies
- Describe behaviours and contracts, not internal structure
