# Chapter 18: Table-Driven Methods

[← Previous: Chapter 17](17-Chapter-17-Unusual-Control-Structures.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 19 →](19-Chapter-19-General-Control-Issues.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

A table-driven method is a scheme that allows you to look up information in a table (array, hash map, dictionary) rather than using complex conditional statements (`if-else` chains or massive `switch` blocks).

Steve McConnell highlights table-driven programming as one of the hallmark differences between novice code and master craftsmanship: **Whenever conditional logic becomes repetitive, complex, or likely to change, replace the logic with data**. Data is easier to read, easier to modify, and decouples business rules from program control flow.

---

## 2. Theoretical Foundations & Key Concepts

### Advantages of Table-Driven Methods
1. **Dramatic Complexity Reduction**: Replaces 50 lines of convoluted `if/elif/else` ladders with a single dictionary lookup.
2. **Ease of Maintenance**: Adding a new business rule requires only adding a row to a table; zero code logic changes.
3. **Runtime Configurability**: Tables can be loaded from configuration files, databases, or JSON without recompilation.

### The Three Types of Table Lookups

1. **Direct Access**:
   - The key directly indexes the table (e.g. array indexed by month number, or hash map keyed by status string).
2. **Indexed Access**:
   - Uses an intermediate lookup table to map non-contiguous keys into compact index spaces.
3. **Stair-Step Access**:
   - For ranges of values (e.g. tax brackets, grading scales, volume discount tiers).
   - The value is compared against ascending bucket thresholds to determine the category.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 18.1 — Replace Multi-Branch Logic with Dictionaries**: If an `if-else` ladder or `switch` block matches identical expressions against discrete constants to return values, refactor it into a lookup table or dictionary.
2. **RULE 18.2 — Stair-Step Lookups for Ranges**: When classifying continuous numerical values into discrete buckets (e.g. ratings, percentages, tax brackets), use a stair-step table structure rather than chained comparisons.
3. **RULE 18.3 — Protect Table Lookups with Fallbacks**: Always handle missing keys safely (e.g. `.get(key, default)` in Python or checking `map.find()`). Never assume the key exists.
4. **RULE 18.4 — Table-Driven Command Dispatch**: Replace massive action switches with command dispatch tables mapping action names to handler routines.

---

## 4. Operational Checklist Derived from McConnell: Table-Driven Methods

- [ ] Has repetitive conditional logic been evaluated for table-driven replacement?
- [ ] Are table entries clean, well-structured, and typed?
- [ ] Does direct table access handle out-of-range keys safely?
- [ ] For stair-step tables, are edge boundaries ($<$, $\le$) clearly tested?
- [ ] Is data separated cleanly from control logic?

---

## 5. Heuristics & Metrics

- **Branch Count Threshold**: An `if-else` or `switch` with $> 5$ branches returning data is a prime candidate for a table-driven refactoring.
- **Cyclomatic Complexity Reduction**: Table-driven methods typically reduce routine cyclomatic complexity from $> 10$ down to $1$ or $2$.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Verbose Conditional Ladder
```javascript
// BAD: 30 lines of brittle conditional code that must be edited for every new country
function getShippingRate(countryCode) {
  if (countryCode === 'US') return 5.00;
  else if (countryCode === 'CA') return 8.00;
  else if (countryCode === 'GB') return 12.00;
  else if (countryCode === 'DE') return 12.00;
  else if (countryCode === 'FR') return 12.00;
  else if (countryCode === 'JP') return 18.00;
  else return 25.00;
}
```

### Pattern: Clean Table-Driven Method
```typescript
// GOOD: Declarative, easily extended table lookup with safe fallback
export const SHIPPING_RATES_CENTS: Readonly<Record<string, number>> = Object.freeze({
  US: 500,
  CA: 800,
  GB: 1200,
  DE: 1200,
  FR: 1200,
  JP: 1800,
});

const DEFAULT_INTERNATIONAL_RATE_CENTS = 2500;

export function getShippingRateCents(countryCode: string): number {
  return SHIPPING_RATES_CENTS[countryCode.toUpperCase()] ?? DEFAULT_INTERNATIONAL_RATE_CENTS;
}
```

---

## 7. Inspection & Verification Protocol

1. Look for conditional chains checking identical variables: convert to dictionary lookups.
2. Verify all table lookups have explicit default handling for unknown keys.
