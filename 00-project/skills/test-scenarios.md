---
name: test-scenarios
description: "Generate structured test scenarios and unit test specifications from functional specs, covering happy paths, edge cases, boundary values, and error handling."
agent: @qa
used-by: [@qa, @developer]
---

# Generate Test Scenarios & Unit Test Specs

## Purpose

Take a functional specification and produce comprehensive test scenarios that cover happy paths, edge cases, boundary conditions, and error handling. Then translate the most critical scenarios into unit test specifications that a developer can implement directly in xUnit.

## When to Use

- After a functional spec is written, BEFORE development begins
- When reviewing existing code for test coverage gaps
- Before a release, to generate regression test scenarios
- When a bug is found, to add a test that catches it

## Template

```markdown
# Test Scenarios: [Feature Name]

**File:** `/03-product/test-scenarios/[feature-name]-tests.md`
**Produced by:** @qa
**Date:** [date]
**Source spec:** `/03-product/functional-specs/[feature-name].md`
**Source stories:** `/03-product/user-stories/epic-N-[name].md`

---

## 1. Test Coverage Matrix

| Business Rule / Requirement | Test Type | Priority | Covered |
|---|---|---|---|
| [BR-1 from spec] | Unit | 🔴 Must | ⬜ |
| [BR-2 from spec] | Unit | 🔴 Must | ⬜ |
| ... | | | |

## 2. Test Scenarios

### Scenario Group: [Logical Grouping]

#### TS-[feature]-[number]: [Scenario Name]

**Type:** Happy Path | Edge Case | Boundary | Error | Security
**Priority:** 🔴 Must | 🟡 Should | 🟢 Could
**Business rule:** [BR-X from spec]

**Preconditions:**
- [What must be true before this test runs]

**Steps:**
1. [Action]
2. [Action]

**Expected result:**
- [What should happen]

**Test data:**
- [Specific values to use]

---

## 3. Unit Test Specifications

### Class: [ServiceName]Tests

#### Test: [MethodName]_[Scenario]_[ExpectedResult]

```csharp
// Arrange
var input = [specific test data];
var expected = [expected output];
var sut = new [ClassUnderTest]([mocked dependencies]);

// Act
var result = sut.[MethodName](input);

// Assert
Assert.Equal(expected, result);
```

**What this tests:** [plain English explanation]
**Mocks needed:** [list of dependencies to mock]

---

## 4. Boundary Values

| Field / Parameter | Min Valid | Max Valid | Below Min | Above Max | Zero | Null/Empty |
|---|---|---|---|---|---|---|
| [field name] | [value] | [value] | [value] | [value] | [value] | [value] |

---

## 5. Edge Cases Specific to This Feature

| # | Edge Case | Expected Behavior | Priority |
|---|---|---|---|
| 1 | [scenario] | [what should happen] | 🔴 |
| 2 | [scenario] | [what should happen] | 🟡 |

---

## 6. Bulgarian-Specific Test Cases

| # | Scenario | Test Data | Expected |
|---|---|---|---|
| 1 | Currency formatting | 1234.56 | "1 234,56 лв" |
| 2 | Cyrillic text in notes | "Смяна на масло" | Stored and displayed correctly |
| 3 | Date format | 2026-03-05 | "05.03.2026" |
```

## Instructions

1. **Read the functional spec thoroughly.** Every business rule must have at least one test scenario.
2. **Start with happy paths.** What should work perfectly?
3. **Then think adversarially.** What could go wrong? What weird inputs could a user provide?
4. **Apply boundary value analysis.** For every numeric field: test min, max, zero, negative, just below min, just above max.
5. **Check empty states.** What happens with no data? First-time use?
6. **Consider Bulgarian context.** Currency (лв), Cyrillic text, date formats, number formatting (comma vs dot for decimals).
7. **Write unit test specs for all 🔴 Must priority scenarios.** Include actual xUnit code structure.
8. **Use AAA pattern.** Arrange (setup), Act (execute), Assert (verify).
9. **Name tests descriptively.** `CalculateMonthlyTotal_WithExpensesInMultipleCategories_ReturnsSumOfAllCategories`
10. **Save output** to `/03-product/test-scenarios/[feature-name]-tests.md`

## Quality Checklist

- [ ] Every business rule from the spec has at least one test scenario
- [ ] Happy paths are covered for all user stories
- [ ] Boundary values are tested for all numeric inputs
- [ ] Empty/null/zero cases are covered
- [ ] Error handling scenarios are defined
- [ ] Bulgarian-specific cases are included (currency, Cyrillic, dates)
- [ ] Unit test specs include concrete xUnit code structures
- [ ] All mocked dependencies are identified
- [ ] Test data uses realistic values (not just "test123")
- [ ] Priorities are assigned (🔴 Must for business logic, 🟡 Should for edge cases)

## After This Skill

Suggest to the user:
- "Want me to write the actual xUnit test code? I'll create the test files."
- "Should I review the acceptance criteria for testability gaps?"
- "Ready to generate tests for the next feature? → `/write-tests [feature]`"
