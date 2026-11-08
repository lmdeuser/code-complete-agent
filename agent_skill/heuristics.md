# Heuristics and Metrics

Numbers in *Code Complete* summarize particular studies, historical projects, or rules of thumb. They are not universal acceptance thresholds. A current project's explicit standards take precedence.

| Topic | How to interpret it safely | Source |
|---|---|---|
| Cognitive chunks | `$7 \pm 2$` is a historical cognitive model and a reminder to reduce simultaneous concepts, not a limit to enforce on classes or methods. | Ch. 5, 33 |
| Routine length | Do not target 10–50 lines. McConnell discusses evidence that some cohesive routines of 100–200 lines are acceptable and advises caution beyond 200; cohesion and complexity matter more. | Ch. 7 |
| Parameters | Around seven parameters is a strong readability warning. Smaller limits can be team policy; coherent grouping is better than mechanical splitting. | Ch. 7 |
| Cyclomatic complexity | Above 10 is a useful review and testing signal. It is not proof that a routine is defective, and above 20 does not literally mean untestable. | Ch. 19, 22 |
| Nesting | Roughly three or more levels often justify simplification. Domain structure may make a different depth clearer. | Ch. 15–17 |
| Variable names | The often-cited 8–20 characters is an average research observation, not a rule for every identifier. Scope and clarity come first. | Ch. 11 |
| Span and live time | Use them to localize data flow and diagnose readability. Do not enforce universal line-count limits on every variable. | Ch. 10 |
| Assertions | There is no universal assertion-per-line density. Use assertions for meaningful programmer invariants. | Ch. 8 |
| Test coverage | No universal 85–95% target or test-to-code ratio proves quality. Requirement, branch, boundary, and data coverage are more informative. | Ch. 22 |
| Review size | Small, focused reviews are useful, but file count and diff size depend on the change. Do not split a coherent migration merely to meet a number. | Ch. 21 |
| Refactoring size | No universal 30-line or two-file limit exists. Prefer independently verifiable behavior-preserving steps. | Ch. 24 |
| Performance hot spots | Historical 80/20 and 4%/50% observations explain why profiling matters; they are not current-system budgets. | Ch. 25–26 |
| Line width | Follow project formatting. Neither 80 nor 100 columns is a universal defect boundary. | Ch. 31 |
| Commits and builds | No universal commit file count or smoke-test duration applies to every project. Use the project's CI and review constraints. | Ch. 28–29 |

## Metrics to avoid as automatic gates

Do not invent or enforce universal thresholds for assertion density, early-return concentration, recursion depth, class dependency count, duplication length, public-method count, magic-literal count, warning count, or percentage of time spent debugging. Measure them only when they answer a concrete question about the current code.

## Using a metric responsibly

1. State the question the metric should answer.
2. Measure with an appropriate current tool and understand what the tool counts.
3. Inspect the code and domain context before deciding.
4. Compare against project standards or a relevant baseline, not an isolated number.
5. Make the smallest change that improves the underlying problem and verify behavior afterward.
