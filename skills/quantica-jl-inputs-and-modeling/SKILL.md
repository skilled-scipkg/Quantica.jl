---
name: quantica-jl-inputs-and-modeling
description: This skill should be used when users ask about inputs and modeling in quantica-jl; it prioritizes documentation references and then source inspection only for unresolved details.
---

# quantica-jl: Inputs and Modeling

## High-Signal Playbook
### Route conditions
- Use this skill for lattice setup, selectors, model terms, Hamiltonian assembly, parametric modifiers, Green-function setup, and mean-field modeling.
- Route full tutorial walkthrough requests to `quantica-jl-examples-and-tutorials`.
- Route pure install/backend issues to `quantica-jl-build-and-install`.
- Route docstring/signature lookup requests to `quantica-jl-api-and-scripting`.
- Route external Wannier90 import workflows to `quantica-jl-src`.

### Triage questions
- What lattice dimensionality and embedding dimension are required?
- Is the model non-parametric or parametric (`@onsite`/`@hopping`)?
- Are orbitals uniform across sublattices or heterogeneous?
- Are fields/terms position dependent (order of `supercell` and modifiers matters)?
- Is the target output harmonics, Bloch matrices, bands, Green functions, or observables?
- Are leads/self-energies attached, and which Green solver is intended?
- Is this a self-consistent mean-field loop (external fixed-point solver needed)?

### Canonical workflow
1. Choose lattice path: preset (`LP.*`) or explicit `sublat` + `lattice` (`docs/src/tutorial/lattices.md`).
2. Define `onsite`/`hopping` terms and selector constraints (`docs/src/tutorial/models.md`).
3. Introduce parameters with `@onsite`/`@hopping`; add modifiers with `@onsite!`/`@hopping!`.
4. Assemble Hamiltonian with orbitals (`docs/src/tutorial/hamiltonians.md`).
5. Validate harmonics/Bloch calls (`h[(dn...)]`, `h(phi...)`) before heavy workflows.
6. For transport/spectral workflows, build `greenfunction` and choose solver by dimensionality (`docs/src/tutorial/greenfunctions.md`).
7. For interacting workflows, build `meanfield` and solve fixed point with an external solver (`docs/src/advanced/meanfield.md`).
8. If behavior remains unclear, drill into curated source entry points.

### Terminal quickstart (from repo root)
- `julia --project -e 'using Quantica; lat = LP.honeycomb(); h = hamiltonian(lat, @hopping((; t = 1.0) -> t) + onsite(0.0)); hk = h(t = 1.0)((0.0, 0.0)); @assert ishermitian(hk); g = greenfunction(supercell(h(t = 1.0), region = r -> norm(r) < 4)); d = ldos(g(0.1 + 1e-3im)); @assert all(isfinite, d[]); println("ok:modeling")'`

### Minimal working example
```julia
using Quantica

lat = LP.honeycomb()
model = @hopping((; t = 2.7) -> t) + onsite(0.0)
peierls = @hopping!((amp, r, dr; bz = 0.0) -> amp * cis(-bz / 2 * SA[-r[2], r[1]]' * dr))
h_param = hamiltonian(lat, model, peierls)

h = h_param(t = 1.0, bz = 0.1)
@show h[(0, 0)]
@show h((0.0, 0.0))
```

### Pitfalls and fixes
- Macro keyword syntax: use `;` inside `@onsite/@hopping/@onsite!/@hopping!`.
- Modifier ordering: modifiers do not commute; apply in intentional sequence.
- Transform order: `supercell/transform/translate/stitch` before vs after model application can change physics.
- Parametric harmonics: unspecified parameters may produce zeros or keyword errors depending on call path.
- Green function scope: observables from `g` apply to the central region, not attached lead sites.
- Position-dependent fields: build supercell before applying field-dependent model terms.
- Arpack threading caveat: avoid assuming thread safety with `ES.Arpack`.

### Convergence and validation checks
- Check matrix sanity early: `ishermitian(h((0.0, 0.0)))` and expected sparsity/shape.
- Confirm periodicity assumptions by comparing `h` and transformed variants on simple probes.
- For band workflows, include known defect points or use `defects`/`patches` if dislocations appear.
- For Green workflows, verify contact counts and slice sizes match intended geometry.
- For mean-field loops, monitor fixed-point residuals and repeat with varied initial seeds.

## Primary documentation references
- `docs/src/tutorial/models.md`
- `docs/src/tutorial/hamiltonians.md`
- `docs/src/tutorial/greenfunctions.md`
- `docs/src/tutorial/bandstructures.md`
- `docs/src/advanced/meanfield.md`
- `docs/src/advanced/nonspatial.md`

## Docs-first workflow
- Start with the primary references above.
- If details are missing, inspect `references/doc_map.md` for the complete topic document list.
- If ambiguity remains after docs, inspect `references/source_map.md` and start with the prioritized entries.
- Cite exact documentation file paths in responses.

## Source entry points for unresolved issues
- `src/lattice.jl`: lattice construction, selectors, and site/cell indexing behavior.
- `src/models.jl`: model terms, parametric macro parsing, and modifier internals.
- `src/hamiltonian.jl`: Hamiltonian assembly, parametric application, and call semantics.
- `src/transform.jl` and `src/supercell.jl`: geometry/periodicity transforms and ordering effects.
- `src/greenfunction.jl`: solver defaults, slicing, and energy-evaluation mechanics.
- `src/meanfield.jl`: Hartree/Fock mean-field construction and field serialization hooks.
- `src/solvers/greensolvers.jl` and `src/solvers/selfenergy/model.jl`: solver/backend behavior for advanced transport workflows.
- `src/presets/hamiltonians.jl`: built-in Hamiltonian presets used in fast prototyping.
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" ext src`).
