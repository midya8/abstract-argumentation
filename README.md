# ArgumentLab

[Live inspector](https://midya8.github.io/argumentlab/) · [Method and six inspected sources](METHOD.md)

I built this to take a small set of arguments and the attacks between them and show, exactly, what follows under nine different definitions of an acceptable set. You hand it arguments and directed attacks; it goes through every subset, checks conflicts and defenses, and compares the nine extension families. The Python side uses bitsets and the JavaScript side uses sets, and the two agree on every complete computational witness. There is no model, no API, and no private dataset involved.

There are four inspectors, one idea each:

- **Extension families** — enumerate subsets, defenses, grounded steps, labellings and acceptance under nine semantics. ([Base report](#base-report))
- **Attack switches** — keep the vocabulary fixed and try every configuration of the hypothetical edits you supply. ([Attack-switch sensitivity](#attack-switch-sensitivity-extension))
- **SCC recursion** — CF2/stage2 component restrictions and recomputed subproblems. ([SCC recursion](#scc-recursion-03))
- **Ideal/eager** — the greatest admissible subset of the preferred/semi-stable intersection, with every pruning round kept. ([Ideal/eager](#idealeager-semantics-04))

These are different definitions applied to the attacks you supply. They are not a ranking of what is true, what persuades, or which argument is strongest. One thing that trips people up: empty extension families. Classical skeptical acceptance can be vacuously true when there is nothing to check against, so the example further down is worth reading carefully.

Right now the suite is **53 Python tests and 18 Node/actual-Chrome tests**. The counts in the release sections below are from earlier checks — they describe what was checked then, not extra tests on top of these. All of them are bounded definition and integration checks. They are not evidence of benchmark superiority.

## Base report

- Every attacker and every selected defender, including attackers nobody answers and self-attacks.
- All nine families — conflict-free, admissible, complete, naive, preferred, stable, semi-stable, stage, and grounded — with every tie kept.
- Every strict admissible/conflict-free superset and every range-dominance witness. Inclusion-maximal is not the same as maximum cardinality.
- The full grounded characteristic-function chain, and the local legality of every complete three-valued labelling.
- Credulous and classical skeptical acceptance, every extension that contains or excludes an argument, first witnesses and counterexamples, and the empty-family conventions spelled out.
- A directed graph with separate curves for opposite attacks, self loops, and highlighting for the selected set. Every table, the full JSON, and the export stay inspectable.

The original strict controls are there to catch a few specific surprises: a smaller preferred extension can coexist with a larger one; the preferred intersection can exceed the grounded set and can even fail admissibility; stage can differ from semi-stable. The point of those cases is to show the definitions apart, not to crown one semantics as best.

## When a family comes up empty

Take an argument that attacks itself: no stable extension exists. Classical skeptical acceptance is vacuously true here, because no extension excludes the argument — but that doesn't mean the position accepts it. Credulous acceptance is false. The separate `skeptical_nonvacuous` field is null and `exists` is false, and the interface shows a warning. A framework with zero arguments is different: it has one empty extension, `[0]`, not an empty family `[]`.

## Trying it

```sh
python -m pip install .
argumentlab examples/demo.json --output /tmp/report.json
python -m argumentlab examples/demo.json
python -m unittest discover -s tests -v
PYTHONPATH=. python tools/build_audits.py
npm test
sh tools/wheel_smoke.sh
python -m http.server 8000 --directory docs
```

Then open http://localhost:8000. Browser checks need Node 22+ and Chrome; `CHROME_BIN` lets you point at a different Chrome. `uv` is needed for the isolated wheel check. The native CLI otherwise runs on Python 3.10+ with no runtime dependency. CI runs Python 3.10–3.13 and regenerates the retained JSON exactly. The browser side runs entirely locally once the static assets have loaded.

## How the base inspector was checked

19 Python test functions cover every one of the 531 directed frameworks with zero through three arguments, loops included, plus 120 seeded larger frameworks, an independent set-based family oracle, and exhaustive three-valued labelling enumeration. Witness replay checks every attacker/defender row, every strict superset/range witness, every acceptance quantifier, and every grounded step. Permutation, strict contrast, input/no-mutation, and CLI checks are in there too.

Five Node/actual-Chrome tests compare all 531 full native reports on 344,023 exact scalar leaves and 19 retained controls on 152,884 leaves, plus 100 additional independent ternary-labelling controls. The retained controls contain 769 subsets, 171 family records, 576 per-family argument acceptance records, 39 complete labellings, and 29 grounded steps. Actual Chrome checks every selected subset/family/labelling/step record, all tables and cards, graph edges, selected sets, path geometry, export, and invalid/stale clearing. All 19 controls are checked for page containment at 320/390/1280. Repeated graph selections cover 19,622 edge states — that is edge states, not that many distinct supplied attacks. The installed wheel reproduces all 19 reports outside the checkout, verifies four module origins, and compares console/module CLI output.

Those counts describe the original bounded controls and test coverage. They say nothing about speed, solver quality, or novel research.

## Scope and limits

Eight arguments maximum, all subsets enumerated. The attack relations you supply are assumptions, not evidence about real-world truth, argument strength, debate winners, or safe decisions. This uses established Dung semantics. It is not a scalable or new solver, and it doesn't do structured-argument construction, dialogue games, probability models, benchmark superiority, or machine-checked proofs. Differences between the native and browser JSON parsers are documented in the method. It is not affiliated with the existing ArguLab or ARGTEACH systems, and no external solver code, examples, or datasets were copied.

Original implementation, independent checks, and synthetic controls: Midya Rahmani. MIT license.

## Attack-switch sensitivity extension

[Live edit-cube inspector](https://midya8.github.io/argumentlab/switches.html) · [Method and dynamics sources](SWITCHES.md)

Hold the arguments and semantics fixed, supply at most six switchable attack pairs, and look at every configuration. It keeps all successful configurations, all tied minimum-edit matches, and all inclusion-minimal matches — which can be larger. Every configuration keeps its complete extension families, exact query witnesses, and full subset classifications, and every single-switch cube edge keeps the extensions gained and lost and the old and new answers. Pick a graph to see its full defense, grounded, and labelling audit.

Classical skepticism and nullable nonvacuous skepticism are different modes, and a null nonvacuous answer matches neither true nor false. The original strict controls show minimal-vs-minimum differences and acceptance being lost and regained. Six arguments maximum, exhaustive enumeration, hypothetical supplied edits only: a failure is relative to the switches you allowed, not a general impossibility. No causal, strategic, or new-solver claim. The established enforcement research is attributed, not relabelled as original theory.

```sh
argumentlab-switches examples/switches.json --configuration-mask 3
python -m argumentlab.switches examples/switches.json --output /tmp/switch-report.json
PYTHONPATH=. python tools/build_switch_audits.py
```

All 19 original tests and the five original Node/Chrome tests still stand. The switch release checked 32 combined Python tests on four versions and ten Node/actual-Chrome tests. The new native checks cover every one of the 256 two-argument base-graph/switch-set pairs, all 72 semantics/mode/target combinations, 80 seeded larger systems, and every retained control with an independent family oracle. Cross-language equality covers 256 full sensitivity reports, 21 retained reports, and another 152 mode/seeded cases. The new browser suite checks all 209 configurations, 5,205 subset/defense records, 802 acceptance cards, 423 transitions, and both exports, with all 21 controls at 320/390/1280. The installed wheel reproduces both the original and switch reports, five module origins, and both console/module CLIs. The extension method has the exact scalar counts and boundaries.

## SCC recursion (0.3)

[Open the CF2/stage2 inspector](https://midya8.github.io/argumentlab/scc.html) · [Exact contract, reproduction and inspected foundations](SCC.md)

Version 0.3 adds a separate six-argument SCC-recursive inspector. Every candidate keeps all component-defeat edges, restricted child graphs, and child membership obligations. SCCs are recomputed after deletion, and you can drill into the surviving child with the selected part preserved. Global naive/stage is compared against CF2/stage2 without treating either as a ranking. An independent set-based verifier checks every record and a second published fixed-point characterization; optional hash-pinned ASPARTIX/clingo checks agree on 725 frameworks and 1,450 extension families. The native and JavaScript full reports agree on 725 frameworks, 3,052 induced subproblems, 16,375 candidates, and 38,184 obligations.

```sh
argumentlab-scc examples/scc.json --output /tmp/scc.json
python -m argumentlab.scc --verify /tmp/scc.json
```

The SCC release checked 39 Python functions on 3.10–3.13 and 15 Node/Chrome tests. The new Chrome checks cover 42 reports at 320/390/1280, 171 subproblems, 1,056 candidate selections, and 14 actual independently verified downloads. The installed wheel reproduces 19 defense, 21 switch, and 14 SCC controls outside the checkout, checks seven module origins, rejects 14 forged SCC reports, and checks both generation and verification entrypoints. These are bounded coverage counts, not benchmark claims. The existing inspectors stay separate; the published semantics and encodings are credited in SCC.md. No new theorem, scalable solver, human persuasion, or universal-semantics ranking is claimed.

## Ideal/eager semantics (0.4)

[Live ideal/eager inspector](https://midya8.github.io/argumentlab/ideal.html) · [Method, scope and inspected foundations](IDEAL.md)

Intersect the preferred or semi-stable positions, then keep the greatest subset that defends itself against every original attacker. One shared argument can lose its defender when another shared argument is pruned. The default original control shows 152→136→128→128, with isolated a7 surviving. A single pass and an induced-pool graph both give the wrong answer.

You can inspect both parent families, the simultaneous removal rounds, every original attacker and response, all 256 bounded candidate subsets, strict eligible supersets, and per-argument membership/removal records. Export the certificate and replay it with `argumentlab-ideal report.json --verify`. Fourteen original controls, all 531 graphs on 0..3 arguments, and 100 seeded graphs agree across native bitsets, independent complete-labelling replay, and JavaScript sets. Optional actual PyArg 2.0.2 matches the same 645 frameworks' ideal/eager families.

Finite 0..8 arguments only. These are established ideal/eager semantics — not new theory, truth scoring, persuasion, scalable solving, or arbitrary infinite-framework eager uniqueness. Original attackers outside the shared pool still count.
