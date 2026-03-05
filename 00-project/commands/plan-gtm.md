---
command: /plan-gtm
description: "Create a complete go-to-market strategy covering beachhead, channels, messaging, launch plan, and metrics"
agent: @strategist
skills: [gtm-strategy]
output: /02-strategy/go-to-market-plan.md
---

# /plan-gtm — Go-To-Market Strategy

Define how the product reaches its first users — which segment, through which channels, with what message, and how to measure success.

## Invocation

```
/plan-gtm
/plan-gtm Focus on Bulgarian car Facebook groups and local car meets
/plan-gtm Budget is €200/month maximum
```

## Workflow

### Step 1: Load Context

Read these files:
1. `/02-strategy/positioning-strategy.md` — how we're positioned
2. `/02-strategy/monetization-plan.md` — pricing and revenue model
3. `/01-discovery/customers/personas.md` — target users (if exists)
4. `/01-discovery/research/competitor-analysis.md` — how competitors reach users
5. `/02-strategy/lean-canvas.md` — channels identified
6. `/04-brand/messaging-framework.md` — messaging (if exists)

### Step 2: Discuss Constraints

Ask the user:

1. **Budget:** "What's your monthly marketing budget? Be realistic — even €0 is fine, we'll plan around organic channels."

2. **Time commitment:** "How many hours per week can you spend on marketing/community work alongside development?"

3. **Existing network:** "Do you already have connections in the Bulgarian car community? Are you a member of any groups? Do you attend car meets?"

4. **Launch timing:** "When do you expect the MVP to be ready? We need to plan pre-launch audience building."

5. **Comfort zone:** "Are you comfortable being the public face of the product (posting, attending meets, going live)? Or do you prefer behind-the-scenes growth?"

### Step 3: Generate the GTM Strategy

Load skill: `/00-project/skills/gtm-strategy.md`

Apply the GTM Canvas — all 10 sections:
1. Beachhead Segment (specific enough to name actual communities)
2. Channel Strategy (with tactics, costs, impact, kill thresholds)
3. Messaging by Channel
4. Pre-Launch Plan (week by week)
5. Launch Plan (day by day)
6. Post-Launch Plan (weeks 1-4)
7. GTM Metrics (AARRR framework)
8. Budget Allocation
9. Risks & Mitigations
10. Decision Triggers (what success/failure looks like and what to do)

**Key rules while writing:**
- Be brutally specific about the beachhead — name actual Facebook groups, forums, Instagram pages
- Prioritize organic community channels over paid for a bootstrapped launch
- Every tactic needs a kill threshold — when to stop if not working
- Budget must match what the user said they can afford
- Time commitment must be realistic alongside development work
- Decision triggers prevent months of drifting without clear direction

### Step 4: Prioritize Channels

After generating, highlight the top 3 channels with the highest expected ROI:
- Explain why each is recommended
- Define the first concrete action for each (not "engage on social media" — what specifically to do this week)
- Set a 30-day checkpoint for each

### Step 5: Save

Save to `/02-strategy/go-to-market-plan.md`
Update `/00-project/project-status.md`

## After Completion

Suggested next commands:
- `/build-brand` — brand identity for consistent channel presence
- `/plan-growth` — design growth experiments
- "Want me to draft your first community posts?"
- "Should I create a pre-launch landing page? → after brand identity"
- "Want to design the beta testing program? → use beta-testing skill"

## Notes

- GTM strategy should be revisited after customer interviews — real user feedback reshapes channel priorities
- The beachhead is NOT your total market — it's your first 100-500 users. Be narrow.
- Pre-launch audience building should start 4-8 weeks before the app is ready
- The founder's personal network and community presence is the #1 channel for a bootstrapped startup
