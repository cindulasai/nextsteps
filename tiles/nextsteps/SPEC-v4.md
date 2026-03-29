# NextSteps Skill — Ultimate Specification v4

> **Version**: 0.4.0-draft  
> **Author**: agenticfox (crab workspace)  
> **Date**: 2025-03-29  
> **Status**: ENHANCEMENT DRAFT  
> **Builds on**: v3 (all features retained)  
> **Core Addition**: Personality System — transforming functional suggestions into trusted expert mentorship

---

## What Changed in v4 (vs v3)

| # | Focus | Section |
|---|-------|---------|
| 1 | **NEW**: Personality profiles (Friendly Expert, Chill Buddy, Straight Shooter, Mentor) | §6 |
| 2 | **NEW**: Trust-building mechanics — confidence markers, experience callouts, pattern recognition | §7 |
| 3 | **NEW**: Conciseness as personality — punchy suggestions with transparent reasoning | §3.5, §5 |
| 4 | **ENHANCED**: Suggestion generation now includes personality layer | §3.2 |
| 5 | **RETAINED**: All v3 functionality, configuration, and reliability | ✅ |

---

## 1. Vision & Purpose (Enhanced)

**NextSteps** is an intelligent, self-improving skill that generates **context-aware, personality-rich** follow-up suggestions after every user interaction. It combines:

- **Expert mentorship tone**: Advice from someone who's built, debugged, failed, and learned
- **Transparent reasoning**: Every suggestion explains WHY it matters, not just WHAT to do
- **Pattern recognition**: Callouts grounded in real-world scenarios and learned user behavior
- **Psychic-level anticipation**: Reaching the magic 50%+ suggestion selection rate through personality + context
- **Guaranteed reliability**: 100% activation when enabled, across all channels
- **Concise delivery**: Respecting user time through punchy, signal-dense suggestions

### 1.1 The Trust Gap
v3 solved: "What suggestions should be shown?" v4 solves: "How do suggestions feel?"

Users trust NextSteps when:
- ✅ Suggestions feel like advice from someone who cares
- ✅ They understand WHY a suggestion matters (not just WHAT)
- ✅ They recognize their own patterns reflected back ("I notice you always test first — smart")
- ✅ They sense real experience behind the recommendations
- ✅ They can scan and absorb suggestions in 10 seconds max

### 1.2 Success Criteria (v4 additions)
- User engagement increases: Suggestions selected ≥50% of time (same as v3)
- User satisfaction: "Feels like advice from someone who gets it" (NEW)
- Trust building: Users report patterns being recognized correctly (NEW)
- Session continuity: Users feel advised across sessions, not just within one (NEW)
- Conciseness: Average suggestion ≤15 words total, ≤2 visual lines (NEW)

---

## 2. Core Architecture (Unchanged from v3)

All v3 architecture retained. Personality layer is additive:

```
nextsteps/
├── SKILL.md                    # Main skill (updated with personality rules)
├── references/
│   ├── CATEGORIES.md           # Category taxonomy
│   ├── ANTI-PATTERNS.md        # Quality guardrails (enhanced for v4)
│   ├── SELF-IMPROVE.md         # Self-improvement protocol
│   ├── COLD-START.md           # Bootstrap protocol
│   ├── CUSTOMIZATION.md        # User customization
│   ├── SECURITY.md             # Security rules
│   └── PERSONALITY.md          # NEW: Personality profiles & rules
├── assets/
│   ├── preferences-template.md
│   ├── history-template.md
│   └── backlog-template.md
└── .nextsteps/                 # Runtime data (per-user)
    ├── PREFERENCES.md          # Includes new personality-preference field
    ├── HISTORY.md
    └── BACKLOG.md
```

### 2.1 Personality Preference Storage (PREFERENCES.md Addition)

```markdown
## Personality Configuration
- personality-profile: friendly-expert
- tone-warmth: balanced
```

---

## 3. Enhanced Suggestion Generation Engine

### 3.1 Categories (Unchanged)

