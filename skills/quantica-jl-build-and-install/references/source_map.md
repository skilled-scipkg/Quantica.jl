# quantica-jl source map: Build and Install

Generated from source roots:
- `ext`
- `src`

Additional behavior-check anchors:
- `test`

Use this map only after exhausting the topic docs in `references/doc_map.md`.

## Fast source navigation
- `rg -n "<symbol_or_keyword>" src ext test`
- `rg -n "qplot|plotlattice|plotbands|spectrum|greenfunction|Arpack|Arnoldi|Krylov|LinearMaps" src ext test`

## Suggested source entry points (existing)
- `Project.toml` | symbols: `[compat]`, `[weakdeps]`, `[extensions]` | behavior: package/version gates and optional extension activation.
- `src/Quantica.jl` | symbols: `qplot`, `plotlattice`, `plotbands`, exports, include order | behavior: load surface and no-backend plotting fallback.
- `src/solvers/eigensolvers.jl` | symbols: `LinearAlgebra`, `Arpack`, `KrylovKit`, `ArnoldiMethod`, `ShiftInvert` | behavior: eigensolver object selection and dispatch.
- `ext/QuanticaMakieExt/QuanticaMakieExt.jl` | symbols: extension module wiring/includes | behavior: plotting methods become available after backend import.
- `ext/QuanticaMakieExt/docstrings.jl` | symbols: plotting keyword docs | behavior: `qplot`/`plotlattice`/`plotbands` options and defaults.
- `ext/QuanticaArpackExt.jl` | symbols: Arpack-backed eigen methods | behavior: optional Arpack solver path.
- `ext/QuanticaArnoldiMethodExt.jl` | symbols: ArnoldiMethod-backed eigen methods | behavior: optional Arnoldi solver path.
- `ext/QuanticaKrylovKitExt.jl` | symbols: KrylovKit-backed eigen methods | behavior: optional Krylov solver path.
- `ext/QuanticaLinearMapsExt.jl` | symbols: LinearMaps-backed helpers | behavior: linear-map based solver pathway.

## Function-level behavior checks
1. Core import + spectrum smoke:
   - `julia --project -e 'using Quantica; h = LP.honeycomb() |> hopping(1); e, _ = spectrum(h, (0.0, 0.0)); @assert !isempty(e) && all(isfinite, e); println("ok:spectrum")'`
2. Plotting backend gate (without Makie backend loaded):
   - `julia --project -e 'using Quantica; ok = false; try qplot(LP.linear()); catch err; ok = occursin("No plotting backend", sprint(showerror, err)); end; @assert ok; println("ok:qplot-gate")'`
3. Optional Arpack extension probe (skip-safe when dependency absent):
   - `julia --project -e 'using Quantica; try using Arpack; e, _ = spectrum(LP.honeycomb() |> hopping(1), (0.0, 0.0); solver = ES.Arpack()); @assert !isempty(e); println("ok:arpack-ext"); catch err; println("skip:arpack-ext:" * string(err)); end'`

## Test anchors for regression checks
- `test/test_bandstructure.jl` | spectrum and solver-related behavior.
- `test/test_plots.jl` | plotting backend requirement and recipe behavior.
- `test/runtests.jl` | integration entry point for package-level checks.
