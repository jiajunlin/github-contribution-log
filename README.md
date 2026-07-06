# Contribution [5]: Add Storybook Stories for Four Chat Surface Components

**Contribution Number:** 1
**Student:** [Jiajun Lin]
**Issue:** [https://github.com/shep-ai/shep/issues/652]
**Status:** Phase IV — In Progress
 
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
 
- [x] All four story files type-check cleanly against real component source and real `@assistant-ui/react`/`radix-ui`/`storybook` types (verified via an isolated `tsc` check, since local `node_modules` wasn't available)
- [ ] `pnpm build:storybook` completes without errors — not yet run, needs a full local install
- [ ] `pnpm validate` passes — not yet run
### Manual Testing
 
- [ ] Open Storybook locally and confirm all four components appear in the sidebar
- [ ] Verify each variant renders correctly without console errors
- [ ] Confirm `TurnGroupCard` with `status: 'in-progress'` renders open by default
- [ ] Confirm `ToolChip` tint variants visually differ as expected
- [ ] Confirm `ChatComposer`'s mock runtime doesn't throw and the Send button behaves reasonably in the canvas
---
 
## Implementation Notes
 
### Week 1 Progress
 
**What I built:**
- Pulled the actual repo and read the real source for all four target components before writing anything, rather than working from the issue text alone
- Confirmed against the live code that none of the four files had a colocated `.stories.tsx` yet (issue's premise checked out)
- Read `CONTRIBUTING.md` and `src/presentation/web/components/features/chat/CLAUDE.md` per the Phase III step-by-step before starting
- Added 4 new files, all colocated, no component edits:
  - `turn-group-card.stories.tsx` — `Default` (completed/collapsed), `InProgress` (open with condensed slot), matching the open-by-default-for-in-progress rule in `CLAUDE.md`
  - `tool-bubble/tool-chip.stories.tsx` — `Default`, `GreenTint`, `BlueTint`, `Expanded`, with a small interactive wrapper so `expanded` actually toggles in the Storybook canvas
  - `tool-bubble/generic-bubble.stories.tsx` — `Default` (parsed-args case) and `WithOutput` (paired input + output sections)
  - `ChatComposer.stories.tsx` — `Default`, `Disabled`, `WithAttachment`
**Decisions made:**
- `ChatComposer` renders `ComposerPrimitive`/`ThreadPrimitive` from `@assistant-ui/react`, which throws without an `AssistantRuntimeProvider` ancestor — it can't mount standalone like the other three. Rather than inventing a new pattern, I reused the existing mock-runtime wrapper (`useExternalStoreRuntime` + `AssistantRuntimeProvider`) already established in `ChatTab.stories.tsx`, trimmed down to just what the composer needs.
- Found a real discrepancy between the issue and `CONTRIBUTING.md`: the repo's stated rule is that every component ships Default/Loading/Empty/Error states, but all four of these are pure presentational components with no async/fetch logic, so Loading/Empty/Error don't apply. I followed the issue's actual acceptance criteria (state/variant coverage matching each component's real prop surface) instead of forcing irrelevant states onto components that can't have them. Worth flagging to a maintainer in the PR description rather than silently deciding this is right.
**Challenges faced:**
- No `node_modules` in my working environment and a full `pnpm install` wasn't practical, so I couldn't run `pnpm build:storybook` or `pnpm validate` directly.
- To still verify correctness rather than assume it, I set up an isolated TypeScript check pulling in the *real* component source plus real `react`, `@storybook/react`, `@assistant-ui/react`, `radix-ui`, and `lucide-react` types, stubbing only two unrelated leaf UI primitives (`Dialog`, `Textarea`) that `ChatComposer`'s attachment-chip dependency pulls in but my story never touches. All four story files type-check cleanly against real types this way.
- Still need to actually run `pnpm build:storybook` and `pnpm validate` in a full local environment before this is truly done — noting as a blocker/next step, not glossing over it.
**Next steps:**
- Run `pnpm build:storybook` and `pnpm validate` locally to get a real pass/fail signal
- Open each story in the Storybook canvas and manually confirm rendering matches expectations
- Flag the Loading/Empty/Error discrepancy in the PR description for maintainer input
### Code Changes
 
- **Files modified:** None
- **Files added:**
  - `src/presentation/web/components/features/chat/ChatComposer.stories.tsx`
  - `src/presentation/web/components/features/chat/turn-group-card.stories.tsx`
  - `src/presentation/web/components/features/chat/tool-bubble/tool-chip.stories.tsx`
  - `src/presentation/web/components/features/chat/tool-bubble/generic-bubble.stories.tsx`
- **Key commits:** [Links to commits as they are made]
- **Approach decisions:** Followed the existing `ChatMessageBubble.stories.tsx` meta/StoryObj scaffold for consistency. For `ChatComposer`, reused the mock `AssistantRuntimeProvider` pattern from `ChatTab.stories.tsx` rather than inventing a new way to satisfy the `assistant-ui` runtime dependency.
---
 
## Pull Request
 
**PR Link:** [Add GitHub PR URL after opening]
 
**PR Description:**
 
*What does this PR do?* Adds colocated `.stories.tsx` files for four chat-surface components — `ChatComposer`, `TurnGroupCard`, `ToolChip`, and `GenericToolBubble` — that had none. No component logic was changed; this is purely additive visual-regression coverage.
 
*Why was this PR needed?* These four Layer 3 chat primitives were out of compliance with the mandatory `CLAUDE.md` rule that every web UI component must have a colocated story. Without stories, any visual regression in these components is invisible until a user reports it. Closes #[issue number].
 
*Key implementation decision:* `ChatComposer` renders `ComposerPrimitive`/`ThreadPrimitive` from `@assistant-ui/react`, which requires an `AssistantRuntimeProvider` ancestor to mount at all. Rather than inventing a new pattern, I reused the existing minimal mock-runtime wrapper (`useExternalStoreRuntime` + `AssistantRuntimeProvider`) already established in `ChatTab.stories.tsx`.
 
*Note for reviewers:* All four components are pure presentational primitives with no async/fetch logic, so there is no Loading or Error state to author. `CONTRIBUTING.md` lists Default/Loading/Empty/Error as the standard set, but that doesn't apply here — variants instead cover each component's actual prop surface (status, tint, expanded, disabled, attachments). Happy to add states if there's a preferred way to represent these in Storybook controls.
 
**Maintainer Feedback:**
- [Date]: [Summary of feedback — fill in after PR is open]
- [Date]: [How you addressed it]
**Status:** [Update to: Awaiting review / Iterating / Approved / Merged after submitting]
 
---
 
## Learnings & Reflections
 
### Technical Skills Gained
 
- **Reading before writing.** The issue text alone wasn't enough — the `ChatComposer` mock-runtime pattern only became obvious after reading `ChatTab.stories.tsx` and the `@assistant-ui/react` provider requirements from actual component source. Reading two or three existing files in the same area always pays off more than starting from scratch.
- **Spec discrepancies are normal and worth surfacing.** The issue's acceptance criteria and `CONTRIBUTING.md`'s story requirements didn't fully align (Loading/Empty/Error states on presentational components). The right move was to note it in the PR description rather than silently pick one or the other.
- **Type-checking in isolation is a real fallback.** Without a full `pnpm install`, I ran an isolated `tsc` against the real component source and real third-party type packages. It caught a real prop-shape issue and gave genuine confidence — not as good as running the actual build, but meaningfully better than nothing.
### Challenges Overcome
 
- `ChatComposer` uses `ComposerPrimitive` from `@assistant-ui/react`, which throws without a runtime provider — it can't be rendered in Storybook like a plain component. Solved by finding and reusing the existing `useExternalStoreRuntime` + `AssistantRuntimeProvider` pattern already in `ChatTab.stories.tsx` rather than guessing at a new solution.
- No `node_modules` available in my working environment meant `pnpm build:storybook` and `pnpm validate` couldn't run directly. Solved by setting up an isolated TypeScript check with the real relevant packages installed — but this is still a gap that needs closing with a real local run before the PR should be considered fully verified.
### What I'd Do Differently Next Time
 
- Set up the full local dev environment first, before writing any code, so I can run `pnpm build:storybook` incrementally as I go rather than deferring it to the end.
- Check `CODEOWNERS` and recent merged PRs in the same directory earlier to know who to tag for review — doing that during implementation rather than after submission saves a round trip.
---
 
## Resources Used
 
- `src/presentation/web/components/features/chat/ChatMessageBubble.stories.tsx` — primary scaffolding reference
- `src/presentation/web/components/bedrock-memory-panel.stories.tsx` — secondary scaffold reference
- `src/presentation/web/components/features/chat/CLAUDE.md` — three-layer architecture docs
- `.storybook/mocks/app/actions/` — mock wiring reference
- [Storybook docs on StoryObj pattern](https://storybook.js.org/docs/writing-stories/typescript)
 