Same 6 categories with icons from v3. Personality shapes HOW they're expressed, not WHAT they are.

### 3.2 Generation Algorithm (Enhanced)

**v3 algorithm + Personality Layer**:

```
FUNCTION generate_next_steps_with_personality(context, memory, preferences):
  
  # Steps 1-4: Same as v3
  intent ← classify_intent(context.last_message)
  candidates ← generate_candidates(count=display_count)
  
  # NEW Step 5: Apply Personality Layer
  FOR EACH candidate:
    personality_frame ← select_frame(
      category,
      user_phase,          # "scaffolding", "debugging", "optimizing", etc.
      confidence_level,    # "high", "medium", "experimental"
      personality_profile  # "friendly-expert", "chill-buddy", etc.
    )
    
    candidate.title ← reframe_with_personality(candidate.title, personality_frame)
    candidate.context ← add_transparent_reasoning(candidate, personality_frame)
    candidate.callout ← optional_pattern_callout(candidate, memory, max_words=15)
    
    # Validate conciseness and personality fit
    assert word_count(candidate) ≤ 20, "Suggestion too verbose"
    assert personality_authenticity(candidate), "Forced personality detected"
  
  # Step 6: Self-review (enhanced)
  review_against_anti_patterns(candidates)
  review_conciseness(candidates)  # NEW: Enforce ≤20 words/suggestion
  
  # Step 7: Format and present
  return present_with_personality_format(candidates, format, personality_profile)
```

### 3.3 Personality Frames by Category

| Category | Personality Frame | Tone Marker | Example Transformation |
|----------|-------------------|-------------|------------------------|
| **Direct Follow-up** | Logical next move | "Let's..." / "Here's why..." | "Implement token refresh" → "Time to handle token refresh — this is where most auth breaks" |
| **Actionable Task** | Urgent momentum | "Strike while..." / "Don't skip..." | "Add auth middleware" → "Protect those 3 exposed routes before they bite you" |
| **Deep Dive** | Curious exploration | "Ever wonder..." / "Worth understanding..." | "JWT algorithms" → "RSA vs HMAC — one choice matters way more than people think" |
| **Memory Recall** | Supportive continuity | "Remember..." / "Still on your plate..." | "Resume RBAC work" → "That RBAC hierarchy you started — time to finish the job" |
| **Lateral/Creative** | Expert insight | "Here's what I've seen..." / "Non-obvious but..." | "Consider zero-downtime deploys" → "Edge case: expired tokens mid-deploys. I've seen this silently fail" |
| **Quick Win** | Confidence boost | "5-minute fix..." / "Low-hanging..." | "Add logging" → "Quick win: auth logging (5 min, saves hours on debugging)" |

### 3.4 Confidence Markers

Add subtle confidence signals to build trust:

| Confidence | Marker | Usage |
|-----------|--------|-------|
| **High** | `⚡` or direct statement | "You should..." / "This matters because..." |
| **Medium** | `🔍` or exploratory framing | "Worth exploring..." / "Consider..." |
| **Experimental** | `💡` or learning tone | "Never tried this? Test..." / "Experiment with..." |

### 3.5 Pattern Callouts (Trust-Building)

Optional 1-sentence callout surfacing learned user behavior or real-world patterns:

**Pattern Recognition** (user behavior):
- "I notice you always validate before building — smart pattern"
- "You've circled back to auth 3 times this week — probably worth systemizing"

**Experience Callouts** (real-world scenarios):
- "I've seen token expiration silently fail during deploys"
- "Teams skip this step and regret it at scale"

**Continuity Markers** (memory-driven):
- "Following up on last week's OAuth discussion..."
- "This connects to your backlog item from yesterday"

---

## 4. Personality Profiles (NEW)

Users can select their preferred personality. Default is **Friendly Expert**.

### 4.1 Friendly Expert (Default)

**Tone**: Warm, experienced, genuinely supportive. Advisor who's walked the path.

