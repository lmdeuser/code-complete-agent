# Verification, Review, and Debugging

Derived primarily from *Code Complete*, chapters 20–23, with modern agent verification practices.

## Review and testing

1. Review correctness and requirements before style. Use project checklists and historical defect patterns when available.
2. Keep changes focused enough to review, but do not impose an arbitrary file or line-count limit on commits.
3. Run the standard build, test, typecheck, lint, and formatting checks that cover the change, insofar as they exist and are appropriate.
4. Add tests only within established project testing conventions. Cover each affected requirement, representative valid and invalid partitions, meaningful boundaries, and regression risk.
5. Use boundary values that make sense for the actual domain; `0, 1, N-1, N` is an example pattern, not a universal input set.
6. Structured basis testing uses cyclomatic complexity to estimate a minimum number of deliberately designed cases. Arbitrary `$v(G)$` tests do not guarantee basis, branch, path, or data coverage.
7. Treat coverage as diagnostic information. No universal statement or branch percentage proves correctness.
8. Use mocks at external or intentionally isolated boundaries when useful; do not mock away the behavior the test is meant to verify.
9. Exercise the user's real scenario end to end where possible, not just import or compile checks.

## Scientific debugging

1. Stabilize a reliable reproduction or precisely characterize the observed defect and evidence.
2. Inspect the failing path and form a falsifiable root-cause hypothesis.
3. Test the hypothesis with a focused experiment, debugger observation, log, or minimal test. Change approach when evidence disproves it.
4. Fix the confirmed cause within the requested scope; avoid symptom-masking handlers and random tweaks.
5. Where feasible, show that a regression check fails on the original behavior and passes after the fix. Then run relevant broader checks.
6. Search for materially similar defects when justified, but report unrelated findings instead of expanding the fix without authorization.
7. If repeated attempts fail, stop editing and rebuild the mental model from current evidence. Do not add sleeps or arbitrary offsets to hide the failure.

## Reporting

- List actual commands and outcomes, distinguishing passing, failing, pre-existing, and unrun checks.
- Never equate a successful compile with correct behavior.
- State any environment, integration, timing, or external dependency that prevented verification.
- Keep review feedback about code and evidence, not the author.
