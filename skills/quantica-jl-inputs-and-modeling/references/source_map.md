# quantica-jl source map: Inputs and Modeling

Generated from source roots:
- `ext`
- `src`

Additional behavior-check anchors:
- `test`

Use this map only after exhausting the topic docs in `references/doc_map.md`.

## Fast source navigation
- `rg -n "<symbol_or_keyword>" src ext test`
- `rg -n "sublat|lattice|siteselector|hopselector|@onsite|@hopping|hamiltonian|greenfunction|meanfield" src ext test`

## Suggested source entry points (existing)
- `src/lattice.jl` | symbols: `sublat`, `lattice`, `combine`, `nrange` | behavior: lattice construction and structural invariants.
- `src/selectors.jl` | symbols: selector application helpers | behavior: site/hopping selector filtering semantics.
- `src/models.jl` | symbols: `@onsite`, `@hopping`, `@onsite!`, `@hopping!`, term parsing | behavior: parametric model and modifier macro contracts.
- `src/hamiltonian.jl` | symbols: `hamiltonian!`, `call!`, `getindex`, `stitch` | behavior: assembly, Bloch/harmonic evaluation, modifier application order.
- `src/supercell.jl` | symbols: supercell transformations | behavior: region/cell reduction effects on finite devices.
- `src/transform.jl` | symbols: geometry transforms | behavior: ordering-sensitive coordinate/model transforms.
- `src/greenfunction.jl` | symbols: `greenfunction`, `GreenSlice`, `selfenergy!` | behavior: transport/spectral solver bridge from Hamiltonians.
- `src/meanfield.jl` | symbols: `meanfield`, `MeanField`, `zerofield` | behavior: self-consistent loop state and call semantics.
- `src/solvers/greensolvers.jl` | symbols: `GS.SparseLU`, `GS.Spectrum`, `GS.Schur`, `GS.KPM`, `GS.Bands` | behavior: solver applicability by model dimensionality.
- `src/solvers/selfenergy/model.jl` | symbols: model-based self-energy handling | behavior: advanced lead/self-energy workflows.
- `src/presets/hamiltonians.jl` | symbols: preset Hamiltonian constructors | behavior: controlled baseline models for quick experiments.

## Function-level behavior checks
1. Hamiltonian assembly + Hermiticity:
   - `julia --project -e 'using Quantica; h = LP.honeycomb() |> hopping(1); @assert ishermitian(h((0.0, 0.0))); println("ok:hamiltonian-hermitian")'`
2. Transform/supercell ordering check:
   - `julia --project -e 'using Quantica; h1 = LP.linear() |> supercell(4) |> hamiltonian(onsite(r -> r[1])); h2 = LP.linear() |> hamiltonian(onsite(r -> r[1])) |> supercell(4); @assert h1[()] != h2[()]; println("ok:ordering")'`
3. Green-function observable probe:
   - `julia --project -e 'using Quantica; hc = LP.square() |> onsite(4) - hopping(1) |> supercell(region = r -> norm(r) < 6); d = ldos(greenfunction(hc)(0.1 + 1e-3im)); @assert all(isfinite, d[]); println("ok:green-observable")'`

## Test anchors for regression checks
- `test/test_lattice.jl` | lattice construction, selectors, and supercell behavior.
- `test/test_hamiltonian.jl` | model macros, Hamiltonian assembly, modifier behavior.
- `test/test_greenfunction.jl` | solver applicability and Green-function slicing.
