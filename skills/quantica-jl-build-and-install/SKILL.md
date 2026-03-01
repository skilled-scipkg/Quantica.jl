---
name: quantica-jl-build-and-install
description: This skill should be used when users ask about build and install in quantica-jl; it prioritizes documentation references and then source inspection only for unresolved details.
---

# quantica-jl: Build and Install

## High-Signal Playbook
### Route conditions
- Use this skill for package install, environment setup, backend extensions, and first-run smoke checks.
- Route lattice/model/Hamiltonian design to `quantica-jl-inputs-and-modeling`.
- Route end-to-end runnable recipes to `quantica-jl-examples-and-tutorials`.
- Route symbol-level API/docstring lookup to `quantica-jl-api-and-scripting`.
- Route Wannier90 import workflows to `quantica-jl-src`.

### Triage questions
- Which Julia version is running (`VERSION`)?
- Are you using a project environment (`Pkg.activate`) or the global environment?
- Do you need plotting (`qplot`) right away?
- Do you need optional eigensolver backends (Arpack, ArnoldiMethod, KrylovKit, LinearMaps)?
- Do you want only a smoke test or a full simulation setup?

### Canonical workflow
1. Confirm Julia `>= 1.9` from `README.md` and `Project.toml`.
2. Install Quantica: `import Pkg; Pkg.add("Quantica")` (`docs/src/index.md`).
3. If plotting is needed, install a Makie backend (commonly `GLMakie`) (`docs/src/index.md`).
4. Load package(s): `using Quantica` and optional `using GLMakie`.
5. If a non-default solver backend is requested, load its package first (`using Arpack`, `using ArnoldiMethod`, etc.) so extension methods are available.
6. Run one tiny Hamiltonian + spectrum smoke test.
7. If behavior is unclear, only then inspect `references/source_map.md`.

### Terminal quickstart (from repo root)
- `julia --project -e 'using Quantica; h = LP.honeycomb() |> hopping(1); e, _ = spectrum(h, (0.0, 0.0)); @assert !isempty(e) && all(isfinite, e); println("ok:install-smoke")'`

### Minimal working example
```julia
import Pkg
Pkg.add("Quantica")
Pkg.add("GLMakie") # optional, only for plotting

using Quantica
lat = LP.honeycomb()
h = lat |> hopping(1)
e, psi = spectrum(h, (0.0, 0.0); solver = ES.LinearAlgebra())
@show e

# Optional plotting path
using GLMakie
qplot(h, hide = :cell)
```

### Pitfalls and fixes
- `qplot` error about missing backend: load `using GLMakie` (or another Makie backend) before plotting.
- Julia too old: Quantica requires Julia 1.9+.
- `ES.Arpack()` or other extension solver fails: load backend package first so extension methods in `ext/QuanticaArpackExt.jl`, `ext/QuanticaArnoldiMethodExt.jl`, `ext/QuanticaKrylovKitExt.jl`, and `ext/QuanticaLinearMapsExt.jl` activate.
- Environment conflicts: activate an explicit project and reinstall in that environment.
- Long first-call latency: warm up with a tiny model before heavy runs.

### Convergence and validation checks
- `using Quantica` succeeds without method errors.
- `LP.honeycomb() |> hopping(1)` builds a Hamiltonian object.
- `spectrum(h, (0.0, 0.0))` returns finite eigenvalues.
- If plotting is enabled, `qplot(h)` renders successfully.
- If an optional solver is selected, one call through that solver completes.

## Primary documentation references
- `README.md`
- `docs/src/index.md`
- `Project.toml`

## Docs-first workflow
- Start with the primary references above.
- If details are missing, inspect `references/doc_map.md` for the complete topic document list.
- If ambiguity remains after docs, inspect `references/source_map.md` and start with the prioritized entries.
- Cite exact documentation file paths in responses.

## Source entry points for unresolved issues
- `src/Quantica.jl`: exported API surface, include order, and `qplot` fallback error when no backend is loaded.
- `ext/QuanticaMakieExt/QuanticaMakieExt.jl`: plotting extension wiring for `qplot`, `plotlattice`, and `plotbands`.
- `ext/QuanticaMakieExt/docstrings.jl`: authoritative plotting keyword/doc behavior.
- `ext/QuanticaArpackExt.jl`, `ext/QuanticaArnoldiMethodExt.jl`, `ext/QuanticaKrylovKitExt.jl`, `ext/QuanticaLinearMapsExt.jl`: extension adapters for optional solver backends.
- `Project.toml`: `weakdeps`, `extensions`, and compatibility gates that control optional features.
- Prefer targeted search: `rg -n "<symbol_or_keyword>" ext src`.
