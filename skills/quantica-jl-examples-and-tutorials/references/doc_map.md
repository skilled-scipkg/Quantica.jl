# quantica-jl documentation map: Examples and Tutorials

## Primary read order
1. `docs/src/tutorial/tutorial.md` | high-level tutorial roadmap.
2. `docs/src/tutorial/lattices.md` | starting lattice patterns.
3. `docs/src/tutorial/models.md` | model-term composition and parameterization.
4. `docs/src/tutorial/hamiltonians.md` | Hamiltonian assembly and evaluation.
5. `docs/src/tutorial/bandstructures.md` | band calculations and continuity tools.
6. `docs/src/tutorial/greenfunctions.md` | contact-based transport setup.
7. `docs/src/tutorial/observables.md` | LDOS/current/transmission/conductance/Josephson outputs.
8. `docs/src/examples.md` | additional example anchors.
9. `docs/src/tutorial/glossary.md` | terminology disambiguation while debugging.

## Startup command (from repo root)
- `julia --project -e 'using Quantica; h = LP.honeycomb() |> hopping(1); b = bands(h, range(0, 2pi, length = 11), range(0, 2pi, length = 11)); @assert length(b) > 0; println("ok:docs-tutorials")'`

## Validation checkpoints
- Tutorial-derived objects (`Hamiltonian`, `Bandstructure`, `GreenFunction`) instantiate successfully.
- Numeric probes pass before introducing plotting backends.
- Observable outputs are finite and aligned with selected slices/contacts.
