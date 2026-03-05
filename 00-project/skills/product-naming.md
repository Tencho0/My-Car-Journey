---
name: product-naming
description: "Brainstorm and evaluate product name options using a structured scoring framework, considering linguistic, cultural, and market factors."
agent: @brand-architect
used-by: [@brand-architect]
---

# Product Naming

## Purpose

Generate and evaluate product name candidates that resonate with the target audience (Bulgarian car enthusiasts), work across languages for future expansion, and are available as domains and app store listings.

## When to Use

- At the start of brand development
- Before building the landing page or app store listing
- Before any public-facing marketing

## Framework

### Name Generation Categories

Generate 3-5 candidates in each category:

1. **Descriptive** — directly describes what the product does
   (e.g., CarCost, DriveTracker, AutoLedger)

2. **Metaphorical** — uses a metaphor related to cars, journeys, or clarity
   (e.g., Odometer, Pitstop, ClearRoad)

3. **Invented/Coined** — new words that sound right and are highly ownable
   (e.g., Vehlo, Drivu, Carvio)

4. **Compound** — combines two meaningful words
   (e.g., FuelBook, CarPulse, DriveCost)

5. **Action-oriented** — implies what the user does
   (e.g., TrackMyRide, KnowYourCar, OwnIt)

### Evaluation Criteria

Score each name 1-5 on:

| Criterion | Weight | What to Evaluate |
|---|---|---|
| **Memorability** | High | Can someone remember it after hearing it once? |
| **Pronunciation** | High | Easy to say in Bulgarian AND English? No awkward sounds? |
| **Meaning/Association** | High | Does it evoke cars, tracking, clarity, community? Any negative associations in Bulgarian? |
| **Domain availability** | High | Is .com or .app available? Close alternatives? |
| **App store availability** | High | No existing app with the same or confusingly similar name? |
| **Scalability** | Medium | Works beyond Bulgaria? Doesn't limit future modules (B2B, data, etc.)? |
| **Visual potential** | Medium | Can you imagine a clean logo/icon? Works as an app icon? |
| **Uniqueness** | Medium | Distinguishable from competitors (Fuelio, Drivvo, Simply Auto)? |
| **Length** | Low | Ideally 2-3 syllables, max 10 characters |

### Scoring Matrix

```markdown
| Name | Memorability | Pronunciation | Meaning | Domain | App Store | Scalability | Visual | Uniqueness | Length | TOTAL |
|------|-------------|---------------|---------|--------|-----------|-------------|--------|------------|--------|-------|
| [name] | /5 | /5 | /5 | /5 | /5 | /5 | /5 | /5 | /5 | /45 |
```

## Instructions

1. **Read positioning-strategy.md and brand-identity.md** (if they exist) to understand what the brand should feel like.
2. **Generate 15-20 name candidates** across the 5 categories above.
3. **Filter to top 8-10** based on gut feel and obvious disqualifications (taken domains, negative meanings).
4. **Score the top 8-10** using the evaluation matrix.
5. **Present the top 3-5** with rationale for each.
6. **Recommend a winner** but let the founder decide — naming is personal.
7. **Check for Bulgarian language issues.** Some words sound fine in English but have unintended meanings in Bulgarian. Flag these.
8. **Save output** to `/04-brand/naming-exploration.md`

## Quality Checklist

- [ ] At least 15 candidates generated across multiple categories
- [ ] Top candidates scored on all 9 criteria
- [ ] Domain availability noted (at least checked conceptually)
- [ ] No negative associations in Bulgarian language
- [ ] Names work when spoken aloud (not just read)
- [ ] Top 3-5 presented with clear pros/cons
- [ ] A recommendation is made with reasoning

## After This Skill

Suggest to the user:
- "Want to check domain availability for the top picks?"
- "Ready to build the brand identity around the chosen name? → `/build-brand`"
- "Should I draft how the name would look in the app store listing?"
