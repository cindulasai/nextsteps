# NextSteps Test Cases

> Production-grade test suite for `crab/nextsteps` skill.
> Each test case specifies: scenario, input context, expected behavior, and pass/fail criteria.

---

## T01 — First Activation (Cold Start)

**Scenario**: User has no `.nextsteps/` directory. Agent responds to a coding question.

**Input Context**:
- No `.nextsteps/` directory exists
- Project has `package.json` (Node.js, Express), `README.md`
- User asked: "How do I add middleware to my Express app?"
- Agent provided a complete answer

**Expected Behavior**:
1. Agent detects missing `.nextsteps/` and runs cold-start protocol
2. Creates `.nextsteps/PREFERENCES.md` with Schema v2 defaults
3. Seeds Topic Affinities from project scan (e.g., `express: MODERATE`, `node: MODERATE`)
4. Creates `.nextsteps/HISTORY.md` with header template
5. Creates `.nextsteps/BACKLOG.md` with header template
6. Generates 5 next steps (default count) with balanced category distribution
7. Includes a suggestion to add `.nextsteps/` to `.gitignore` (security rule)

**Pass Criteria**:
- [ ] `.nextsteps/PREFERENCES.md` created with Schema Version 2
- [ ] `.nextsteps/HISTORY.md` created with correct header
- [ ] `.nextsteps/BACKLOG.md` created with correct header
- [ ] Response ends with `## ⚡ Next Steps` section
- [ ] Exactly 5 suggestions shown
- [ ] At least 1 ⚡ Direct Follow-up related to Express middleware
- [ ] At least 1 🔧 Actionable Task
- [ ] `.gitignore` suggestion present (security rule 5)
- [ ] No generic filler suggestions

---

## T02 — Standard Activation (Happy Path)

**Scenario**: Returning user with established preferences. Agent responds to a debugging question.

**Input Context**:
- `.nextsteps/PREFERENCES.md` exists: enabled=true, display-count=5, format=standard
- Category preferences: direct-follow-up=STRONG, deep-dive=STRONG, others=MODERATE
- `.nextsteps/BACKLOG.md` has 2 OPEN items: "Add unit tests for auth module", "Refactor DB connection pool"
- User asked: "Why is my React component re-rendering on every state update?"
- Agent diagnosed unnecessary re-renders and provided fix

**Expected Behavior**:
1. Reads PREFERENCES.md for config
2. Generates 5 suggestions respecting STRONG categories
3. Includes at least 1 backlog recall item
4. All suggestions are specific to the React re-rendering context

**Pass Criteria**:
- [ ] Exactly 5 suggestions shown in standard format (icons, bold titles, descriptions)
- [ ] ⚡ Direct Follow-up present (STRONG = guaranteed slot)
- [ ] 🔍 Deep Dive present (STRONG = guaranteed slot)
- [ ] 📋 Memory Recall present referencing a backlog item
- [ ] No suggestion restates the re-rendering fix already given
- [ ] Footer line present: "_Your selections help me learn what matters to you._"

---

## T03 — Compact Format

**Scenario**: User has configured compact format.

**Input Context**:
- PREFERENCES.md: format=compact, display-count=3
- User asked a quick question about git commands

**Expected Behavior**:
1. Shows 3 suggestions in compact format
2. No icons, no bold, single-line pipe-separated

**Pass Criteria**:
- [ ] Output matches: `⚡ Next: [1] Title | [2] Title | [3] Title`
- [ ] Exactly 3 items
- [ ] No multi-line formatting

---

## T04 — Disabled State

**Scenario**: User previously disabled next steps.

**Input Context**:
- PREFERENCES.md: enabled=false
- User asked: "Explain the difference between TCP and UDP"

**Expected Behavior**:
1. Reads PREFERENCES.md, sees enabled=false
2. Does NOT append any next steps section
3. Still maintains BACKLOG.md passively if relevant topics mentioned

