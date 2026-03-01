# quantica-jl source map: API and Scripting

Generated from source roots:
- `ext`
- `src`

Additional behavior-check anchors:
- `test`

Use this map only after exhausting the topic docs in `references/doc_map.md`.

## Fast source navigation
- `rg -n "<symbol_or_keyword>" src ext test`
- `rg -n "greenfunction|ldos|current|transmission|serializer|deserialize|@which|qplot" src ext test`

## Suggested source entry points (existing)
- `src/Quantica.jl` | symbols: exports, include order | behavior: top-level API ownership and module wiring.
- `src/docstrings.jl` | symbols: in-package API docstrings | behavior: authoritative argument/keyword semantics.
- `src/hamiltonian.jl` | symbols: `call!`, `getindex`, `stitch`, `parametric!` | behavior: Hamiltonian call/indexing contracts.
- `src/greenfunction.jl` | symbols: `greenfunction`, `call!`, `getindex`, `selfenergy!` | behavior: energy evaluation and Green-slice indexing.
- `src/observables.jl` | symbols: `ldos`, `current`, `transmission`, `conductance`, `josephson`, `densitymatrix` | behavior: observable definitions and return shapes.
- `src/serializer.jl` | symbols: `serializer`, `serialize!`, `deserialize!`, `check` | behavior: parameter stream encoding/decoding for loops.
- `ext/QuanticaMakieExt/docstrings.jl` | symbols: `qplot`/`plotbands` docs | behavior: plotting API keyword reference.
- `ext/QuanticaMakieExt/QuanticaMakieExt.jl` | symbols: extension include chain | behavior: method activation with Makie backend.

## Function-level behavior checks
1. Symbol-to-source resolution:
   - `julia --project -e 'using Quantica, InteractiveUtils; loc = @which greenfunction(LP.honeycomb() |> hopping(1)); @assert occursin("greenfunction.jl", String(loc.file)); println("ok:which-greenfunction")'`
2. Observable shape/value probe:
   - `julia --project -e 'using Quantica; h = LP.square() |> onsite(4) - hopping(1) |> supercell(region = r -> norm(r) < 6); d = ldos(greenfunction(h)(0.1 + 1e-3im)); @assert !isempty(d[]) && all(isfinite, d[]); println("ok:ldos")'`
3. Serializer round trip probe:
   - `julia --project -e 'using Quantica; h = LP.linear() |> hopping(1) + @onsite((; u = 2) -> u); s = serializer(Float64, h); v = serialize(s; u = 4); h2 = deserialize!(s, v); @assert h2 == h(u = 4); println("ok:serializer")'`

## Test anchors for regression checks
- `test/test_hamiltonian.jl` | parametric Hamiltonian call/index behavior.
- `test/test_greenfunction.jl` | Green slicing/evaluation behavior.
- `test/test_show.jl` | API display and object-surface checks.
