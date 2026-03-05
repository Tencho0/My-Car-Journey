---
name: positioning
description: "Define competitive positioning and differentiation strategy using a structured framework covering category, alternatives, differentiation, and proof points."
agent: @strategist
used-by: [@strategist, @brand-architect]
---

# Positioning Strategy

## Purpose

Define how the product is positioned in the market — what category it belongs to, how it's different from alternatives, and why the target customer should choose it. Positioning drives all messaging, branding, and go-to-market decisions.

## When to Use

- After lean canvas and value proposition are defined
- After competitor analysis is complete
- Before brand identity and messaging work
- When you need to sharpen "what makes us different"

## Framework

### The Positioning Statement

```
For [TARGET CUSTOMER]
who [STATEMENT OF NEED/PAIN],
[PRODUCT NAME] is a [CATEGORY]
that [KEY BENEFIT/DIFFERENTIATION].
Unlike [PRIMARY ALTERNATIVE],
our product [PRIMARY DIFFERENTIATOR]
because [PROOF POINTS/REASON TO BELIEVE].
```

### Positioning Canvas

```markdown
## 1. Category Definition
What category does this product belong to? How do customers think about this space?
- **Existing category to claim:** [e.g., "car expense tracker"]
- **Category reframe (optional):** [e.g., "car ownership intelligence platform"]
- **Why this category:** [does the customer already search for this?]

## 2. Competitive Alternatives
What would the customer do if your product didn't exist?
| Alternative | What It Does Well | Where It Falls Short |
|---|---|---|
| [Competitor 1] | | |
| [Competitor 2] | | |
| [Do nothing / status quo] | | |
| [Manual workaround] | | |

## 3. Unique Differentiation
What do you offer that no alternative does?
- **Differentiator 1:** [capability + why it matters]
- **Differentiator 2:** [capability + why it matters]
- **Differentiator 3:** [capability + why it matters]

Test: Can a competitor claim the same thing? If yes, it's not a differentiator.

## 4. Value Proof Points
Evidence that your differentiation is real, not just a claim.
| Claim | Proof |
|---|---|
| [Differentiator] | [Data, testimonial, demo, benchmark] |

Note: Pre-launch, proof points are limited. Plan what proof you'll gather post-launch.

## 5. Emotional Positioning
Beyond features — how should the product make users FEEL?
- **Current feeling** (without our product): [frustrated, confused, uncertain]
- **Desired feeling** (with our product): [in control, proud, competitive]

## 6. Positioning Tier Map
Where do you sit on key axes vs competitors?

| Axis | Low End | Our Position | High End |
|---|---|---|---|
| Cost tracking depth | Basic logging | → | Full ownership cost intelligence |
| Community/social | None | → | Gamified, competitive, social |
| Design quality | Dated/functional | → | Modern, car-culture aesthetic |
| Ecosystem integration | Isolated app | → | Connected (driver ↔ garage ↔ dealer) |
| Data insights | Your data only | → | Benchmarked against all users |

## 7. One-Line Positioning Statements
Write 3-5 variations of the core positioning, each emphasizing a different angle:
1. [Cost angle]
2. [Community angle]
3. [Insight/intelligence angle]
4. [Car culture/identity angle]
5. [Ecosystem angle]

Recommend the strongest one as the primary positioning.
```

## Instructions

1. **Read competitor-analysis.md, lean-canvas.md, and value-proposition.md** before starting.
2. **Be honest about differentiation.** "Better UI" is weak. "Only app that benchmarks your costs against other owners of the same car model" is strong.
3. **Category choice matters enormously.** Positioning as a "car expense tracker" puts you in a crowded, low-value category. Positioning as a "car ownership intelligence platform" creates a new frame. Choose carefully.
4. **Test differentiators with the "only" test.** Can you say "we are the ONLY product that..."? If yes, it's a real differentiator.
5. **Emotional positioning drives brand.** Rational features are copied easily. How the product makes people feel is harder to replicate.
6. **Write multiple one-liners.** The positioning statement will be used differently in the app store, landing page, social media, and pitch deck.
7. **Save output** to `/02-strategy/positioning-strategy.md`

## Quality Checklist

- [ ] Category is defined and justified
- [ ] At least 3 competitive alternatives are mapped (including "do nothing")
- [ ] Differentiators pass the "only" test — competitors can't claim the same
- [ ] Proof points exist or are planned for post-launch
- [ ] Emotional positioning is defined (before/after feeling)
- [ ] Multiple positioning statement variations are written
- [ ] Primary positioning is recommended with reasoning
- [ ] References competitor-analysis.md and value-proposition.md

## After This Skill

Suggest to the user:
- "Ready to define monetization? → `/plan-monetization`"
- "Want to start building the brand? → `/name-product`"
- "Should I draft the messaging framework based on this positioning? → use messaging-framework skill"
