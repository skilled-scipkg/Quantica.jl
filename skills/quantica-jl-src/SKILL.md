---
name: quantica-jl-src
description: This skill should be used when users ask about src in quantica-jl; it prioritizes documentation references and then source inspection only for unresolved details.
---

# quantica-jl: Src

## High-Signal Playbook
### Route conditions
- Use this skill for advanced source-backed workflows around `ExternalPresets.wannier90`, imported model augmentation, and operator-level checks.
- Route generic package setup to `quantica-jl-build-and-install`.
- Route lattice/model/Hamiltonian construction from scratch to `quantica-jl-inputs-and-modeling`.
- Route symbol-level docstring/API lookup to `quantica-jl-api-and-scripting`.
- Route guided beginner walkthroughs to `quantica-jl-examples-and-tutorials`.

### Triage questions
- Do you already have a Wannier90 `*_tb.dat` file path, or should we start with the repo smoke file `test/wannier_test_tb.dat`?
- Is the physical model effectively 2D or 3D (`dim`)?
- Do you need matrix-element truncation (`htol`, `rtol`) for speed/memory?
- Do you need only `hamiltonian(w)` or also `position(w)`?
- Are you adding parametric terms at import time or modifying an existing builder?
- Is performance sensitive enough to care about closure allocations in modifiers?

### Canonical workflow
1. Import with `EP.wannier90(path; dim, htol, rtol)` (`docs/src/advanced/wannier90.md`).
2. Build core operators: `h = hamiltonian(w)` and optional `r = position(w)`.
3. If model augmentation is needed, pass the model at import time (`EP.wannier90(path, model; ...)`) instead of internal in-place mutations.
4. Apply explicit modifiers with `@onsite!`/`@hopping!` when needed.
5. Evaluate harmonics (`h[(0,0)]`) or Bloch matrices (`h(phi...)`) and validate dimensions/nonzeros.
6. Escalate to source only if docs do not explain behavior.

### Terminal quickstart (from repo root)
- `julia --project -e 'using Quantica; w = EP.wannier90(\"test/wannier_test_tb.dat\"; dim = 2, htol = 1e-5, rtol = 1e-4); h = hamiltonian(w); r = position(w); @assert size(h[(0, 0)], 1) > 0; @assert !isempty(r[SA[0, 0]]); println(\"ok:wannier-smoke\")'`

### Minimal working example
```julia
using Quantica

w = EP.wannier90("test/wannier_test_tb.dat"; dim = 2, htol = 1e-5, rtol = 1e-4)
h = hamiltonian(w)
r = position(w)

w_delta = EP.wannier90("test/wannier_test_tb.dat", @onsite((; delta = 0) -> delta); dim = 2)
h_delta = hamiltonian(w_delta)
@show h[(0, 0)]
@show h_delta(delta = 0.05)[(0, 0)]
@show r[SA[0, 0]]
```

### Pitfalls and fixes
- Wrong effective dimension: default import is 3D; set `dim = 2` for quasi-2D data.
- Large dense import objects: increase `htol`/`rtol` carefully and re-check physics.
- Adding models with internal `Quantica.add!` APIs: prefer passing models at import time.
- Treating `position(w)` as a full `Hamiltonian`: it is a `BarebonesOperator` with narrower API.
- Performance regressions from non-constant closure captures (for example over `r`): bind constants in module scope.

### Convergence and validation checks
- Import summary (`cells`, `elements`, `modifiers`) matches expected scale.
- `h[(0, 0)]` shape matches orbital count from imported basis.
- `r[SA[0, 0]]` has expected nonzero structure.
- Parameter toggles (for example `delta`) produce measurable Hamiltonian changes.
- Downstream spectra/bands remain physically plausible after tolerance changes.

## Primary documentation references
- `docs/src/advanced/wannier90.md`

## Docs-first workflow
- Start with the primary references above.
- If details are missing, inspect `references/doc_map.md` for the complete topic document list.
- If ambiguity remains after docs, inspect `references/source_map.md` and start with the prioritized entries.
- Cite exact documentation file paths in responses.

## Source entry points for unresolved issues
- `src/presets/external.jl`: `WannierBuilder`, `wannier90` import path, tolerance handling, and API glue for `hamiltonian`/`position`.
- `src/builders.jl`: sparse harmonic builders used by imported models.
- `src/hamiltonian.jl`: `hamiltonian` assembly and parametric call behavior.
- `src/models.jl`: macro parsing and modifier application internals (`@onsite`, `@hopping`, `@onsite!`, `@hopping!`).
- `src/transform.jl` and `src/supercell.jl`: post-import geometry/periodicity transforms when workflows branch into custom modeling.
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" ext src`).
