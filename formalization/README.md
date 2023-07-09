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
  - `ListCtx.v`: Definitions and notations for reasoning about polymorphic contexts.
  - `RefinementType.v`: Definitions and notations of Hoare Automata Types.
  - `Denotation.v`: Definitions and notations of the automaton and type denotation.
  - `Instantiation.v`: Lemmas for the substitution under under type context.
  - `Typing.v`: Definitions and notations used in the typing rules; as well as statement and proof of the fundamental and soundness theorem.

## Compilation and Dependencies

The project is organized by the coq make file `_CoqProject`, which may be executed by running `make` (about `10` min).

    $ make

Our formalization is tested against `Coq 8.16.1`. We also rely on the library `coq-stdpp 1.8.0` and `coq-hammer 1.3.2`.

Our formalization takes inspiration and ideas from the following work, though does not directly depend on them:
- [Software Foundations](https://softwarefoundations.cis.upenn.edu/): a lot of our formalization is inspired by the style used in Software Foundations.
- [The Locally Nameless Representation](https://chargueraud.org/research/2009/ln/main.pdf): we use the locally nameless representation for variable bindings.

## Paper-to-artifact Correspondence

| Definition/Theorems          | Paper                                                                       | Definition                                                                                                                | Notation                        |
|------------------------------|-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| Term Syntax                  | Figure 2                                                                    | mutual recursive defined as values (`value`) and expressions (`tm`) in file `CoreLang.v` (line `45`)                      |                                 |
| Trace Syntax                 | Figure 3                                                                    | `trace` in file `Trace.v` (line `17`)                                                                                     | `[ev{ op ~ v1 := v2 }]`         |
| Type Syntax                  | Figure 4                                                                    | basic types (`ty`) in file `CoreLang` (line `20`) and Hoare Automata Types (`hty`) in file `RefinementType.v` (line `68`) | `{: b `&#124;` ϕ }`, `-: t ⤑[: s `&#124;` A ▶ B ]`, and `[: s `&#124;` A ▶ B ]`                               |
| Operational semantics        | Figure 3 (and Figure 12 in supplementary materials shows full set of rules) | `step` in file `OperationalSemantics.v` (line `14`)                                                                       | `α '⊧' e '↪{' α' '}' e'`        |
| Basic typing rules           | Figure 13 (supplementary materials)                                         | mutual recursive definition of `tm_has_type` and `value_hsa_type` in file `BasicTyping.v` (line `36`)                     | `Γ ⊢t e ⋮t T` and `Γ ⊢t v ⋮v T` |
| Type Erasure                 | Figure 7                                                                    | `hty_erase` (line `88`) and `ctx_erase` (line `300`) in file `RefinementTypes.v`                                          | `⌊ τ ⌋` and `⌊ Γ ⌋*`            |
| Well-formedness typing rules | Figure 15                                                                   | `wf_hty` in file `Typing.v` (line `47`) in file `Typing.v`                                                                | `Γ ⊢WF τ`                       |
| Subtyping rules              | Figure 7 (and Figure 15 in supplementary materials shows full set of rules) | `subtyping` in file `Typing.v` (line `59`)                                                                                | `Γ ⊢ τ1 <⋮ τ2`                  |
| Typing rules                 | Figure 8 (and Figure 16 in supplementary materials shows full set of rules) | given by the mutually inductive types `value_type_check` and `term_type_check` in file `Typing.v` (line `71`)             | `Γ ⊢ e ⋮t τ` and `Γ ⊢ v ⋮v τ`   |
| SFA language                 | Figure 10                                                                   | `langA` in file `Denotation.v` (line `33`)                                                                                | `a⟦ A ⟧`                        |
| Type denotation              | Figure 10                                                                   | `htyR` in file `Denotation.v` (line `93`)                                                                                 | `⟦ τ ⟧`                         |
| Type context denotation      | Figure 10                                                                   | `ctxRst` in file `Denotation.v` (line `117`)                                                                              |                                 |
| Fundamental Theorem          | Theorem 4.10                                                                | `fundamental` in file `Typing.v` (line `?`)                                                                               |                                 |
| Soundness theorem            | Theorem 4.11                                                                | `soundness` in file `Typing.v` (line `?`)                                                                                 |                                 |
> Readers can find the supplemental materials in `doc/poirot-SM.pdf`.

## Differences Between Paper and Artifact

- Basic types: our formalization only has two base types: nat and bool.
- Operatros: To simplify the syntax, all operators in our formalization only takes one argument; the pure opeartors, for example, arithemetic operator (e.g., `op_eq_zero` and `op_plus_one`) are treated as effectful opertaors, whose return value is independent from the context trace, and will not intereface the result values of other operators.
- In the formalization, to simplify the syntax, pattern-matching can only pattern-match against Boolean variables. Pattern matching over natural numbers

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

- We assume all input programs are alpha-converted, such that all variables have unique names.
- We use the [locally nameless representation](https://chargueraud.org/research/2009/ln/main.pdf) in all terms, values, types, and type context, thus the definitions look slightly different from the definitions shown in the paper.
- We encode the propositions in the refinement type as Coq propositions. In order to capture the free variables and the bound variables (see locally nameless representation), TODO: quanifiers. Following the encoding above, for exmaple, the base refinement type `[v:b | φ]` will be encoded as `[: b| φ]`, where `φ` contains a local bound variable with index `0`.
