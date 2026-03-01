# quantica-jl documentation map: Src

## Primary read order
1. `docs/src/advanced/wannier90.md` | `EP.wannier90` import path, tolerances (`dim`, `htol`, `rtol`), and operator usage.
2. `docs/src/tutorial/hamiltonians.md` | post-import Hamiltonian call/index semantics.
3. `docs/src/tutorial/models.md` | import-time or post-import modifier patterns.

## Local smoke input files
- `test/wannier_test_tb.dat`
- `test/wannier_test2_tb.dat`

## Startup command (from repo root)
- `julia --project -e 'using Quantica; w = EP.wannier90("test/wannier_test_tb.dat"; dim = 2, htol = 1e-5, rtol = 1e-4); h = hamiltonian(w); @assert size(h[(0, 0)], 1) > 0; println("ok:docs-wannier")'`

## Validation checkpoints
- Wannier import completes and reports non-empty operators.
- `h[(0, 0)]` and `position(w)[SA[0, 0]]` return non-empty matrices.
- Parameterized modifiers change the evaluated Hamiltonian as expected.
