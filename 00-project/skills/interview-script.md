---
name: interview-script
description: "Create a structured customer interview guide with warm-up, behavioral, pain, solution, and commitment questions. Based on The Mom Test methodology."
agent: @customer-analyst
used-by: [@customer-analyst]
---

# Customer Interview Script

## Purpose

Design a customer interview guide that uncovers real behaviors, pains, and willingness to act — not polite opinions. Based on The Mom Test: never tell people your idea, ask about their life instead.

## When to Use

- Before building anything (discovery interviews)
- When validating a pivot or new feature
- When entering a new market segment
- When customer understanding feels assumption-heavy

## Framework: The Mom Test Rules

1. **Talk about their life, not your idea.** Bad: "Would you use an app that..." Good: "How do you currently track..."
2. **Ask about specifics in the past, not generics about the future.** Bad: "Would you pay for this?" Good: "What's the last app you paid for? Why?"
3. **Talk less, listen more.** Your job is to shut up after asking the question.
4. **Seek emotional signals.** Frustration, surprise, excitement = real signal. "Yeah, that sounds cool" = noise.
5. **Get commitment or advancement.** At the end, ask for something: a follow-up, an introduction, early access signup. If they won't commit to anything, they weren't that interested.

## Template

```markdown
# Customer Interview Guide: [Topic/Segment]

**File:** `/01-discovery/customers/interview-guide.md`
**Produced by:** @customer-analyst
**Date:** [date]
**Target segment:** [who you're interviewing]
**Goal:** [what you're trying to learn]

---

## Interview Setup

**Duration:** 15-20 minutes
**Format:** In person, phone, or video call
**Recording:** Ask permission to take notes (not record unless they agree)
**Golden rules:**
- Don't pitch your idea until the very end (if at all)
- Ask open-ended questions, then be quiet
- Follow up with "why?" and "tell me more"
- Pay attention to emotions, not just words

---

## Questions

### Warm-Up (2 min)
Purpose: Get them comfortable and talking.
- [Contextual opener about their situation]
- [Follow-up that reveals what they value]

### Current Behavior (5 min)
Purpose: Understand what they do TODAY. Past behavior predicts future behavior.
- [How do you currently handle X?]
- [When was the last time you did Y? Walk me through it.]
- [What tools/methods do you use?]

### Pain Discovery (5 min)
Purpose: Find real frustrations, not hypothetical ones.
- [What's the most annoying thing about X?]
- [Tell me about a time when X went wrong.]
- [What have you tried to fix this? What happened?]

### Value Testing (3 min)
Purpose: Test whether your proposed value matters to them.
- [If you could see/know/do X, would that change anything?]
- [How would you use that information?]
- [What would you do differently?]

### Commitment (2 min)
Purpose: Test real interest, not politeness.
- [Would you be willing to try an early version and give feedback?]
- [Can you introduce me to someone else who has this problem?]
- [Can I follow up in two weeks to show you what we've built?]

---

## After Each Interview

### Signal Scoring (rate 1-5)

| Signal | Score | Notes |
|---|---|---|
| Problem awareness | /5 | Do they recognize the problem? |
| Current behavior | /5 | Are they actively trying to solve it? |
| Emotional intensity | /5 | Frustration? Excitement? Or just "meh"? |
| Willingness to act | /5 | Would they try a solution? Pay? Refer? |
| Frequency of need | /5 | How often does this problem affect them? |

### Key Quotes
- "[Exact words they used — these become marketing copy]"

### Surprises
- [Anything unexpected that challenges your assumptions]

---

## Red Flags
- "Yeah, that sounds cool" (polite, not passionate)
- Can't describe a specific time the problem affected them
- "I'd use it if it was free" (no willingness to pay)
- Never tried to solve the problem (low pain)

## Green Lights
- Describes workarounds (spreadsheets, notes, asking friends)
- Emotional reaction (frustration, surprise at their own spending)
- Asks when they can try it
- Offers to refer friends
- Mentions specific dollar amounts they've been surprised by
```

## Instructions

1. **Tailor every question to the specific segment and context.** Generic questions get generic answers.
2. **Include competitor probing.** If competitors exist (Fuelio, Drivvo), ask if they've tried them and what they thought.
3. **Write questions in natural, conversational language.** These are conversations, not surveys.
4. **Plan for silence.** After each question, note: "Wait. Let them think. Don't fill the silence."
5. **Include the scoring template.** Consistent scoring across interviews reveals patterns.
6. **Save output** to `/01-discovery/customers/interview-guide.md`

## Quality Checklist

- [ ] Questions follow The Mom Test (no leading, no pitching)
- [ ] Questions cover: current behavior, past specifics, pain depth, value testing, commitment
- [ ] Competitor awareness questions are included
- [ ] Scoring template is included for consistency
- [ ] Red flags and green lights are defined
- [ ] Questions are conversational, not formal
- [ ] Guide is usable in 15-20 minutes (not too many questions)

## After This Skill

Suggest to the user:
- "Go conduct 15-20 interviews, then come back with your notes."
- "After interviews, use `/analyze-interviews` to find patterns."
- "Want me to help you find communities to recruit interviewees from?"
