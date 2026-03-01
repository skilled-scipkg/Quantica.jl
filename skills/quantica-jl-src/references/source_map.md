# quantica-jl source map: Src

Generated from source roots:
- `ext`
- `src`

Additional behavior-check anchors:
- `test`

Use this map only after exhausting the topic docs in `references/doc_map.md`.

## Fast source navigation
- `rg -n "<symbol_or_keyword>" src ext test`
- `rg -n "wannier90|WannierBuilder|load_wannier90|hamiltonian\(|position\(|@onsite!|@hopping!" src ext test`

## Suggested source entry points (existing)
- `src/presets/external.jl` | symbols: `WannierBuilder`, `Wannier90Data`, `load_wannier90`, `lattice(::Wannier90Data)` | behavior: import path, tolerances, and builder conversion.
- `src/hamiltonian.jl` | symbols: `hamiltonian!`, `call!`, `getindex` | behavior: imported builder to matrix/harmonic evaluation.
- `src/models.jl` | symbols: modifier macros and parser (`@onsite`, `@onsite!`, `@hopping`, `@hopping!`) | behavior: augmentation of imported Wannier models.
- `src/builders.jl` | symbols: sparse builder internals | behavior: harmonic assembly used after import.
- `src/presets/docstrings.jl` | symbols: preset-level docs for EP/LP/HP | behavior: user-facing docs for external presets.
- `test/wannier_test_tb.dat` | symbols: smoke input dataset | behavior: reproducible local import checks.
- `test/wannier_test2_tb.dat` | symbols: alternate smoke dataset | behavior: secondary import/debug fixture.

## Function-level behavior checks
1. Import and Hamiltonian construction:
   - `julia --project -e 'using Quantica; w = EP.wannier90("test/wannier_test_tb.dat"; dim = 2, htol = 1e-5, rtol = 1e-4); h = hamiltonian(w); @assert size(h[(0, 0)], 1) > 0; println("ok:wannier-import")'`
2. Position operator access:
   - `julia --project -e 'using Quantica; w = EP.wannier90("test/wannier_test_tb.dat"; dim = 2); r = position(w); @assert !isempty(r[SA[0, 0]]); println("ok:wannier-position")'`
3. Import-time modifier parameterization:
   - `julia --project -e 'using Quantica; w = EP.wannier90("test/wannier_test_tb.dat", @onsite((; delta = 0.0) -> delta); dim = 2); h = hamiltonian(w); h0 = h[(0, 0)]; h1 = h(delta = 0.2)[(0, 0)]; @assert size(h0) == size(h1) && norm(h1 - h0) > 0; println("ok:wannier-param")'`

## Test anchors for regression checks
- `test/test_show.jl` | includes `EP.wannier90("wannier_test_tb.dat")` smoke rendering and operator checks.
- `test/test_hamiltonian.jl` | validates model/modifier behavior also used in imported workflows.
- `test/runtests.jl` | full-suite entry point when validating repository state.