**Pass Criteria**:
- [ ] Response does NOT contain `## ⚡ Next Steps` or any compact equivalent
- [ ] Response does NOT contain suggestion formatting
- [ ] Agent answers the question normally

---

## T05 — Re-enable Next Steps

**Scenario**: User re-enables after being disabled.

**Input Context**:
- PREFERENCES.md: enabled=false
- User says: "Enable next steps again"

**Expected Behavior**:
1. Detects re-enable intent
2. Sets enabled=true in PREFERENCES.md
3. Logs `[ENABLED]` in HISTORY.md
4. Confirms and immediately appends next steps

**Pass Criteria**:
- [ ] PREFERENCES.md updated: enabled=true
- [ ] HISTORY.md has `[ENABLED]` entry
- [ ] Confirmation message present
- [ ] Response ends with next steps section

---

## T06 — Customization: Change Display Count

**Scenario**: User wants fewer suggestions.

**Input Context**:
- PREFERENCES.md: display-count=5
- User says: "Show me only 3 next steps from now on"

**Expected Behavior**:
1. Detects customization intent (count change)
2. Validates 3 is within range [1-7]
3. Confirms: describes the change
4. Updates PREFERENCES.md: display-count=3
5. Logs `[CONFIG-CHANGE] property: display-count, old: 5, new: 3`
6. Shows exactly 3 next steps in the response

**Pass Criteria**:
- [ ] Confirmation message acknowledges the change
- [ ] PREFERENCES.md: display-count=3
- [ ] HISTORY.md: `[CONFIG-CHANGE]` entry logged
- [ ] Exactly 3 next steps shown (not 5)

---

## T07 — Customization: Invalid Value

**Scenario**: User requests out-of-range value.

**Input Context**:
- PREFERENCES.md: display-count=5
- User says: "Show me 20 next steps"

**Expected Behavior**:
1. Detects customization intent
2. Validates 20 > max-count (7 default)
3. Does NOT silently clamp
4. Responds: "I can set display-count between 1 and 7. Want me to set it to 7?"

**Pass Criteria**:
- [ ] Agent does NOT set display-count to 20
- [ ] Agent suggests closest valid value (7)
- [ ] Agent asks for confirmation before applying
- [ ] PREFERENCES.md unchanged until user confirms

---

## T08 — Anti-Pattern: No Generic Filler

**Scenario**: Agent must not produce generic suggestions.

**Input Context**:
- User asked: "How do I deploy a Docker container to AWS ECS?"
- Agent provided complete deployment instructions

**Pass Criteria** (negative test — none of these should appear):
- [ ] No "Want to know more about Docker?"
- [ ] No "Can you explain further?"
- [ ] No "Tell me more about ECS"
- [ ] No "Pros and cons of ECS?"
- [ ] No "Would you like me to do anything else?"
- [ ] No "What is [term]?" for terms already explained
- [ ] All suggestions are specific and actionable

---

## T09 — Anti-Pattern: No Restating the Obvious

**Scenario**: Agent just explained something in detail.

**Input Context**:
- User asked: "What's the difference between `let` and `const` in JavaScript?"
- Agent provided thorough explanation with examples

**Pass Criteria**:
- [ ] No suggestion like "Deep dive into let vs const"
- [ ] No suggestion like "Explore more about variable declarations"
- [ ] Suggestions move the conversation FORWARD, not backward
- [ ] At least one lateral suggestion (e.g., "Explore TypeScript's type inference for stricter variable safety")

---

## T10 — Anti-Pattern: No Scope Mismatch

**Scenario**: Quick-fix session where user is fixing a bug.

**Input Context**:
- Conversation is a 2-message exchange about a typo in a CSS selector
- Agent fixed the typo

**Pass Criteria**:
- [ ] No suggestions like "Redesign your entire CSS architecture"
- [ ] No suggestions like "Migrate from CSS to Tailwind"
- [ ] Suggestions are appropriately scoped (quick actions, related fixes)
- [ ] At least one Quick Win suggestion

