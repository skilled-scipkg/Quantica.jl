# quantica-jl source map: Examples and Tutorials

Generated from source roots:
- `ext`
- `src`

Additional behavior-check anchors:
- `test`

Use this map only after exhausting the topic docs in `references/doc_map.md`.

## Fast source navigation
- `rg -n "<symbol_or_keyword>" src ext test`
- `rg -n "LP\.|bands\(|greenfunction\(|ldos\(|qplot\(|plotbands|plotlattice" src ext test`

## Suggested source entry points (existing)
- `src/presets/lattices.jl` | symbols: `LP.*` constructors | behavior: lattice presets used by tutorial workflows.
- `src/presets/hamiltonians.jl` | symbols: `HP.*` constructors | behavior: quick canonical models for examples.
- `src/bands.jl` | symbols: `spectrum`, `bands`, `subdiv`, defects/patches machinery | behavior: bandstructure generation and continuity handling.
- `src/greenfunction.jl` | symbols: `greenfunction`, `attach`, `GreenSlice` | behavior: transport-ready system setup and slice evaluation.
- `src/observables.jl` | symbols: `ldos`, `current`, `transmission`, `conductance`, `josephson` | behavior: observable extraction from tutorial outputs.
- `ext/QuanticaMakieExt/plotlattice.jl` | symbols: `qplot` for lattices/Hamiltonians/GreenFunctions | behavior: tutorial lattice and device visualization path.
- `ext/QuanticaMakieExt/plotbands.jl` | symbols: `qplot` for `Bandstructure` | behavior: tutorial band plotting pipeline.

## Function-level behavior checks
1. Bands workflow probe:
   - `julia --project -e 'using Quantica; h = LP.honeycomb() |> hopping(1); b = bands(h, range(0, 2pi, length = 11), range(0, 2pi, length = 11)); @assert length(b) > 0; println("ok:bands")'`
2. Green + LDOS workflow probe:
   - `julia --project -e 'using Quantica; h = LP.square() |> onsite(4) - hopping(1) |> supercell(region = r -> norm(r) < 8); d = ldos(greenfunction(h)(0.1 + 1e-3im)); @assert all(isfinite, d[]); println("ok:ldos")'`
3. Plotting gate probe (works without backend install):
   - `julia --project -e 'using Quantica; ok = false; try qplot(LP.linear()); catch err; ok = occursin("No plotting backend", sprint(showerror, err)); end; @assert ok; println("ok:qplot-gate")'`

## Test anchors for regression checks
- `test/test_bandstructure.jl` | `spectrum`/`bands` behavior and slicing.
- `test/test_greenfunction.jl` | example-style Green workflows and contact attachment.
- `test/test_plots.jl` | plotting examples and shader keyword behavior.
