# quantica-jl documentation map: API and Scripting

## Primary read order
1. `docs/src/api.md` | API index and generated reference surface.
2. `docs/src/tutorial/observables.md` | practical API usage for `ldos`, `current`, `transmission`, `conductance`, `josephson`.
3. `docs/src/advanced/serializers.md` | `serializer`/`serialize!`/`deserialize!` workflows for scripted loops.
4. `docs/src/tutorial/greenfunctions.md` | Green-slice call and indexing semantics used by many APIs.

## Startup command (from repo root)
- `julia --project -e 'using Quantica; h = LP.square() |> onsite(4) - hopping(1) |> supercell(region = r -> norm(r) < 6); d = ldos(greenfunction(h)(0.1 + 1e-3im)); @assert !isempty(d[]) && all(isfinite, d[]); println("ok:docs-api")'`

## Validation checkpoints
- `@which` and docstrings resolve to expected symbols.
- Observable outputs are finite and dimensionally consistent.
- Serializer round-trip preserves model semantics.