---

## T11 — Selection Tracking

**Scenario**: User selects a suggestion by referencing it.

**Input Context**:
- Previous response showed 5 next steps. #2 was: "🔧 **Add input validation to the /users endpoint**"
- User says: "Yes, let's add that input validation"

**Expected Behavior**:
1. Detects selection matches suggestion #2
2. Logs `[SELECTED] #2 actionable-task` in HISTORY.md
3. Proceeds to help with input validation
4. Generates fresh next steps at end of new response

**Pass Criteria**:
- [ ] HISTORY.md has `[SELECTED]` entry
- [ ] Category recorded correctly (actionable-task)
- [ ] Agent proceeds with the selected task
- [ ] New next steps generated (not repeating old ones)

---

## T12 — All Suggestions Ignored

**Scenario**: User ignores all suggestions and asks unrelated question.

**Input Context**:
- Previous response showed 5 next steps about React performance
- User says: "How do I set up a PostgreSQL database?"

**Expected Behavior**:
1. Detects no overlap with previous suggestions
2. Logs `[IGNORED] all` in HISTORY.md
3. Answers the PostgreSQL question
4. Generates new next steps relevant to PostgreSQL

**Pass Criteria**:
- [ ] HISTORY.md has `[IGNORED]` entry
- [ ] New suggestions are about PostgreSQL, NOT about React
- [ ] No stale React suggestions carried forward

---

## T13 — Security: No Hardcoded Credentials

**Scenario**: Conversation involves API keys.

**Input Context**:
- User asked: "How do I configure the Stripe API in my app?"
- Conversation includes mention of `sk_test_xyz123`

**Pass Criteria**:
- [ ] No suggestion contains `sk_test_xyz123` or any API key
- [ ] No suggestion says "Add your API key to config.js"
- [ ] Suggestions reference environment variables for secrets
- [ ] If credential was in context, it's sanitized from next steps

---

## T14 — Security: .gitignore Enforcement

**Scenario**: `.nextsteps/` exists but not in `.gitignore`.

**Input Context**:
- `.nextsteps/` directory exists with preferences
- `.gitignore` exists but does not contain `.nextsteps/`

**Pass Criteria**:
- [ ] One suggestion says to add `.nextsteps/` to `.gitignore`
- [ ] This is categorized as ✅ Quick Win
- [ ] This takes a guaranteed slot (overrides normal allocation)
- [ ] Persists until user adds it or dismisses

---

## T15 — Token Budget Exhaustion

**Scenario**: Agent response is very long, approaching token limit.

**Input Context**:
- User asked for a comprehensive architecture review
- Agent's response is extremely long (approaching output token limit)

**Expected Behavior**:
1. Detects limited remaining token budget
2. Switches to compact format regardless of user's format preference
3. Shows min-count items (default: 1)
4. If even compact won't fit, uses inline format: `(Next: [suggestion])`

**Pass Criteria**:
- [ ] Next steps ARE present (reliability guarantee)
- [ ] Format is compact or inline (not standard)
- [ ] At least 1 suggestion shown
- [ ] Suggestion is still specific and contextual (not placeholder)

---

## T16 — Error Recovery: Missing PREFERENCES.md

**Scenario**: `.nextsteps/` exists but PREFERENCES.md was deleted.

**Input Context**:
- `.nextsteps/HISTORY.md` and `.nextsteps/BACKLOG.md` exist
- `.nextsteps/PREFERENCES.md` is missing

**Expected Behavior**:
1. Detects missing PREFERENCES.md
2. Uses defaults: enabled=true, display-count=5, format=standard
3. Recreates PREFERENCES.md from defaults on next write
4. Generates next steps normally — does NOT fail

**Pass Criteria**:
- [ ] Next steps generated successfully (not blocked)
- [ ] Uses default count of 5
- [ ] PREFERENCES.md recreated with Schema v2 defaults
- [ ] HISTORY.md and BACKLOG.md preserved

