---
name: quantica-jl-examples-and-tutorials
description: This skill should be used when users ask about examples and tutorials in quantica-jl; it prioritizes documentation references and then source inspection only for unresolved details.
---

# quantica-jl: Examples and Tutorials

## High-Signal Playbook
### Route conditions
- Use this skill for step-by-step runnable workflows, tutorial sequencing, and quick simulation prototypes.
- Route installation/backend setup to `quantica-jl-build-and-install`.
- Route deep modeling/assembly logic to `quantica-jl-inputs-and-modeling`.
- Route API signature/docstring questions to `quantica-jl-api-and-scripting`.
- Route Wannier90 import workflows to `quantica-jl-src`.

### Triage questions
- What is the target output: bands, Green functions, LDOS/current, transmission/conductance, or Josephson?
- Is the system bounded (`L=0`) or periodic (`L>0`)?
- Which lattice dimension and geometry are required?
- Do you need parameter sweeps or a single-point calculation?
- Is plotting required immediately or can results be validated numerically first?
- What runtime budget and system size are acceptable?

### Canonical workflow
1. Follow tutorial progression: lattices -> models -> Hamiltonians -> bands/Green functions -> observables.
2. Pick the nearest existing tutorial pattern instead of composing from scratch.
3. Run a minimal numeric version first (without plotting/backend extras).
4. Add plotting (`GLMakie`) or sweeps once the baseline output is sane.
5. For transport, attach contacts and verify indexing/slicing assumptions.
6. Use `references/doc_map.md` for additional pages when the initial tutorial is insufficient.

### Terminal quickstart (from repo root)
- `julia --project -e 'using Quantica; h = LP.honeycomb() |> hopping(1); b = bands(h, range(0, 2pi, length = 11), range(0, 2pi, length = 11)); @assert length(b) > 0; hc = LP.square() |> onsite(4) - hopping(1) |> supercell(region = r -> norm(r) < 8); d = ldos(greenfunction(hc)(0.1 + 1e-3im)); @assert all(isfinite, d[]); println("ok:tutorial-smoke")'`

### Minimal working example
```julia
using Quantica

h = LP.honeycomb() |> hopping(1)
b = bands(h, range(0, 2pi, length = 19), range(0, 2pi, length = 19))
@show b

hc = LP.square() |> onsite(4) - hopping(1) |> supercell(region = r -> norm(r) < 10)
g = greenfunction(hc)
d = ldos(g(0.1 + 1e-3im))
@show length(d[])
```

### Pitfalls and fixes
- `docs/src/examples.md` is sparse: use tutorial pages as the actual runnable corpus.
- Missing plotting backend: install/load Makie backend before calling `qplot`.
- Arpack threading caveat: avoid assuming thread-safe parallel runs with `ES.Arpack`.
- Band dislocations near Dirac/diabolical points: include defects and tune `patches`.
- Position-dependent fields: apply `supercell` before field-dependent model terms.
- Green function interpretation: central-region `g` does not directly return lead observables.
- Multiorbital Green slicing caveat: prefer site-based observables (`ldos`, etc.) when needed.

### Convergence and validation checks
- Object summaries (`Hamiltonian`, `Bandstructure`, `GreenFunction`) match expected dimensionality.
- Bands are continuous over the chosen mesh (or repaired with defects/patches).
- Observable outputs are finite and obey basic symmetries/limits for the chosen model.
- Contact counts and slice sizes match the intended device geometry.
- Small-system run reproduces qualitative behavior before scaling system size.

## Primary documentation references
- `docs/src/tutorial/lattices.md`
- `docs/src/examples.md`
- `docs/src/tutorial/tutorial.md`
- `docs/src/tutorial/glossary.md`

## Docs-first workflow
- Start with the primary references above.
- If details are missing, inspect `references/doc_map.md` for the complete topic document list.
- If ambiguity remains after docs, inspect `references/source_map.md` and start with the prioritized entries.
- Cite exact documentation file paths in responses.

## Source entry points for unresolved issues
- `src/presets/lattices.jl`: lattice constructors used by tutorial recipes.
- `src/hamiltonian.jl`: object assembly and parametric call semantics.
- `src/bands.jl`: bandstructure building, mapping, and slicing behavior.
- `src/greenfunction.jl`: solver defaults, contact handling, and slice evaluation.
- `src/observables.jl`: LDOS/current/transmission/conductance/Josephson implementations.
- `ext/QuanticaMakieExt/plotlattice.jl` and `ext/QuanticaMakieExt/plotbands.jl`: plotting recipes used by tutorial visuals.
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" ext src`).
