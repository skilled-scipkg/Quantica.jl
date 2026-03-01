# quantica-jl documentation map: Build and Install

## Primary read order
1. `docs/src/index.md` | install commands for Quantica and plotting backends.
2. `README.md` | package-level quickstart, feature scope, and support pointers.
3. `Project.toml` | exact Julia/version compatibility and optional dependency gates.

## Startup command (from repo root)
- `julia --project -e 'using Quantica; h = LP.honeycomb() |> hopping(1); e, _ = spectrum(h, (0.0, 0.0)); @assert !isempty(e) && all(isfinite, e); println("ok:docs-build")'`

## Validation checkpoints
- `using Quantica` succeeds in a clean Julia session.
- A tiny spectrum call returns finite eigenvalues.
- Plotting/backend expectations are explicit before calling `qplot`.
