---
command: /write-tests
description: "Generate test scenarios and unit test specifications from a functional spec or user story"
agent: @qa
skills: [test-scenarios]
output: /03-product/test-scenarios/[feature-name]-tests.md
---

# /write-tests — Test Scenarios & Unit Test Specs

Generate comprehensive test scenarios from a functional spec, then produce xUnit test specifications for the most critical business logic.

## Invocation

```
/write-tests expense-tracking
/write-tests cost-dashboard
/write-tests fuel-entry
/write-tests all                     (generates tests for all feature specs)
```

## Workflow

### Step 1: Load Context

Read these files:
1. `/03-product/functional-specs/[feature-name].md` — feature behavior and business rules
2. `/03-product/user-stories/epic-N-*.md` — acceptance criteria
3. `/03-product/technical/database-schema.md` — data constraints (if exists)
4. `/03-product/product-requirements-document.md` — feature priorities

If the functional spec doesn't exist:
"The functional spec for [feature] doesn't exist yet. I need it to generate meaningful tests. Want me to ask @product-architect to write it first? → `/write-spec [feature]`"

### Step 2: Identify What to Test

Extract from the functional spec:
1. All business rules (BR-1, BR-2, etc.)
2. All validation rules (field constraints)
3. All calculations (cost/km, monthly totals, fuel consumption)
4. All state transitions (reminder states, challenge states)
5. All edge cases mentioned in the spec

Map each to a test priority:
- 🔴 Must: Financial calculations, core business rules, data validation
- 🟡 Should: Edge cases, alternative paths, formatting
- 🟢 Could: UI state verification, minor formatting, nice-to-have coverage

### Step 3: Generate Test Scenarios

Load skill: `/00-project/skills/test-scenarios.md`

Produce:
1. Test coverage matrix (business rule → test mapping)
2. Test scenarios grouped by logical area
3. Boundary value table for all numeric inputs
4. Edge cases specific to the feature
5. Bulgarian-specific test cases

### Step 4: Write Unit Test Specifications

For all 🔴 Must scenarios, produce concrete xUnit test structures:

```csharp
[Fact]
public void MethodName_Scenario_ExpectedResult()
{
    // Arrange
    var mockRepo = new Mock<IExpenseRepository>();
    mockRepo.Setup(r => r.GetByVehicleId(vehicleId))
        .Returns(testExpenses);
    var sut = new CostCalculationService(mockRepo.Object);

    // Act
    var result = sut.CalculateCostPerKm(vehicleId, 15000);

    // Assert
    Assert.Equal(0.45m, result);
}
```

Include:
- Test class name and namespace
- All mocks needed with setup
- Concrete test data (not generic placeholders)
- Clear assertions
- Comments explaining what's being tested and why

### Step 5: Identify Gaps

After generating tests, report:
- Any business rules that are ambiguous and need clarification from @product-architect
- Any acceptance criteria that aren't testable (suggest rewording)
- Any missing edge cases the functional spec didn't address

### Step 6: Save

Save test scenarios to `/03-product/test-scenarios/[feature-name]-tests.md`
If writing actual test code, save to `/05-development/src/Tests/[FeatureName]Tests.cs`
Update `/00-project/project-status.md`

## After Completion

Suggested next commands:
- `/write-tests [next-feature]` — continue with next feature
- "Want me to write the actual xUnit test files?"
- "Should I create a regression checklist for the current sprint?"
- "Ready to review existing code for untested business logic?"

## Batch Mode

If invoked with `/write-tests all`:
1. Generate tests for each functional spec in order:
   expense-tracking → fuel-entry → cost-dashboard → vehicle-management → vehicle-timeline → maintenance-reminders → challenges-gamification
2. After each, summarize: scenarios count, must-test count, unit test specs count
3. At the end, produce a coverage summary:

| Feature | Business Rules | Test Scenarios | Unit Test Specs | Coverage |
|---------|---------------|----------------|-----------------|----------|

## Notes

- Test scenarios should be written BEFORE development — this is shift-left testing
- Don't aim for 100% coverage — aim for 100% coverage of business logic and calculations
- A failing test that catches a real bug is worth more than 50 passing tests that verify obvious behavior
- When in doubt, test the math. Financial calculations are where bugs hurt users most.
