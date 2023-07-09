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
| Type Syntax                  | Figure 4                                                                    | basic types (`ty`) in file `CoreLang` (line `20`) and Hoare Automata Types (`hty`) in file `RefinementType.v` (line `68`) |                                 |
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

- Our formalization only has two base types: nat and bool.
- To simplify the syntax, our formalization don't treat the operators (e.g. `+`) as values. Alternately, we can define the operators as values using lambda functions. For example, the value `+` can be defined as

```
fun (x: nat) (y: nat) =
    let (res: nat) = x + y in
    res
```

- Our formalization only has four operators: `+`, `==`, `<`, `nat_gen`. Other operators shown in the paper can be implemented in terms of these. E.g., the minus operator can be defined as:

```
let minus (x: nat) (y: nat) =
    let (diff: nat) = nat_gen () in
    if x + diff == y then diff else err
```

In addition, to simplify the syntax, all operators take two input arguments; e.g., the random nat generator takes two arbitrary numbers as input.
- In the formalization, to simplify the syntax, pattern-matching can only pattern-match against Boolean variables. Pattern matching over natural numbers

```
match n with
| 0 -> e1
| S m -> e2
```

is implemented as follows:

```
if n == 0 then e1
else let m = n - 1 in e2
```

- We assume all input programs are alpha-converted, such that all variables have unique names.
- We use the [locally nameless representation](https://chargueraud.org/research/2009/ln/main.pdf) in all terms, values, refinement, and type context, thus the definitions look slightly different from the definitions shown in the paper.
- We encode the propositions in the refinement type as Coq propositions. In order to capture the free variables and the bound variables (see locally nameless representation), all propositions will be constructed with
  + a set of atoms (variables) `d`, which are the free variables in the proposition.
  + a natural number `n`, which indicates the upper bound of the bound variables.
- Following the encoding above, for example, the base Hoare Automata Type `[v:b | φ]` will be encoded as `[v:b | n | d | φ]`.
- The substitution of refinement types is formalized into states (a mapping from variables to values), helping to eliminate termination checks of the fixpoint function in Coq when we define the logical relation. Precisely
  + the definition of the type denotation has the form `{ n; bst; st }⟦ τ ⟧` instead of `⟦ τ ⟧`, where `(n, bst)` is the state of the bound variables, `st` is the state of the free variables.
  + the definition of the type denotation under the type context has the form `{ st }⟦ τ ⟧{ Γ }` instead of `⟦ τ ⟧{ Γ }`, where `st` is the state of the free variables. Here we omit the bound state `(n, bst)` thus all types in the type context are locally closed (see locally nameless representation).
- In the formalization, our coverage typing rules additionally require that all the branches of a pattern-matching expression are type-safe in the basic type system (they may not be consistent with the Hoare Automata Type we want to check). We didn't mention it in the original paper, however, we will fix it in the second round submission.
- For sake of convenience of the proof, we split a single typing rule into different cases (then we can prove these cases separately during the induction proof). For example, the rule `TLetE` in Figure 13 (in the appendix) is split into `UT_Lete_base` and `UT_Lete_arr` in our proof.
