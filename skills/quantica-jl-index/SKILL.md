---
name: quantica-jl-index
description: This skill should be used when users ask how to use quantica-jl and the correct generated documentation skill must be selected before going deeper into source code.
---

# quantica-jl Skills Index

## Route the request
- Classify the request into one of the generated topic skills listed below.
- Prefer abstract, workflow-level guidance for large scientific packages; do not attempt full function-by-function coverage unless explicitly requested.

## Simulation bootstrap
- Start all runnable checks from repository root with `julia --project -e 'using Quantica; println(\"ok\")'`.
- If this fails, route to `quantica-jl-build-and-install` before any deeper modeling/debugging guidance.

## Generated topic skills
- `quantica-jl-inputs-and-modeling`: Inputs and Modeling (inputs, system setup, models, and physical parameterization)
- `quantica-jl-examples-and-tutorials`: Examples and Tutorials (worked examples, tutorials, and cookbook usage)
- `quantica-jl-api-and-scripting`: API and Scripting (language bindings, APIs, and programmatic interfaces)
- `quantica-jl-build-and-install`: Build and Install (build, installation, compilation, and environment setup)
- `quantica-jl-src`: Src (documentation grouped under the 'src' theme)

## Documentation-first inputs
- `docs/src`

## Tutorials and examples roots
- `docs/src/tutorial`

## Test roots for behavior checks
- `test`

## Escalate only when needed
- Start from topic skill primary references.
- If those references are insufficient, search the topic skill `references/doc_map.md`.
- If documentation still leaves ambiguity, open `references/source_map.md` inside the same topic skill and inspect the suggested source entry points.
- Use targeted symbol search while inspecting source (e.g., `rg -n "<symbol_or_keyword>" ext src`).

## Source directories for deeper inspection
- `ext`
- `src`