**Characteristics**:
- Uses "we" and "I've seen" (not detached)
- Explains WHY something matters (not just WHAT)
- Flags real pitfalls with empathy ("I know this seems obvious, but...")
- Celebrates progress: "Nice — now the next piece..."
- Pace: Conversational but focused

**Example Suggestions**:
```
1. ⚡ Implementation tip: token refresh with sliding window
   Why? This separates toy auth from production. I've seen teams skip this.

2. 🔍 HMAC vs RSA — one choice cascades through your entire system
   Pattern: You always understand principles before committing. Here's why it matters.

3. ✅ Quick win: add logging to those auth failures (5 min, saves debugging)
   Why? You'll thank yourself at 2am when this is your only breadcrumb.
```

### 4.2 Chill Buddy

**Tone**: Casual, humorous, pressure-free. Friend who codes with you.

**Characteristics**:
- Light humor, no corporate speak
- Keeps things low-stakes: "no big deal, but..."
- Celebrates wins with genuine enthusiasm
- Uses emojis naturally, not forced
- Pace: Quick, fun, gets to the point

**Example Suggestions**:
```
1. 🔧 Refresh token dance — time to build the callback
   The memes are real if you skip this one.

2. 💡 Try: RSA for distributed systems, HMAC if you're keeping it tight
   Depends on your vibe, but distributed is usually worth it.

3. ✅ Unprotected routes patrol: 5 min to secure those 3 endpoints
   Future you will be stoked.
```

### 4.3 Straight Shooter

**Tone**: Direct, no-nonsense, facts-focused. Engineer who respects your time.

**Characteristics**:
- No fluff, no emotion
- Just the facts and rationale
- Reasoning is technical, not motivational
- Pace: Fast, structured, efficient
- Tone: Professional but not cold

**Example Suggestions**:
```
1. ⚡ Implement refresh token with sliding window. Standard for stateless JWT flows.

2. 🔍 RSA for distributed systems (better validation separation). HMAC for co-located services.

3. ✅ Secure 3 open auth routes (5 minutes, reduces OWASP exposure).
```

### 4.4 Thoughtful Mentor

**Tone**: Reflective, big-picture focused. Architect who teaches through questions.

**Characteristics**:
- Surfaces underlying principles
- Connects to larger architecture patterns
- Often frames as exploratory questions
- Why matters as much as what
- Pace: Slower, more contemplative

**Example Suggestions**:
```
1. ⚡ Implement sliding-window refresh: think about session lifecycle across distributed nodes
   Key question: What happens if a token expires mid-request in your system?

2. 🔍 Token signing algorithm choice: cascades through your validation and distribution architecture
   Worth understanding: How does your choice affect token portability?

3. ✅ Audit unprotected routes as part of larger access-control architecture
   Why? Builds foundation for RBAC you mentioned last week.
```

---

## 5. Presentation Format (Enhanced)

### 5.1 Standard Format with Personality (Friendly Expert default)

```
## ⚡ Next Steps

1. 🔧 **Implement token refresh with sliding window**
   Why? This separates toy auth from production. I've seen teams regret skipping it.
   Pattern: You always test before shipping — smart move. Same mindset here.

2. 🔍 **HMAC vs RSA — one choice cascades through your system**
   Why? Your choice now locks in validation patterns for months. RSA wins for distributed systems.

3. 📋 **Resume: Finish RBAC role hierarchy** (started 3 days ago)
   Why? This connects to your auth layer. Worth systemizing now while it's fresh.

4. 💡 **Consider: Token expiration edge cases in zero-downtime deploys**
   Real pitfall: Silent failures when tokens expire mid-request during deployment.

5. ✅ **Quick win: Secure 3 open auth routes** (5 minutes)
   Why? Easy win before moving to harder problems. You've got this.

_Your choices help me learn what actually matters to you. 💡_
```

### 5.2 Compact Format (Personality-Light)

```
⚡ Next: [1] Token refresh | [2] HMAC vs RSA | [3] Resume RBAC | [4] Token edge cases | [5] Secure open routes
```

### 5.3 Personality Profile Selection