---

## T17 — Error Recovery: Corrupted PREFERENCES.md

**Scenario**: PREFERENCES.md has invalid/corrupted content.

**Input Context**:
- PREFERENCES.md contains garbled text or invalid structure

**Expected Behavior**:
1. Attempts to parse PREFERENCES.md
2. Preserves any readable sections
3. Fills missing values with defaults
4. Generates next steps normally

**Pass Criteria**:
- [ ] Agent does NOT crash or refuse to respond
- [ ] Next steps generated with default values
- [ ] Any readable preferences are preserved
- [ ] Non-readable sections replaced with defaults

---

## T18 — History Overflow (50+ entries)

**Scenario**: HISTORY.md has reached 50 entries.

**Input Context**:
- HISTORY.md has 52 entries (2 over limit)

**Expected Behavior**:
1. Detects overflow (> 50)
2. Summarizes oldest 25 entries into a `[SUMMARY]` block
3. Retains newest 27 entries (25 new + 2 that were over)
4. Summary includes: period, selection count, top category, promotions/demotions

**Pass Criteria**:
- [ ] HISTORY.md has a `[SUMMARY]` block for the oldest entries
- [ ] Total entries after cleanup ≤ 50
- [ ] Summary preserves aggregate statistics
- [ ] No data loss — trends are captured in summary

---

## T19 — Self-Improvement: Category Promotion

**Scenario**: User consistently selects Deep Dive suggestions.

**Input Context**:
- HISTORY.md has 10 recent entries
- 4 of the 10 are `[SELECTED] deep-dive`
- PREFERENCES.md: deep-dive=MODERATE

**Expected Behavior**:
1. Detects 4/10 selections for deep-dive (≥ 3 threshold)
2. Promotes deep-dive: MODERATE → STRONG
3. Updates PREFERENCES.md
4. Deep Dive gets a guaranteed slot in future suggestions

**Pass Criteria**:
- [ ] PREFERENCES.md: deep-dive=STRONG
- [ ] HISTORY.md logs the promotion
- [ ] Next suggestions include a guaranteed Deep Dive slot

---

## T20 — Self-Improvement: Category Demotion

**Scenario**: User never selects Quick Win suggestions.

**Input Context**:
- HISTORY.md has 15+ entries where Quick Win was presented
- 0 selections of Quick Win in those entries
- PREFERENCES.md: quick-win=MODERATE

**Expected Behavior**:
1. Detects 0/15 selections (demotion threshold met)
2. Demotes quick-win: MODERATE → WEAK
3. Updates PREFERENCES.md
4. Quick Win only fills spare slots going forward

**Pass Criteria**:
- [ ] PREFERENCES.md: quick-win=WEAK
- [ ] HISTORY.md logs the demotion
- [ ] Quick Win no longer gets guaranteed or round-robin slots

---

## T21 — Disable via Explicit Command

**Scenario**: User explicitly disables next steps.

**Input Context**:
- PREFERENCES.md: enabled=true
- User says: "Turn off next steps"

**Expected Behavior**:
1. Detects disable intent
2. Sets enabled=false
3. Logs `[DISABLED]`
4. Confirms: "NextSteps disabled. Say 'enable next steps' anytime to turn them back on."
5. NO next steps appended to this response
6. Passive backlog collection continues

**Pass Criteria**:
- [ ] PREFERENCES.md: enabled=false
- [ ] HISTORY.md: `[DISABLED]` entry
- [ ] Confirmation message shown
- [ ] No next steps section in response
- [ ] Subsequent responses also have no next steps

---

## T22 — Multiple Category Exclusion

**Scenario**: User excludes categories.

**Input Context**:
- User says: "Don't show me lateral suggestions or memory recall items"

**Expected Behavior**:
1. Detects preference for excluding categories
2. Confirms: lists both categories being excluded
3. Updates excluded-categories: [lateral-jump, memory-recall]
4. Remaining suggestions only come from: direct-follow-up, actionable-task, deep-dive, quick-win

