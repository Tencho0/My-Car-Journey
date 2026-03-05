---
command: /name-product
description: "Brainstorm, evaluate, and select a product name"
agent: @brand-architect
skills: [product-naming]
output: /04-brand/naming-exploration.md
---

# /name-product — Product Naming

Brainstorm product name candidates, evaluate them on a structured scorecard, and recommend finalists.

## Invocation

```
/name-product
/name-product I want something that sounds techy and modern
/name-product I'm thinking something with "drive" or "auto" in the name
```

## Workflow

### Step 1: Load Context

Read these files:
1. `/02-strategy/positioning-strategy.md` — how we want to be perceived
2. `/02-strategy/value-proposition.md` — what value we deliver
3. `/01-discovery/customers/personas.md` — who the user is (if exists)
4. `/01-discovery/research/competitor-analysis.md` — competitor names to avoid confusion with
5. `/00-project/concept-document.md` — overall vision

### Step 2: Understand Preferences

Ask the user:

1. **Tone:** "What feeling should the name evoke? Pick what resonates most."
   - Professional and trustworthy
   - Modern and techy
   - Fun and playful
   - Bold and car-culture
   - Clean and minimal

2. **Language preference:** "Should the name work primarily in Bulgarian, English, or both?"

3. **Any names you already like or dislike?** "Have you thought about any names? Even rejected ones help me understand your taste."

4. **Must-avoid:** "Any words, sounds, or associations to stay away from?"

### Step 3: Generate Candidates

Load skill: `/00-project/skills/product-naming.md`

Generate 15-20 candidates across 5 categories:
1. Descriptive (directly says what it does)
2. Metaphorical (evokes the concept)
3. Invented/Coined (new, ownable words)
4. Compound (two meaningful words combined)
5. Action-oriented (implies what the user does)

### Step 4: First Filter

Cut obviously weak names:
- Already taken by a well-known app
- Negative associations in Bulgarian
- Unpronounceable in Bulgarian or English
- Too similar to a competitor name
- Too generic to own

Present the surviving 8-10 to the user for gut reaction:
"Here are my candidates. Before I score them, which ones do you immediately like or dislike?"

### Step 5: Score Finalists

Take the user's feedback, narrow to 5-6 finalists.
Score each on the 9-criteria matrix:
- Memorability, Pronunciation, Meaning, Domain, App Store, Scalability, Visual Potential, Uniqueness, Length

Present the scored matrix with a recommended top 3.

### Step 6: Final Recommendation

For each finalist, provide:
- Why it works
- Potential risks or concerns
- How it would look as: app icon text, app store title, "welcome to [name]" in-app
- Possible tagline pairings

Recommend a winner, but let the founder decide.

### Step 7: Save

Save to `/04-brand/naming-exploration.md`
Log the naming decision in `/00-project/decisions/DEC-XXX-product-name.md` (after user decides)
Update `/00-project/project-status.md`

## After Completion

Suggested next commands:
- `/build-brand` — brand identity built around the chosen name
- "Want me to check domain availability approaches for the top picks?"
- "Ready to draft the app store listing? → after brand identity is done"

## Notes

- Naming often takes multiple rounds — user might want to sleep on it
- Don't spend more than 2 sessions on naming — at some point, pick one and move on
- The name can be changed pre-launch. Don't let naming block progress.
- If the user is stuck between 2-3 names, suggest testing them with friends or the target community