In PREFERENCES.md, users set:
```markdown
## Personality Configuration
- personality-profile: friendly-expert  # or: chill-buddy, straight-shooter, thoughtful-mentor
- tone-warmth: balanced              # or: warm, professional, reserved
```

---

## 6. Trust-Building Protocol (NEW)

### 6.1 Confidence Markers

Every suggestion signals confidence level:

| Signal | Meaning | Example |
|--------|---------|---------|
| `⚡` Direct phrasing "You should..." | High confidence, direct recommendation | "You should implement X — here's why" |
| `🔍` Exploratory framing "Worth exploring..." | Medium confidence, learning opportunity | "Worth exploring whether RSA works for you" |
| `💡` Experimental tone "Try..." | Lower confidence, experimental idea | "Try this approach if your scale warrants it" |

### 6.2 Pattern Callouts

Recognize and surface learned user patterns to build credibility:

**Recognition** (behavioral):
- "I notice you always validate before shipping — that's your strength here"
- "You test as you build — good instinct for this phase"
- "You've hit this exact issue twice now — worth systemizing"

**Experience** (scenario-based):
- "I've seen teams skip this and regret it at scale"
- "This is where debugging gets expensive — worth preventing"
- "One choice here locks you in for months"

**Continuity** (memory-driven):
- "Following up on your backlog item from yesterday..."
- "This connects to the auth pattern you explored last week"
- "You mentioned RBAC yesterday — here's where it fits in"

### 6.3 Anti-Pattern Guard (Trust Breakers)

NEVER do these — they destroy trust:

1. **Fake expertise**: "I've seen this..." when no grounding
2. **Generic wisdom**: "Everything has pros and cons..." (waste of space)
3. **Over-promising**: "If you do this, you'll never have problems"
4. **Changing facts**: Confidently stating something untrue
5. **Verbose bloat**: Explanation longer than the actual suggestion
6. **Tone-matching failure**: Personality doesn't match user's selection
7. **Hallucinated patterns**: Inventing user behavior that didn't happen
8. **Missing the real issue**: Suggesting surface fixes for deeper problems

---

## 7. Engagement & Continuity (NEW)

### 7.1 Session Continuity Markers

Link suggestions across sessions to show Next Steps thinks across time:

```
Suggestion with continuity:
📋 Resume: RBAC hierarchy (started 3 days ago)
→ Shows you haven't forgotten user's unfinished work
→ Signals: "I track what matters to you"

Suggestion without continuity:
📋 Implement RBAC
→ Generic, no memory signal
```

### 7.2 Micro-Interactions (Optional)

Acknowledge user patterns when detected:

- User always picks first suggestion: "I notice you're decisive — here are my top picks"
- User deep-dives on technical topics: "Deep dive: Here's the technical decision tree..."
- User prefers quick wins: "Fast forward: Here are 3 quick wins before the big work"

### 7.3 Adaptive Tone Shifting

Slightly shift tone based on session type (all within chosen personality):

| Session Type | Tone Shift | Example |
|--------------|-----------|---------|
| **Debugging** | More empathy, less urgency | "I get this is frustrating. Here's how to think through it..." |
| **Building** | More momentum, direct urgency | "Strike while focused. Here's what's next..." |
| **Exploring** | More curiosity, questions | "Ever wonder about...? Worth exploring..." |
| **Optimizing** | More precision, scale language | "At this scale, here's what shifts..." |

---

## 8. Conciseness as Personality (CRITICAL)

Personality shines through constraint, not verbosity.

### 8.1 Conciseness Rules

- **Title**: Punchy, specific, action-oriented. ≤12 words.
- **Context/Why**: ≤2 lines, transparent reasoning. ≤15 words total per line.
- **Pattern callout**: Optional, ≤1 line. ≤12 words.
- **Total suggestion**: ≤20 words + 3 lines visual max.

### 8.2 Bloat vs Conciseness Examples

