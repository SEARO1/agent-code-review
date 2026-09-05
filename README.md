# Agent Code Review

Review skills for code authored or substantially modified by AI coding agents. They apply to ordinary application code as well as agent systems, with a focus on requirements, observable behavior, and verifiable evidence.

These are instructions for a coding agent to follow while reviewing a repository. Reviews produce findings; implementation changes require a separate request.

## Choose a skill

| Skill | Entry point | Use it for |
| --- | --- | --- |
| **Agent Code Review** | [agent-code-review/SKILL.md](agent-code-review/SKILL.md) | Reviewing an AI-authored change against its requirements, integration paths, dependencies, and tests. |
| **Complexity Review** | [SKILL.md](SKILL.md) | Inspecting complex logic and weak coverage using cyclomatic complexity (CC) and CRAP scores. |

**Start with `agent-code-review/SKILL.md` for a general review.** The root `SKILL.md` is the focused complexity companion. The main skill can use that companion when available and otherwise reports qualitative complexity and coverage limits.

## Use it

From a local checkout, give your coding agent the path to the main skill, the code to review, and the original requirements. For example:

```text
Read /path/to/agent-code-review/agent-code-review/SKILL.md and follow it
to review my staged and unstaged changes against HEAD.
The acceptance criteria are in docs/specs/cancellation.md.
Report findings and verification limits without editing the implementation.
```

Replace the paths with your actual checkout and requirements file. You can also supply requirements directly in the prompt and identify a branch or exact pair of revisions instead of local changes.

If your agent runtime has already installed and registered the skill, an example invocation is:

```text
Use $agent-code-review to review these AI-authored changes against
the original requirements and report evidence-backed findings.
```

Keep the `agent-code-review/` directory together when installing it: the entry point links to its `references/` files. Installation and automatic discovery depend on the agent runtime; cloning this repository alone does not register a skill.

## What the review checks

- **Requirement fidelity:** missing behavior, scope expansion, and requested fixes that remain incomplete.
- **Integration:** whether the public entry point reaches the new behavior and produces the required result or persisted effect.
- **API and dependency validity:** whether the actual dependency version supports the imported symbols, signatures, and return shapes.
- **Test integrity:** whether assertions, mocks, skips, and CI settings still reject incorrect behavior. Legitimate requirement changes can justify updating tests.
- **Error and state handling:** incorrect success responses, partial writes, retry effects, and relevant concurrency risks. Specified fallbacks are valid behavior.
- **Repository fit:** existing implementations and shared domain rules, with reuse recommendations grounded in semantics and maintenance cost.

The reviewer derives counterexamples for high-risk behavior and distinguishes author-reported results from its own executions and source inspection. CC and CRAP help locate review targets; scores alone do not establish correctness or justify blocking a change.

See the [inspection criteria](agent-code-review/references/review-checks.md) and [research rationale](agent-code-review/references/sources.md) for details.

## Expected output

A review states its scope and evidence limits, then reports findings ordered by impact. Each defect includes its location, trigger, expected and actual behavior, consequence, evidence, and a focused correction or regression-check direction.

Supported defects, unresolved questions, and maintenance suggestions remain distinct. Zero actionable findings is a valid result. The report ends with checks actually run and material areas left unverified.

## Evaluation

Run the maintained fixture checks from the repository root with Python 3; they use only the standard library:

```sh
python evals/check_cases.py
```

The script executes the maintained Python snippets in [evals/cases.md](evals/cases.md). Its seven checks verify fixture behavior; they do not run or score a coding agent.

The initial review evaluation used eight synthetic scenarios: five defect-bearing changes, two legitimate changes, and one case with insufficient evidence. Fresh reviewers using the original complexity skill and the new main skill both reached the expected judgments. **This evaluation demonstrates no detection improvement over the baseline.** It was one pass per variant, not a statistical benchmark or a test of large-repository navigation.

For a new behavioral evaluation, give a fresh reviewer the chosen skill and raw cases, withholding [evals/results.md](evals/results.md) and `evals/check_cases.py` until grading. Compare misses, false positives, and evidence errors. The [evaluation record](evals/results.md) documents the initial results and remaining limits.

## Repository layout

```text
.
├── README.md
├── SKILL.md                         # Complexity Review companion
├── agent-code-review/
│   ├── SKILL.md                     # Main review skill
│   ├── agents/openai.yaml           # UI metadata and example invocation
│   └── references/
│       ├── review-checks.md
│       └── sources.md
└── evals/
    ├── cases.md                     # Raw review scenarios
    ├── check_cases.py               # Executable fixture checks / answer key
    └── results.md                   # Evaluation results and limitations
```
