---
name: creo10-jlink-cad-automation
description: Safely inspect, automate, optimize, validate, and hand off Creo Parametric 10.0.0 CAD work with Java/J-Link, parameterized templates, DXF/STL neutral geometry, and controlled work copies. Use for Creo 10 or J-Link setup, protk.dat registration, Java CAD performance, model changes, microfluidic L1/L2 design, regeneration/readback checks, or deciding when GUI automation is permissible.
---

# Creo 10 J-Link CAD Automation

Apply this workflow to Creo Parametric 10.0.0 tasks.

## Required reading

1. Read [AUTOMATION_SOP_CN.md](references/AUTOMATION_SOP_CN.md) for every task.
2. Read [DESIGN_PATTERNS_CN.md](references/DESIGN_PATTERNS_CN.md) for geometry, layer, channel, port, pattern, or editable-model tasks.
3. Read [PERFORMANCE_FAILURES_CN.md](references/PERFORMANCE_FAILURES_CN.md) for performance work, J-Link failures, startup issues, or API boundary decisions.
4. Read [VERIFIED_CASES_CN.md](references/VERIFIED_CASES_CN.md) before reusing a claimed command, method, tolerance, result, or local precedent.

## Non-negotiable order

Use this automation priority:

```text
J-Link / Creo API
→ parameterized template and batched parameter changes
→ Creo native commands, Relations, Pattern, or UDF
→ Mapkey or Trail
→ mouse/keyboard GUI automation only as the final fallback
```

Do not use mouse coordinates, screen recognition, or button-by-button automation unless the API blocker is documented and the user explicitly authorizes that fallback.

## Safety gate

- Inspect first; do not mutate during discovery.
- Never binary-edit `.prt` or overwrite the protected source/original directory.
- Hash the protected source, create a new work copy, and write only to a new output name or revision.
- Do not change Creo, JDK, `otk.jar`, or licensing components unless the user explicitly asks.
- Do not invent PTC methods. Check the installed examples and APIWizard when uncertain.
- Do not save after regeneration failure, failed features, invalid parameter readback, invalid geometry, or a unit mismatch.
- Do not equate a visible model with success. Require evidence from Creo/J-Link and the filesystem.
- Treat the current L2 printing model as unfinished until the user completes its internal details and a fresh validation passes.

## Execution loop

1. Inventory environment, registration, source hashes, current Creo sessions, and project status read-only.
2. Classify the route: parameter edit, repeated structure, simple new feature, complex topology, or neutral import.
3. Create a uniquely named work copy and record the original hash.
4. Instrument stage timing before optimization.
5. Apply one low-risk, testable change group.
6. Regenerate once at the final gate; require zero failed features.
7. Read back parameters/dimensions, units, outline, feature counts, and mass properties as applicable.
8. Save once only after validation; confirm `GetIsModified=false` and output existence/hash.
9. Recheck the protected source hash and label the result as verified, partially verified, or unverified.
10. Update handoff evidence, including exact blockers and the next safe action.

## API blocker report

Before requesting GUI fallback, output:

```text
API blocker:
Local documentation/examples checked:
Exact operation that cannot be completed:
Alternative routes:
Mouse automation risks:
```

Then wait for explicit authorization.