**Pass Criteria**:
- [ ] PREFERENCES.md: excluded-categories=[lateral-jump, memory-recall]
- [ ] No 💡 Lateral or 📋 Memory Recall in subsequent suggestions
- [ ] Remaining slots redistribute among allowed categories
- [ ] display-count respected (same total shown)

---

## T23 — Cross-Channel State Sharing

**Scenario**: User switches from rich-text to terminal channel.

**Input Context**:
- PREFERENCES.md was configured in a rich-text channel (standard format)
- User now interacts via terminal/TUI channel
- PREFERENCES.md: display-count=5, format=standard

**Expected Behavior**:
1. Detects terminal channel
2. Overrides format to compact regardless of stored preference
3. Keeps display-count and all other preferences
4. Same category preferences, same backlog, same history

**Pass Criteria**:
- [ ] Format is compact (not standard) in terminal
- [ ] display-count still 5
- [ ] Category preferences unchanged from stored values
- [ ] PREFERENCES.md not modified (channel adaptation is runtime only)

---

## T24 — Reset Preferences

**Scenario**: User requests a full reset.

**Input Context**:
- PREFERENCES.md has customized settings: display-count=3, excluded-categories=[lateral-jump], deep-dive=STRONG
- User says: "Reset my next steps preferences"

**Expected Behavior**:
1. Detects reset intent
2. Confirms: "This will reset all NextSteps preferences to defaults. Your history will be preserved. Continue?"
3. After confirmation: resets User Configuration section to defaults
4. Resets all category preferences to MODERATE
5. Clears anti-preferences
6. Keeps HISTORY.md and BACKLOG.md intact

**Pass Criteria**:
- [ ] Confirmation prompt before reset
- [ ] PREFERENCES.md: display-count=5, excluded-categories=[], all categories=MODERATE
- [ ] HISTORY.md untouched
- [ ] BACKLOG.md untouched
- [ ] `[CONFIG-CHANGE] property: all, old: custom, new: defaults` logged

---

## T25 — Backlog Integration

**Scenario**: Backlog has relevant items.

**Input Context**:
- BACKLOG.md has: "OPEN - Add error handling to payment service (context: mentioned in session 3 days ago)"
- User is now working on the payment service
- PREFERENCES.md: include-backlog=true

**Expected Behavior**:
1. Detects context match between current work and backlog item
2. Includes the backlog item as a 📋 Memory Recall suggestion
3. Formats as: "📋 **Resume: Add error handling to payment service** — Started 3 days ago"

**Pass Criteria**:
- [ ] Backlog item surfaces as Memory Recall suggestion
- [ ] Only surfaces when contextually relevant
- [ ] Brief description and time context shown
- [ ] Not shown if include-backlog=false

---

## T26 — Lateral Suggestion Quality

**Scenario**: Ensure lateral suggestions are genuinely creative.

**Input Context**:
- User has been working on a REST API for a todo app
- Agent just implemented the CRUD endpoints

**Pass Criteria**:
- [ ] 💡 Lateral suggestion is NOT just another API endpoint
- [ ] Lateral introduces a new dimension (e.g., "Add WebSocket real-time sync for collaborative todo lists")
- [ ] Lateral is feasible, not absurd
- [ ] Lateral relates to the project but from a different angle

---

## T27 — Category Slot Allocation (display-count=3)

**Scenario**: Verify correct slot allocation with small count.

**Input Context**:
- PREFERENCES.md: display-count=3
- All categories MODERATE, no exclusions
- User just asked a coding question

**Expected Behavior** (per slot rules):
- 1 ⚡ Direct Follow-up (STRONG = guaranteed)
- 1 🔧 Actionable Task (STRONG = guaranteed)
- 1 from remaining MODERATE pool (round-robin)

