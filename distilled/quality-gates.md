---
type: topic
updated: 2026-11-07
---

# Quality gates for agent-written code

Goal: a list of metrics to drop into any project, so generated code improves without per-project
thought.

## Problem statement

- Single-shot benchmarks miss maintainability. Real work is build, change requirements, build again.
  Without the end state, an agent stacks cruft, and the odds of a correct change that breaks nothing
  fall at each step.
- Models refactor well when asked, but do not refactor, re-centralise abstractions or push back
  unprompted. RL trains task completion, not maintainability.

> [!important] Key insight
>
> Agent time and token cost matter far less than maintainability, which compounds. Paying twice the
> tokens for cleaner code is worth it.

## Metrics

Per function. Lower is better.

From BAML's `baml codequality`
([post](https://boundaryml.com/blog/teaching-agents-to-write-maintainable-code)):

- **Cognitive complexity** (SonarSource): +1 per break in linear flow (`if`, `else`, loop, `catch`),
  plus a penalty for each level of nesting. Tracks how hard code is to read.
- **Cyclomatic complexity** (McCabe): independent paths through a function, decision points + 1.
  Tracks how many tests full branch coverage needs.
- **NPath** (Nejmeh): execution paths. Branches in sequence multiply, so it grows fastest on long
  functions.
- **Nesting depth:** deepest stack of `if`s, loops and lambdas.
- **Exits:** count of `return` and `throw`, plus normal completion.

BAML reports these against the base branch in a PR comment, with no fail thresholds. Its evals
scored the worst function per run, not the average: agents can lower averages by splitting functions
without improving the code.

## Designing gates

- **Measure leftovers.** Temporary markdown, dead scripts, scratch and verifier code count as slop,
  not noise: unattended merges keep them. They pollute later agents' context, go stale, and teach
  agents that dropping them is the local pattern. Some team bans checking in markdown files
  outright.
- **Start from existing style guides.** Google's C++ (Abseil) and Java guides were the models named.
  Rules were written as good and bad examples: no magic numbers, `match` over if/else (exhaustive
  under the type system), filter/map over loops building lists, truthiness over `!= null`.
- **Using an LLM command as a linter.** A rule that is hard to express as a lint rule can be a skill
  the agent runs.

## Open question: guide, feedback or hard gate

The planned approach was a hard commit gate per metric. The talk's main result came from a style
guide given to agents: metrics improved with no block on committing. A possible combination is a
style guide up front plus a metric report the agent reads. Undecided.

- For guidance: in the talk's runs, guidelines improved every complexity metric except
  worst-function complexity.
- Against guidance alone: those runs were one-shot builds. The
  [SlopCodeBench paper](https://arxiv.org/abs/2603.24755) tests agents building on their own code,
  and finds quality prompts lower initial verbosity and erosion but "cannot stop the eroding
  behavior". Correctness also fell by 2.3 points on average and cost per checkpoint rose 12.1%.
- For feedback: the talk's team also builds the metrics into their compiler, so the agent is told
  when a change made the code worse. Whether that was measured apart from the guidelines is unclear
  from the captions.

## Slop detectors

Scores and the full rule list are in [[slop-code-bench]]. The rules are public in
[scb-check](https://github.com/gabeorlanski/scb-check) (Apache-2.0), the checker behind
SlopCodeBench:

- [AST-grep rules](https://github.com/gabeorlanski/scb-check/tree/main/src/scb_check/resources/slop_rules):
  197 at last count, in 8 files. The paper cites 137 and the talk about 212 including standard
  linters, so the set has moved.
- [Structural rules](https://github.com/gabeorlanski/scb-check/tree/main/src/scb_check/rules):
  `trivial-wrapper`, and the opt-in `low-use-short-function`.
- These rules are Python only. Other languages get clone detection and complexity metrics.

## References

- [Why Opus Medium Beats X-High at Writing Maintainable Code](https://www.youtube.com/watch?v=VLonoD9m4VY)
