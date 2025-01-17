## Proof Artifact

Our proofs are verified in **Coq 8.13.0** and **8.15.1** (online, compiled by CI in this repository). We rely on two Coq libraries: [`metalib`](https://github.com/plclub/metalib) for the locally nameless representation in our proofs; and [`LibTactics.v`](https://softwarefoundations.cis.upenn.edu/plf-current/LibTactics.html), which is included in the directory.

### Getting Started

The steps to pull our Docker image, start a Docker container and compile the proofs are (`$` indicates a command in a shell):

1. Start Docker in your system.
2. In the host system, `$ docker load -i fip-artifact-env.tar` to load the image from the archive or `$ docker pull fipartifact/fip-artifact-env:latest` to pull our Docker image.
3. In the host system, `$ docker run -it -w /root fipartifact/fip-artifact-env:latest bash ` to start a container and a `bash` inside. (You can safely ignore `[WARNING] Running as root is not recommended` from opam)
4. The proofs are available in the container, `$ cd /root/fiplus/coq ` and `$ make` to compile our proofs. It may take approximately 20 minutes to finish. During the proving process, the file to prove will be logged as, for example, `COQC TypeSafety.v`.

If you wish to build our proofs in your system than a Docker container, here are the steps:

1. With [opam](https://opam.ocaml.org/), you can easily install Coq 8.13.0, [ott](https://github.com/ott-lang/ott), and [`metalib`](https://github.com/plclub/metalib) by:

   ```
   $ opam install coq.8.13.0
   $ opam install ott
   $ opam repo add coq-extra-dev https://coq.inria.fr/opam/extra-dev
   $ opam install coq-metalib
   ```

2. Follow the [readme](https://github.com/plclub/lngen/blob/master/README.txt) to install [lngen](https://github.com/plclub/lngen).
3. In `fiplus/coq`, `make` to compile our proofs.

### Proof Structure

* `syntax_ott.v`: all definitions, functions, and inductively defined relations, generated by [ott](https://github.com/ott-lang/ott)
* `rules_inf.v`: infrastructure proofs regarding locally nameless representation [^LN], generated by [lngen](https://github.com/plclub/lngen)
* `LibTactics.v`: useful tactics maintained by Arthur Chargueraud, available at [LibTactics](https://softwarefoundations.cis.upenn.edu/plf-current/LibTactics.html)
* `Infrastructure.v`: infrastructure lemmas based only on the syntax and well-formedness
* `TypeLemmas.v`: lemmas about ordinary types, splittable types
* `Subtyping.v`: lemmas about subtyping
* `Disjointness.v`: lemmas about disjointness
* `IsomorphicSubtyping.v`: lemmas about isomorphic subtyping and applicative distribution including binding related ones
* `TermLemmas.v`: lemmas about regularity and substitution on typing and lemmas about values and pre-values
* `KeyProperties.v`: lemmas about principal type and typing
* `Progress.v`: lemmas about determinism and progress, also includes lemmas about casting and consistency
* `TypeSafety.v`: lemmas about preservation, also includes lemmas about typing

[^LN]: Charguéraud, A. The Locally Nameless Representation. *J Autom Reasoning* **49,** 363–408 (2012). https://doi.org/10.1007/s10817-011-9225-2

### Correspondence

#### Definitions

| Paper       | Definition                      | File           | Name in Coq                |
| ----------- | ------------------------------- | -------------- | -------------------------- |
| Section 3.1 | Types                           | `syntax_ott.v` | `Inductive typ`            |
|             | Expressions                     | `syntax_ott.v` | `Inductive exp`            |
|             | Values                          | `syntax_ott.v` | `Inductive value`          |
|             | Term contexts                   | `syntax_ott.v` | `Definition ctx`           |
|             | Type contexts                   | `syntax_ott.v` | `Definition tctx`          |
| Figure 6    | Declarative subtyping           | `syntax_ott.v` | `Inductive sub`            |
| Figure 7    | Bidirectional typing            | `syntax_ott.v` | `Inductive Typing`         |
|             | Typing modes                    | `syntax_ott.v` | `Inductive dirflag`        |
|             | Pre-values                      | `syntax_ott.v` | `Inductive prevalue`       |
| Figure 8    | Applicative distribution        | `syntax_ott.v` | `Inductive appDist`        |
| Figure 9    | Arguments                       | `syntax_ott.v` | `Inductive arg`            |
|             | Evaluation contexts [^EvalCntx] | n/a            | n/a                        |
|             | Parallel application            | `syntax_ott.v` | `Inductive papp`           |
|             | Small-step semantics            | `syntax_ott.v` | `Inductive step`           |
| Figure 10   | Ordinary types                  | `syntax_ott.v` | `Inductive ord`            |
|             | Splittable types                | `syntax_ott.v` | `Inductive spl`            |
|             | Expression wrapping             | `syntax_ott.v` | `Inductive wrapping`       |
|             | Casting                         | `syntax_ott.v` | `Inductive casting`        |
| Figure 11   | Bottom-like types               | `syntax_ott.v` | `Inductive botLike`        |
|             | Top-like types                  | `syntax_ott.v` | `Inductive topLike`        |
| Figure 12   | Algorithmic subtyping           | `syntax_ott.v` | `Inductive algo_sub`       |
| Figure 13   | Type disjointness               | `syntax_ott.v` | `Inductive disjoint`       |
|             | Consistency                     | `syntax_ott.v` | `Inductive consistent`     |
| FIgure 14   | Isomorphic subtyping            | `syntax_ott.v` | `Inductive subsub`         |
| Figure 16   | Type well-formedness            | `syntax_ott.v` | `Inductive TWell`          |
|             | Type context well-formedness    | `syntax_ott.v` | `Inductive TCWell`         |
|             | Term context well-formedness    | `syntax_ott.v` | `Inductive CWell`          |
| Figure 17   | Value generator [^ValueGen]     | n/a            | n/a                        |
| Figure 18   | Disjointness axioms             | `syntax_ott.v` | `Inductive disjoint_axiom` |
| Figure 19   | Principal types                 | `syntax_ott.v` | `Inductive pType`          |

[^EvalCntx]: Rule `Step-cntx` in the paper is based on evaluation contexts, the equivalent rules in Coq are `Step-mergel,Step-merger,Step-appl,Step-tapp,Step-proj,Step-anno`
[^ValueGen]: Rule `EW-top` and `Cast-top` in the paper are based on a value generator, the equivalent rules in Coq are `EW-top,EW-topArr,EW-topAll,EW-topRcd` and `Cast-top,Cast-topArr,Cast-topAll,Cast-topRcd`

#### Lemmas, Theorems, and Corollaries

| Paper            | File                    | Name in Coq                                                  |
| ---------------- | ----------------------- | ------------------------------------------------------------ |
| Lemma 3.3        | `Subtyping.v`           | `Lemma dsub_decidable`                                       |
| Lemma 3.4        | `Disjointness.v`        | `Lemma disjoint_soundness`                                   |
| Lemma 3.5        | `KeyProperties.v`       | `Lemma inference_unique`                                     |
| Lemma 3.6        | `Subtyping.v`           | `Lemma split_sub`                                            |
| Lemma 4.1        | `Subtyping.v`           | `Lemma botLike_equiv`                                        |
| Lemma 4.2        | `Subtyping.v`           | `Lemma topLike_equiv`                                        |
| Lemma 4.3        | `Subtyping.v`           | `Lemma sub_inversion_split_r_equiv`                          |
| Lemma 4.4        | `Subtyping.v`           | `Theorem declarative_sub_eqv`                                |
| Lemma 4.5        | `Subtyping.v`           | `Lemma sub_decidable`                                        |
| Theorem 4.6      | `Disjointness.v`        | `Lemma disjoint_covariance`                                  |
| Theorem 4.7      | `KeyProperties.v`       | `Lemma disjoint_subst`                                       |
| Lemma 5.1        | `Progress.v`            | `Lemma consistent_casting_no_ambiguity`                      |
| Lemma 5.2        | `Progress.v`            | `Lemma casting_toplike`                                      |
| Lemma 5.3        | `Progress.v`            | `Lemma casting_sub`                                          |
| Lemma 5.4        | `Progress.v`            | `Lemma casting_unique`                                       |
| Theorem 5.5      | `Progress.v`            | `Theorem step_unique`                                        |
| Lemma 5.6        | `Progress.v`            | `Lemma casting_progress_1`                                   |
| Lemma 5.7        | `Progress.v`            | `Lemma wrapping_progress`                                    |
| Lemma 5.8        | `Progress.v`            | `Lemma papp_progress`<br />`Lemma papp_progress2`<br />`Lemma papp_progress3` |
| Theorem 5.9      | `Progress.v`            | `Theorem progress`                                           |
| Lemma 5.10        | `TypeSafety.v`          | `Lemma casting_consistent`                                   |
| Lemma 5.11       | `TypeSafety.v`          | `Lemma consistent_steps`                                     |
| Lemma 5.12       | `TypeSafety.v`          | `Lemma papp_consistent `<br />`Lemma papp_consistent2 `<br />`Lemma papp_consistent3 ` |
| Theorem 5.13     | `IsomorphicSubtyping.v` | `Lemma subsub2sub`                                           |
| Lemma 5.14       | `TypeSafety.v`          | `Lemma casting_preservation`                                 |
| Lemma 5.15       | `TypeSafety.v`          | `Lemma wrapping_preservation`                                |
| Lemma 5.16       | `TypeSafety.v`          | `Lemma papp_preservation`<br />`Lemma papp_preservation2`<br />`Lemma papp_preservation3` |
| Lemma 5.17       | `KeyProperties.v`       | `Lemma Typing_narrowing`                                     |
| Lemma 5.18       | `TypeSafety.v`          | `Lemma Typing_subst_1`                                       |
| Lemma 5.19, 5.20 | `TypeSafety.v`          | `Lemma Typing_subst_2_inf_subsub`                            |
| Theorem 5.21     | `TypeSafety.v`          | `Theorem preservation_multi_step`                            |
| Corollary 5.22   | `TypeSafety.v`          | `Theorem preservation`                                       |
| Lemma A.1        | `TermLemmas.v`          | `Lemma Typing_regular_0`<br />`Lemma Typing_regular_1`<br />`Lemma Typing_regular_2`<br />`Lemma Typing_regular_3` |
| Lemma A.2        | `KeyProperties.v`       | `Lemma prevalue_exists_ptype`                                |
| Lemma A.3        | `KeyProperties.v`       | `Lemma typ_prevalue_ptype`                                   |
| Lemma B.1        | `IsomorphicSubtyping.v` | `Lemma appDist_arrow_unique`<br />`Lemma appDist_forall_unique`<br />`Lemma appDist_rcd_unique` |
| Lemma B.2        | `KeyProperties.v`       | `Lemma Typing_chk_sub_2`                                     |
