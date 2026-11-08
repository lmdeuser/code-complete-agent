# Refactoring, Performance, Integration, and Tools

Derived primarily from *Code Complete*, chapters 24–30. These practices never authorize unrelated cleanup or environment changes.

## Refactoring

- Keep behavior-preserving changes small and separately verifiable from feature work and bug fixes where practical.
- Use the existing regression suite as a safety net. If it is absent, disclose the risk and use proportionate characterization or manual verification rather than blocking every requested change.
- Target actual smells in the requested scope: duplication that causes divergence, unclear data flow, low cohesion, or difficult decision logic.
- Do not extract every repeated fragment mechanically; some duplication is cheaper than a premature abstraction.
- Preserve public contracts, error behavior, serialization, and observable side effects unless their change is requested.

## Performance

1. Establish a real performance requirement and representative workload.
2. Measure before changing code; locate the bottleneck with appropriate profiling or benchmarking.
3. Consider requirements, architecture, algorithms, data structures, and I/O before statement-level tuning.
4. Preserve a correctness baseline and change the smallest relevant area.
5. Measure again under comparable conditions. Report variance and environment; keep the change only if the measured benefit justifies its complexity.
6. Historical ratios and old benchmark gains are not predictions for the current runtime or hardware.

## Integration and tooling

- Integrate multi-component work in small, verifiable increments and run relevant smoke or integration checks after wiring.
- Determine whether failures are caused by the current change, pre-existing, or environmental. Never hide them.
- Discover tools from project configuration and existing scripts. Use the project's standard environment rather than installing global replacements.
- Do not introduce new linters, strict warning regimes, formatters, CI workflows, dependencies, or build systems without authorization.
- Keep generated artifacts and temporary verification files out of the deliverable unless the project expects them.

## Handoff

Review the final diff for scope, compatibility, accidental files, secrets, documentation drift, and unverified behavior. Commit, push, deploy, or modify CI only when explicitly requested.
