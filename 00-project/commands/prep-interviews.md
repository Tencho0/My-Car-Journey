---
command: /prep-interviews
description: "Create a customer interview guide tailored to specific segments and research goals"
agent: @customer-analyst
skills: [interview-script]
output: /01-discovery/customers/interview-guide.md
---

# /prep-interviews — Customer Interview Preparation

Design a customer interview guide that uncovers real behaviors and pains, not polite opinions.

## Invocation

```
/prep-interviews
/prep-interviews Bulgarian car enthusiasts aged 20-40
/prep-interviews I want to validate whether people would track expenses in an app
/prep-interviews update — I need to add questions about competitor apps
```

## Workflow

### Step 1: Load Context

Read these files:
1. `/00-project/concept-document.md` — what we're building
2. `/01-discovery/research/competitor-analysis.md` — competitor context for probing questions
3. `/02-strategy/lean-canvas.md` — key assumptions to validate (if exists)
4. `/02-strategy/value-proposition.md` — pains and gains to explore (if exists)
5. `/01-discovery/customers/interview-guide.md` — existing guide to update (if exists)

### Step 2: Define Interview Goals

Ask the user:

1. **Who are you interviewing?** "Which specific segment? Where will you find them?"

2. **What's the #1 thing you need to learn?** "If you could answer one question from these interviews, what would it be?"

3. **What assumptions scare you most?** "Which assumption, if wrong, would kill the business?"

4. **Have you done interviews before?** "First time, or updating an existing guide?"

If an interview guide already exists, ask: "What's changed since the last guide? What new questions do you need?"

### Step 3: Generate the Guide

Load skill: `/00-project/skills/interview-script.md`

Structure the guide:
- Interview setup (duration, format, golden rules)
- Warm-up questions (2 min)
- Current behavior questions (5 min)
- Pain discovery questions (5 min)
- Value testing questions (3 min)
- Commitment questions (2 min)
- Post-interview scoring template
- Red flags and green lights

**Tailor to context:**
- Include competitor probing ("Have you tried Fuelio? What did you think?")
- Include Bulgarian-specific context where relevant
- Include questions that test the riskiest assumptions from the lean canvas
- Use conversational Bulgarian-friendly language

### Step 4: Add Recruitment Help

Provide:
- Suggested outreach message for recruiting interviewees (for Facebook groups, forums, etc.)
- Tips for where to find the target segment
- How to explain the interview request without biasing responses

### Step 5: Save

Save to `/01-discovery/customers/interview-guide.md`
Update `/00-project/project-status.md`

## After Completion

Suggested next actions:
- "Go conduct 15-20 interviews. Take notes immediately after each one."
- "Save raw notes in `/01-discovery/data/interviews/interview-NNN-raw.md`"
- "After interviews, come back and use `/analyze-interviews` to find patterns."

## Update Mode

If invoked with `/prep-interviews update`:
1. Read the existing interview guide
2. Ask what's changed or what new questions are needed
3. Add new questions or modify existing ones
4. Preserve the scoring template and structure

## Notes

- The Mom Test principle: never ask "would you use this?" — ask about their life instead
- 15-20 interviews is the sweet spot — enough for patterns, not so many that you're avoiding building
- Interview quality matters more than quantity — 10 great conversations beat 25 shallow ones
