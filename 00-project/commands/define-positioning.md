---
command: /define-positioning
description: "Define competitive positioning and differentiation strategy"
agent: @strategist
skills: [positioning]
output: /02-strategy/positioning-strategy.md
---

# /define-positioning — Positioning Strategy

Define how the product is different from alternatives and why the target customer should choose it.

## Invocation

```
/define-positioning
/define-positioning Car ownership intelligence platform for Bulgarian car enthusiasts
```

## Workflow

### Step 1: Load Context

Read these files:
1. `/01-discovery/research/competitor-analysis.md` — what exists today
2. `/02-strategy/lean-canvas.md` — business model
3. `/02-strategy/value-proposition.md` — pains and gains
4. `/01-discovery/customers/personas.md` — target user (if exists)
5. `/01-discovery/customers/interview-notes-summary.md` — real user language (if exists)

### Step 2: Discuss Strategic Choices

This is a strategic document that benefits from conversation. Ask the user:

1. **Category:** "How should customers think about us? Are we a 'car expense tracker', a 'car ownership platform', or something else entirely? The category we claim shapes everything."

2. **Primary angle:** "What's the ONE thing you want to be known for? Your cost insights, your car culture community, your beautiful timeline, or the connected ecosystem?"

3. **Emotional identity:** "When someone uses your app, how should they FEEL? In control? Proud? Competitive? Smart?"

If the user isn't sure, present 2-3 positioning options with tradeoffs and let them choose.

### Step 3: Generate the Positioning Strategy

Load skill: `/00-project/skills/positioning.md`

Apply the Positioning Canvas:
1. Category Definition (existing vs reframed)
2. Competitive Alternatives (including "do nothing")
3. Unique Differentiation (must pass the "only" test)
4. Value Proof Points (evidence or planned evidence)
5. Emotional Positioning (before/after feeling)
6. Positioning Tier Map (axes vs competitors)
7. One-Line Positioning Statements (3-5 variations)

### Step 4: Select Primary Positioning

Present the 3-5 positioning statement variations.
Recommend one as the primary, explain why.
Let the user choose — positioning is deeply personal for a founder.

### Step 5: Save

Save to `/02-strategy/positioning-strategy.md`
Log the positioning decision in `/00-project/decisions/DEC-XXX-positioning.md`
Update `/00-project/project-status.md`

## After Completion

Suggested next commands:
- `/plan-monetization` — pricing and revenue model
- `/name-product` — brand naming aligned to positioning
- `/plan-gtm` — go-to-market strategy

## Notes

- Positioning should be revisited after customer interviews — real user language often reshapes how you position
- The positioning statement will be used differently in: app store, landing page, pitch deck, social media
- If the founder is torn between two angles, both can be tested with different landing page headlines
