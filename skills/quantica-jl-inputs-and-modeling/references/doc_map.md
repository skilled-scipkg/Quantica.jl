# quantica-jl documentation map: Inputs and Modeling

## Primary read order
1. `docs/src/tutorial/lattices.md` | lattice construction, selectors, and transforms.
2. `docs/src/tutorial/models.md` | `onsite`/`hopping` terms, selectors, and parametric modifiers.
3. `docs/src/tutorial/hamiltonians.md` | Hamiltonian assembly, call/indexing semantics, transform ordering.
4. `docs/src/tutorial/greenfunctions.md` | Green solver selection, contacts, and slicing semantics.
5. `docs/src/tutorial/bandstructures.md` | spectrum/bands workflows and defect handling.
6. `docs/src/advanced/meanfield.md` | self-consistent loops and fixed-point integration.
7. `docs/src/advanced/nonspatial.md` | non-spatial parameter workflows and matrix-valued terms.

## Startup command (from repo root)
- `julia --project -e 'using Quantica; h = LP.honeycomb() |> hopping(1); @assert ishermitian(h((0.0, 0.0))); println("ok:docs-modeling")'`

## Validation checkpoints
- Hamiltonian probes (`h[(dn...)]`, `h(phi...)`) return expected shapes.
- Transform/supercell ordering choices are validated on small systems.
- Green/observable values are finite before scaling system size.