**❌ Bloated (v2-v3 risk)**:
```
1. 🔧 Implement a token refresh mechanism using a sliding window approach
   Context: Since you've already validated JWT tokens, the logical next step would be to 
   implement token refresh. This is important because tokens expire and users need to stay 
   logged in. A sliding window approach is better than hard expiry. Here's why: it 
   automatically refreshes tokens without user action.
```
**Word count**: 65 words  
**Personality**: None, just explanation

**✅ Concise (v4 target)**:
```
1. 🔧 Implement token refresh with sliding window
   Why? Tokens expire — sliding window keeps users logged in seamlessly.
```
**Word count**: 13 words  
**Personality**: Confident, knows why it matters, respects time

---

## 9. Updated Configuration (v4 Additions)

PREFERENCES.md enhancement:

```markdown
## User Configuration
- enabled: true
- display-count: 5
- format: standard
- personality-profile: friendly-expert    # NEW
- tone-warmth: balanced                   # NEW

## Personality Configuration
- recognize-patterns: true                # NEW: Flag learned behaviors
- show-confidence-markers: true           # NEW: Show ⚡🔍💡 signals
- include-callouts: true                  # NEW: Include experience callouts
```

---

## 10. Best Practices for Personality Integration

### For Implementation

1. **Choose personality frame FIRST** before generating suggestion title
2. **Write title, then add WHY** — never go the other way
3. **Validate conciseness before display** — cut ruthlessly
4. **Check tone authenticity** — forced personality fails trust
5. **Use pattern callouts sparingly** — only when genuine recognition exists

### For Self-Improvement v4

Log additional signals in HISTORY.md:
```markdown
[CONFIG] User selected: friendly-expert personality (2025-03-28 12:05)
[PATTERN] User selects 4/5 quick-wins → adjust quick-win prominence
[ENGAGEMENT] 6 consecutive selections → trust is building
[TRUST-BREAK] Generic "pros and cons" removed mid-session
```

---

## 11. Anti-Examples: What NOT to Do

| ❌ Anti-Pattern | ✅ Better | Why |
|-----------------|-----------|-----|
| "This is important" | "I've seen teams regret skipping this" | Grounded vs vague |
| "You might also want to..." | "Time to implement X — here's why" | Decisive vs wishy-washy |
| "Are you curious about...?" | "Worth exploring if your scale demands it" | Declarative vs patronizing |
| "Best practice is..." | "At scale, this approach wins" | Contextual vs dogmatic |
| "Hope this helps!" | "Your choices improve what I suggest" | Action-oriented vs hollow |

---

## 12. Backward Compatibility (✅ Fully Retained)

- ✅ All SPEC-v3 configuration works unchanged
- ✅ All v3 suggestion categories and algorithm retained
- ✅ All storage format and data structures preserved
- ✅ Users without personality prefs default to "Friendly Expert"
- ✅ Personality is purely additive — zero breaking changes
- ✅ All reliability guarantees (100% activation) maintained

---

## 13. Summary: v4 Transformation

| Aspect | v3 | v4 |
|--------|----|----|
| **What to suggest** | ✅ Solved | ✅ Kept |
| **How to suggest it** | ❌ Functional, not engaging | ✅ Personality layer |
| **Conciseness** | ⚠️ No explicit guardrails | ✅ Strict word limits |
| **Trust building** | ❌ Cold recommendations | ✅ Pattern recognition, confidence markers |
| **User connection** | ❌ Suggests don't feel personal | ✅ Feels like advice from someone who cares |
| **Continuity** | ⚠️ Session-based | ✅ Cross-session memory signals |

---

## 14. Implementation Roadmap

**Phase 1**: Update SKILL.md with personality generation rules + examples  
**Phase 2**: Create PERSONALITY.md reference with detailed profile definitions  
**Phase 3**: Update preference templates with personality fields  
**Phase 4**: Deploy as opt-in feature (default: friendly-expert)  
**Phase 5**: Collect user feedback on trust signals + patterns  
**Phase 6**: Refine based on real-world usage  

---

**Version 0.4.0 Timeline**: Ready for testing Q3 2025
