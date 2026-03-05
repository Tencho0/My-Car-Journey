---
name: gtm-strategy
description: "Create a go-to-market strategy covering beachhead segment, channels, messaging, launch plan, and success metrics."
agent: @strategist
used-by: [@strategist, @growth-lead]
---

# Go-To-Market Strategy

## Purpose

Define how the product reaches its first users — which segment to target first, through which channels, with what message, and how to measure success. A GTM strategy turns a product into a business.

## When to Use

- After positioning and monetization are defined
- Before building the landing page or any marketing materials
- When planning the launch timeline
- When deciding where to spend limited marketing resources

## Framework

### GTM Canvas

```markdown
# Go-To-Market Strategy

**File:** `/02-strategy/go-to-market-plan.md`
**Produced by:** @strategist
**Date:** [date]

## References
- Positioning: `/02-strategy/positioning-strategy.md`
- Monetization: `/02-strategy/monetization-plan.md`
- Personas: `/01-discovery/customers/personas.md`
- Competitor Analysis: `/01-discovery/research/competitor-analysis.md`

---

### 1. Beachhead Segment

**Who are your first 100 users?**
Not "car enthusiasts in Bulgaria" — much more specific:
- [Exact description: age, location, car type, online behavior, what group they're in]
- [Where they hang out — specific Facebook groups, forums, Instagram pages, car meets]
- [Why THEY specifically will adopt first — what makes them more likely than others]

**Why this beachhead:**
- [Access — can you reach them?]
- [Pain intensity — is the problem acute for them?]
- [Influence — will they bring others?]

### 2. Channel Strategy

| Channel | Tactic | Cost | Expected Impact | Timeline |
|---------|--------|------|-----------------|----------|
| [channel] | [specific action] | [€/free] | 🟢🟡🔴 | [when] |

**Primary channels (80% of effort):**
1. [Channel with highest expected ROI]
2. [Channel with best access to beachhead]

**Secondary channels (20% of effort):**
3. [Testing / experimental]
4. [Long-term investment]

For each channel, define:
- **Specific tactic** (not just "social media" — which platform, what content, what frequency)
- **Content/asset needed** (what you must create)
- **Metric to track** (how you know it's working)
- **Kill threshold** (when to stop if it's not working)

### 3. Messaging by Channel

| Channel | Primary Message | CTA |
|---------|----------------|-----|
| App Store | [headline + key benefit] | Download |
| Landing page | [pain point → solution] | Join waitlist |
| Facebook car groups | [peer-level, not salesy] | Check this out |
| Car meets | [in-person demo hook] | Try it now |
| Instagram/TikTok | [visual, shareable] | Follow / Download |

### 4. Pre-Launch Plan

**Weeks -8 to -4 (Building audience):**
| Week | Action | Goal |
|------|--------|------|
| -8 | [action] | [measurable goal] |
| ... | | |

**Weeks -4 to -1 (Building hype):**
| Week | Action | Goal |
|------|--------|------|

### 5. Launch Plan

**Launch week day-by-day:**
| Day | Action | Channel | Owner |
|-----|--------|---------|-------|
| Day 1 | [action] | [channel] | [who] |
| ... | | | |

### 6. Post-Launch (Weeks 1-4)

| Week | Focus | Key Metric |
|------|-------|------------|
| 1 | [focus area] | [metric] |
| 2 | | |
| 3 | | |
| 4 | | |

### 7. GTM Metrics

| Stage | Metric | Target (Month 1) | Target (Month 3) |
|-------|--------|-------------------|-------------------|
| Awareness | [reach, impressions] | | |
| Acquisition | [downloads, signups] | | |
| Activation | [first action taken] | | |
| Retention | [came back in 7/30 days] | | |
| Revenue | [premium conversion] | | |
| Referral | [invited others] | | |

### 8. Budget Allocation

| Category | Monthly Budget | Notes |
|----------|---------------|-------|
| Paid ads | €[amount] | [where] |
| Content creation | €[amount] | [what] |
| Events/meets | €[amount] | [which] |
| Influencer/partnerships | €[amount] | [who] |
| Tools | €[amount] | [what] |
| **Total** | **€[amount]** | |

### 9. Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| [risk] | [what happens] | [how to prevent/respond] |

### 10. Decision Triggers

| Milestone | If achieved → | If NOT achieved → |
|-----------|--------------|-------------------|
| 500 downloads in Month 1 | Scale primary channel spend | Revisit messaging and channels |
| 25% 30-day retention | Begin Phase 2 features | Focus on activation/onboarding fixes |
| <5% 30-day retention | — | Consider pivot or fundamental rethink |
```

## Instructions

1. **Read positioning, monetization, personas, and competitor analysis** before starting.
2. **Be brutally specific about the beachhead.** "Car enthusiasts in Bulgaria" is too broad. "Members of the 'BMW Club Bulgaria' Facebook group who post about modifications" is a beachhead.
3. **Prioritize channels by access and cost.** As a solo bootstrapped founder, organic community engagement beats paid advertising.
4. **Every tactic needs a kill threshold.** If it's not working after X weeks or €Y spent, stop and try something else.
5. **Budget must be realistic.** If the founder has €200/month for marketing, plan for €200/month.
6. **Decision triggers prevent drift.** Define upfront what success and failure look like, and what you'll do in each case.
7. **Save output** to `/02-strategy/go-to-market-plan.md`

## Quality Checklist

- [ ] Beachhead is specific enough to name individual communities/groups
- [ ] Channel strategy has specific tactics, not just channel names
- [ ] Every channel has a metric and kill threshold
- [ ] Pre-launch and launch plans are week-by-week/day-by-day
- [ ] Budget is realistic for a bootstrapped solo founder
- [ ] Decision triggers are defined for both success and failure scenarios
- [ ] References positioning, monetization, and persona documents

## After This Skill

Suggest to the user:
- "Ready to build the landing page? → `/build-brand` then create landing page"
- "Want to design growth experiments? → `/plan-growth`"
- "Should I create the community engagement strategy? → use community-strategy skill"
