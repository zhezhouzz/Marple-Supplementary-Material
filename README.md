# POPL 24 Supplementary Material

## Results

The Coq formalization of our core language and its soundness theorem are included in the "formalization" subdirectory.

## Benchmarks

Details about the `13` benchmarks used in our evaluation are given in individual folders with the same name as the benchmark; these folders are all located under "benchmarks". Each benchmark has a file `prog.ml` that presents
  + the effect operators used in this benchmark (notated as `type effect = ...`)
  + the source code of client program (an OCaml program `let CLIENT_NAME = ...`)
  + the Hoare Automata Type we want to check against (an OCaml program with the same as the source code but notated as `[@assert]`).

For example, there are two effect operators in `minset\prog.ml`: `Setinsert` and `Setmem`. The client code `min_insert` with be type checked against the HAT with the same name (`let[@assert] min_insert ...`).

## Types of Built-in Operators

The built-in types for the pure operators and effect operators are included in file `predefined/builtin_pure_typing.ml` and `predefined/builtin_eff_typing.ml`.