**Pass Criteria**:
- [ ] Exactly 3 suggestions
- [ ] ⚡ present (guaranteed)
- [ ] 🔧 present (guaranteed)
- [ ] Third is from a MODERATE category
- [ ] No categories repeated

---

## T28 — Category Slot Allocation (display-count=7)

**Scenario**: Verify correct slot allocation with maximum count.

**Input Context**:
- PREFERENCES.md: display-count=7
- direct-follow-up=STRONG, actionable-task=STRONG, deep-dive=STRONG
- All others MODERATE

**Expected Behavior**:
- 3 guaranteed STRONG slots (1 per STRONG category)
- 4 remaining slots from MODERATE pool

**Pass Criteria**:
- [ ] Exactly 7 suggestions
- [ ] ⚡ Direct Follow-up present
- [ ] 🔧 Actionable Task present
- [ ] 🔍 Deep Dive present
- [ ] Remaining 4 from memory-recall, lateral-jump, quick-win (distributed)
- [ ] All 7 contextually relevant, no filler

---

## T29 — Self-Diagnostic at 20 Entries

**Scenario**: HISTORY.md reaches 20 entries since last diagnostic.

**Input Context**:
- HISTORY.md has 20 entries since last `[DIAGNOSTIC]`
- Selections: 8 total, 4 deep-dive, 3 actionable-task, 1 quick-win
- No selections for lateral-jump or memory-recall

**Expected Behavior**:
1. Triggers self-diagnostic
2. Calculates: overall rate 40%, deep-dive 50%, actionable 37.5%, quick-win 12.5%
3. Identifies gaps: lateral-jump and memory-recall at 0%
4. Logs `[DIAGNOSTIC]` entry

**Pass Criteria**:
- [ ] `[DIAGNOSTIC]` entry logged with statistics
- [ ] Categories with 0% identified
- [ ] Low-performing categories considered for demotion
- [ ] Diagnostic doesn't block normal next-steps generation

---

## T30 — Negative Feedback Handling

**Scenario**: User expresses dissatisfaction.

**Input Context**:
- User says: "These suggestions aren't helpful at all"

**Expected Behavior**:
1. Detects negative feedback
2. Triggers immediate self-diagnostic
3. Acknowledges feedback
4. Generates improved suggestions using diagnostic results

**Pass Criteria**:
- [ ] Feedback acknowledged (not ignored)
- [ ] Self-diagnostic triggered
- [ ] Next set of suggestions shows different pattern
- [ ] No defensive responses ("I'm sorry you feel that way")

---

## Running Tests

### Manual Testing
Execute each test by simulating the scenario in an OpenClaw-compatible agent with the nextsteps skill loaded. Verify each pass criterion checkbox.

### Automated Testing (tessl eval)
```bash
# Generate scenarios from the tile
tessl scenario generate -n 10 .

# Download and run
tessl scenario download --last
tessl eval run .
tessl eval view --last
```

### Coverage Matrix

| Area | Test Cases | Coverage |
|------|-----------|----------|
| Cold Start | T01 | Bootstrap, project scan, file creation |
| Happy Path | T02, T03 | Standard activation, format variations |
| Disable/Enable | T04, T05, T21 | State transitions |
| Customization | T06, T07, T22, T24 | Config changes, validation, reset |
| Anti-Patterns | T08, T09, T10 | Quality gates, generic filler, scope |
| Selection Tracking | T11, T12 | Selected, ignored |
| Security | T13, T14 | Credentials, .gitignore |
| Error Recovery | T15, T16, T17 | Token budget, missing files, corruption |
| Self-Improvement | T18, T19, T20, T29 | Overflow, promotion, demotion, diagnostic |
| Cross-Channel | T23 | Format adaptation |
| Backlog | T25 | Context-aware recall |
| Lateral Quality | T26 | Creative suggestion quality |
| Slot Allocation | T27, T28 | Category distribution at min/max counts |
| Feedback | T30 | Negative feedback handling |
