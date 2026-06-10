# Contribution [#]: Add Storybook Stories for Four Chat Surface Components

**Contribution Number:** 1
**Student:** [Your Name]
**Issue:** [GitHub issue link]
**Status:** Phase I — In Progress

---

## Why I Chose This Issue

This issue is a good entry point into a real production codebase without requiring any changes to existing component logic. Since it's purely additive — writing `.stories.tsx` files only — it's low-risk and well-scoped, which means I can focus on understanding the codebase structure and conventions rather than debugging unfamiliar behavior. It also has a clear acceptance criteria list, so I'll know exactly when I'm done.

The issue also touches a genuinely interesting architectural pattern: the three-layer chat rendering system (raw events → grouping → high-level cards). Writing visual regression stories for Layer 3 primitives means I need to understand what each component actually does and how its props map to visible states, which is a solid way to build mental models of an unfamiliar frontend codebase.

---

## Understanding the Issue

### Problem Description

Four presentational components in the chat surface (`ChatComposer`, `TurnGroupCard`, `ToolChip`, and `GenericBubble`) have no colocated `.stories.tsx` files. The project's `CLAUDE.md` mandates that every web UI component must have one, so these four are out of compliance. More practically, without stories, any visual regression in these components is invisible until a user reports it.

### Expected Behavior

Each of the four components should have a colocated `*.stories.tsx` file with a `Default` story plus 2–3 variant stories covering the key prop combinations documented in the issue.

### Current Behavior

No `.stories.tsx` files exist for these components. Running Storybook shows no stories for `ChatComposer`, `TurnGroupCard`, `ToolChip`, or `GenericBubble`.

### Affected Components

- `src/presentation/web/components/features/chat/ChatComposer.tsx`
- `src/presentation/web/components/features/chat/turn-group-card.tsx`
- `src/presentation/web/components/features/chat/tool-bubble/tool-chip.tsx`
- `src/presentation/web/components/features/chat/tool-bubble/generic-bubble.tsx`

---

## Reproduction Process

### Environment Setup

[Notes on setting up local dev environment — challenges faced and how resolved]

### Steps to Reproduce

1. Clone the repo and run `pnpm install`
2. Run `pnpm build:storybook` or open Storybook locally
3. Navigate to the chat surface components section — no stories exist for the four listed components

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in fork]
- **Screenshots/logs:** [If applicable]
- **My findings:** Confirmed all four component files exist at the listed paths with no sibling `.stories.tsx` file

---

## Solution Approach

### Analysis

This is not a bug — it's a missing coverage gap. The components already exist and work; they just lack Storybook stories. The root cause is that these four components were likely written before the mandatory stories rule was enforced, or the rule was added after. No component logic needs to change.

### Proposed Solution

Write four new `.stories.tsx` files, one colocated with each component, using the existing `ChatMessageBubble.stories.tsx` and `bedrock-memory-panel.stories.tsx` as scaffolding references. Each file will export a `Default` story plus variants matching the acceptance criteria.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** Four chat components lack required `.stories.tsx` files. Need to add them with correct variants — no component changes allowed.

**Match:** Existing pattern is in `ChatMessageBubble.stories.tsx` — use its `meta` + `StoryObj` scaffolding structure. Check `.storybook/mocks/app/actions/` to see if any mock wiring is needed (issue says probably not for these four).

**Plan:**
1. Read `src/presentation/web/components/features/chat/CLAUDE.md` to understand the three-layer architecture and the `in-progress` open-by-default behavior before writing `TurnGroupCard` stories
2. Open `ChatMessageBubble.stories.tsx` and copy the meta/StoryObj scaffold
3. Write `turn-group-card.stories.tsx`: `Default` (completed, collapsed), `InProgress` (open with condensed slot)
4. Write `tool-chip.stories.tsx`: `Default` (tint: default), `GreenTint`, `BlueTint`, `Expanded` (expanded: true)
5. Write `generic-bubble.stories.tsx`: `Default`, `ParsedArgs`, `WithOutput` (input + output sections)
6. Write `chat-composer.stories.tsx`: `Default`, `Disabled`, `WithAttachment`
7. Run `pnpm build:storybook` and `pnpm validate` locally to confirm all pass

**Implement:** [Link to branch/commits as work progresses]

**Review:**
- [ ] No component files modified
- [ ] All four stories files colocated with their component
- [ ] Each file has at least a `Default` export
- [ ] Variants match acceptance criteria exactly
- [ ] Follows same meta/StoryObj pattern as reference files

**Evaluate:** `pnpm build:storybook` passes, `pnpm validate` passes, all four components appear in Storybook with correct variants

---

## Testing Strategy

### Unit Tests

- [ ] N/A — this is a Storybook-only change; no unit tests required

### Integration Tests

- [ ] `pnpm build:storybook` completes without errors
- [ ] `pnpm validate` passes

### Manual Testing

- Open Storybook locally and confirm all four components appear in the sidebar
- Verify each variant renders correctly without console errors
- Confirm `TurnGroupCard` with `status: 'in-progress'` renders open by default
- Confirm `ToolChip` tint variants visually differ as expected

---

## Implementation Notes

### Week 1 Progress

[Document progress as work begins — what was built, decisions made, blockers]

### Code Changes

- **Files modified:** None
- **Files added:** 4 new `.stories.tsx` files (listed above)
- **Key commits:** [Links to commits as they are made]
- **Approach decisions:** Strictly following the existing `ChatMessageBubble.stories.tsx` scaffold to stay consistent with project conventions; not introducing any new patterns

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [To be drafted — will adapt content from this document]

**Maintainer Feedback:**
- [Date]: [Summary of feedback]
- [Date]: [How it was addressed]

**Status:** Awaiting implementation

---

## Learnings & Reflections

### Technical Skills Gained

[To be filled in after completion]

### Challenges Overcome

[To be filled in after completion]

### What I'd Do Differently Next Time

[To be filled in after completion]

---

## Resources Used

- `src/presentation/web/components/features/chat/ChatMessageBubble.stories.tsx` — primary scaffolding reference
- `src/presentation/web/components/bedrock-memory-panel.stories.tsx` — secondary scaffold reference
- `src/presentation/web/components/features/chat/CLAUDE.md` — three-layer architecture docs
- `.storybook/mocks/app/actions/` — mock wiring reference
- [Storybook docs on StoryObj pattern](https://storybook.js.org/docs/writing-stories/typescript)
