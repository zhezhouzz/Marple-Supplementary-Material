# Proof Readme #

## Proof File Structures

The files are structured as follows:
+ Definitions and proofs of our core language **λ<sup>E</sup>**.
  - `Atom.v`: Definitions and notations of atoms (variables) in **λ<sup>E</sup>**.
  - `Tactics.v`: Some auxiliary tactics.
  - `CoreLang.v`: Definitions and notations of terms in **λ<sup>E</sup>**.
  - `NamelessTactics.v`: Auxiliary tactics for the locally nameless representation.
  - `CoreLangProp.v`: Lemmas for our core language **λ<sup>E</sup>**.
  - `OperationalSemantics.v`: Definitions and notations of the small-step operational semantics of **λ<sup>E</sup>**.
  - `BasicTyping.v`: Definitions and notations for the basic typing.
  - `BasicTypingProp.v`: Lemmas for the basic typing rules.
  - `Qualifier.v`: Definitions and notations for qualifiers.
  - `ListCtx.v`: Definitions and notations for reasoning about type context.
  - `RefinementType.v`: Definitions and notations of Hoare Automata Types.
  - `Denotation.v`: Definitions and notations of the automaton and type denotation.
  - `Instantiation.v`: Lemmas for the substitution under type context.
  - `Typing.v`: Definitions and notations used in the typing rules; as well as statement and proof of the fundamental and soundness theorem.

## Compilation and Dependencies

Our formalization is tested against `Coq 8.16.1`, and requires the libraries
`coq-stdpp 1.8.0` and `coq-hammer-tactics 1.3.2+8.16`. The easiest way to install the
dependencies is via [OPAM](https://opam.ocaml.org/doc/Install.html). For
example:

```sh
# If you have not already used opam
opam init
opam update
# Create a new opam environment for testing this project
opam switch create HAT --package=ocaml-variants.4.14.1+options,ocaml-option-flambda
eval $(opam env)
opam repo add coq-released https://coq.inria.fr/opam/released
opam update
opam pin coq 8.16.1
opam pin coq-stdpp 1.8.0
opam pin coq-hammer-tactics 1.3.2+8.16
```

To build and check the Coq formalization, simply run `make` in the
`formalization` directory.

Our formalization takes inspiration and ideas from the following work, though does not directly depend on them:
- [Software Foundations](https://softwarefoundations.cis.upenn.edu/): a lot of our formalization is inspired by the style used in Software Foundations.
- [The Locally Nameless Representation](https://chargueraud.org/research/2009/ln/main.pdf): we use the locally nameless representation for variable bindings.

## Paper-to-artifact Correspondence

| Definition/Theorems          | Paper                                                                       | Definition                                                                                                                | Notation                        |
|------------------------------|-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| Term Syntax                  | Figure 2                                                                    | mutually recursively defined as values (`value`) and expressions (`tm`) in file `CoreLang.v` (line `45`)                      |                                 |
| Trace Syntax                 | Figure 3                                                                    | `trace` in file `Trace.v` (line `17`)                                                                                     | `[ev{ op ~ v1 := v2 }]`         |
| Type Syntax                  | Figure 4                                                                    | basic types (`ty`) in file `CoreLang` (line `20`), Refinement Types (`pty`) and Hoare Automata Types (`hty`) in file `RefinementType.v` (line `40` and `68`) | `{: b `&#124;` ϕ }`, `-: t ⤑[: s `&#124;` A ▶ B ]`, and `[: s `&#124;` A ▶ B ]`                               |
| Operational semantics        | Figure 3 (and Figure 12 in supplementary materials shows full set of rules) | `step` in file `OperationalSemantics.v` (line `14`)                                                                       | `α ⊧ e ↪{ α } e`        |
| Basic typing rules           | Figure 13 (supplementary materials)                                         | mutually recursive definition of `tm_has_type` and `value_has_type` in file `BasicTyping.v` (line `36`)                     | `Γ ⊢t e ⋮t T` and `Γ ⊢t v ⋮v T` |
| Type Erasure                 | Figure 7                                                                    | `hty_erase` (line `88`) and `ctx_erase` (line `300`) in file `RefinementTypes.v`                                          | `⌊ τ ⌋` and `⌊ Γ ⌋*`            |
| Well-formedness typing rules | Figure 15 (supplementary materials)                                                                  | `wf_hty` in file `Typing.v` (line `47`)                                                               | `Γ ⊢WF τ`                       |
| Subtyping rules              | Figure 7 (and Figure 15 in supplementary materials shows full set of rules) | `subtyping` in file `Typing.v` (line `59`)                                                                                | `Γ ⊢ τ1 <⋮ τ2`                  |
| Typing rules                 | Figure 8 (and Figure 16 in supplementary materials shows full set of rules) | given by the mutually inductive types `value_type_check` and `term_type_check` in file `Typing.v` (line `71`)             | `Γ ⊢ e ⋮t τ` and `Γ ⊢ v ⋮v τ`   |
| SFA language                 | Figure 10                                                                   | `langA` in file `Denotation.v` (line `33`)                                                                                | `a⟦ A ⟧`                        |
| Type denotation              | Figure 10                                                                   | `htyR` in file `Denotation.v` (line `93`)                                                                                 | `⟦ τ ⟧`                         |
| Type context denotation      | Figure 10                                                                   | `ctxRst` in file `Denotation.v` (line `117`)                                                                              |                                 |
| Fundamental Theorem          | Theorem 4.10                                                                | `fundamental` in file `Typing.v` (line `1775`)                                                                               |                                 |
| Soundness theorem            | Theorem 4.11                                                                | `soundness` in file `Typing.v` (line `2523`)                                                                                 |                                 |

## Differences Between Paper and Artifact

- Basic types: our formalization only has two base types: nat and bool.
- Effectful operators: to simplify the syntax, all operators in our formalization only take one argument
- Pure operators (e.g., arithmetic operators): they are treated as effectful
 operators, whose return value is independent of the context trace, and will not
 interfere the result of other operators.
- For simplicity, pattern-matching only matches on Boolean discriminees.
  Pattern matching on natural numbers such as

```
match n with
| 0 -> e1
| S m -> e2
```

is implemented as follows:

```
let cond = op_eq_zero n in
match cond with
| true -> e1
| else ->
  let m = op_minus_one n in
  e2
```

- We assume all input programs are alpha-converted, such that all variables have
  unique names.
- We use the [locally nameless
  representation](https://chargueraud.org/research/2009/ln/main.pdf) to
  represent binders in all of our definitions, thus they look slightly different
  from the ones shown in the paper.
- We encode the propositions in the refinement types as shallowly embedded Coq
  propositions.
- As automata can only have the `<ret t>` event at the end (per our
  well-formedness condition), the definition of automata in our formalization
  does not include this event, and we can recover the definition in the paper by
  taking a pair of an automata and a refinement type as the return event.
- The built-in operator typing relation `Γ ⊢ (θ, t) ∈ Δ` in the paper is paramaterized as any relation that is well-formed (Definition 4.9).
- The post automata in a Hoare Automata Type is in union normal form, consistent
  with how they are treated in the typing rules.
