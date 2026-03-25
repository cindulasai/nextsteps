# Skill Routing

NextSteps acts as a skill router — it discovers installed workflow skills and suggests them when context matches their trigger conditions.

## Discovery

Scan for installed skills at cold-start and cache the list in memory for the session:

1. Read `.github/skills/*/SKILL.md` — tessl-installed skills
2. Read `.github/copilot/skills/*/SKILL.md` — manually added skills
3. For each skill found, extract from YAML frontmatter: `name`, `description`
4. Store as a lightweight roster: `[{name, description, trigger-keywords}]`

**Trigger keywords**: Extract from the skill's `description` field. Look for phrases after "Use when", "Triggers on", or "Use for". These become match candidates.

If no skills are installed (besides nextsteps itself), skip routing entirely — zero overhead.

## Context Matching

During Step 4 (Generate Candidates), after generating category-based candidates, check if the current conversation context matches any installed skill's trigger condition:

### Match Signals (any one is sufficient)

| Signal | Example |
|--------|---------|
| User's task aligns with skill description | User finishing code + `finishing-a-development-branch` installed |
| User explicitly mentions a workflow the skill handles | "I need to create a PR" + branch-finishing skill |
| Conversation state matches trigger keywords | Tests passing, branch work done → branch completion skill |
| User is stuck at a decision point the skill resolves | "Should I merge or PR?" → branch finishing skill |

### Match Confidence

- **HIGH**: 2+ signals match → suggest the skill in a guaranteed slot (replace one MODERATE candidate)
- **LOW**: 1 signal matches → suggest the skill only if a Quick Win slot is available
- **NONE**: 0 signals → don't suggest

Never suggest more than 1 skill per response. If multiple skills match, pick the one with highest confidence. Break ties by relevance to the most recent user message.

## Suggestion Format

Skill-routed suggestions use existing categories but get a special prefix:

```
🔧 **Use skill: [skill-name]** — [Why it's relevant right now]
```

Or in context of the category it replaces:

```
⚡ **Activate finishing-a-development-branch** — Tests pass and your feature branch is ready; this skill handles merge/PR/cleanup
```

**Rules:**
- Use the skill's exact `name` from its frontmatter
- Explain WHY now, not what the skill does generically
- Never suggest a skill the user has already dismissed in this session
- If the user selects a skill suggestion, log `[SELECTED] #N category: skill-route, skill: [name]` in HISTORY.md

## Anti-Patterns

1. **Don't be a skill catalog** — Only surface a skill when context genuinely matches. Never list available skills unprompted.
2. **Don't override user intent** — If the user is clearly in the middle of work, don't interrupt with "you should use X skill instead."
3. **Don't suggest skills for trivial contexts** — A simple Q&A doesn't need a workflow skill suggestion.
4. **Don't duplicate** — If a skill would do the same thing as a regular suggestion, prefer the skill (it's more structured).

## Examples

### Match: finishing-a-development-branch

**Context**: User just finished implementing a feature, tests pass, on a feature branch.

**Trigger keywords from skill**: "implementation is complete", "all tests pass", "integrate the work"

**Suggestion**:
```
⚡ **Activate finishing-a-development-branch** — Your tests pass and you're on a feature branch; this skill walks you through merge/PR/discard options
```

### Match: test-driven-development

**Context**: User asks to implement a new function.

**Suggestion**:
```
🔧 **Use skill: test-driven-development** — Start with a failing test for this function; the skill runs red-green-refactor automatically
```

### No Match

**Context**: User asks "what's the difference between let and const?"

**Result**: No skill routes. All 5 suggestions come from standard categories.

## Tracking

Skill-route suggestions follow normal selection tracking. Additionally:

- If a routed skill is selected 3+ times across sessions → mark it as a "frequent route" in PREFERENCES.md under a `skill-affinities` section
- If a routed skill is ignored 5+ times when suggested → stop suggesting it unless match confidence is HIGH
