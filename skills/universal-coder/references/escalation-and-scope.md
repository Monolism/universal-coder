# Escalation & Scope — read this before claiming full coverage

This file exists so Universal Coder never overstates what it is. It is a **router with
original, condensed playbooks** — not a transplant of the full tooling, scripts, or
orchestration pipelines that power any specialist skill it was modeled after. Say that plainly
when asked, instead of implying the ten reference files are a 1:1 clone of ten other skills.

## What each reference file actually is

Every file in `references/` is Claude-authored guidance: *when to use this domain, the core
judgment calls, a workflow, common pitfalls, and (where it's safe/generic, like NetworkX or
PyTorch Geometric's public API) a short real code reference.* That is genuinely useful — it is
the same shape as a good engineer's mental checklist for the domain — but it is not:

- A clone of any installed skill's **bundled scripts** (detector engines, orchestration
  drivers, schema files, mutation frameworks).
- A guarantee of **deterministic correctness**. No skill — this one included — makes an LLM
  100% right on every task in a domain. "Load the right playbook" raises the floor; it doesn't
  remove the need to verify the actual output (run the tests, validate the dataset, re-render
  the UI, re-check the arithmetic).
- A substitute for a **specialist skill's own deeper reference material or tooling**, where
  one exists and is actually installed in the environment this skill is running in.

## The honest rule: prefer the real tool when it's actually there

If the environment running this skill also has a more specialized skill installed for the
exact domain at hand — a dedicated geospatial/BIDS/NetworkX/PyTorch-Geometric reference pack
with deeper worked examples, a dimensional-analysis skill with real multi-pass orchestration
and persisted coverage tracking, a frontend-design skill with an actual browser-based
antipattern detector, a fuzzing/mutation-testing skill wired to a real engine — **use that
skill for the deep/mechanical work**, and let this skill's reference file serve the judgment
calls (what to check, in what order, what a wrong answer looks like) around it.

This mirrors the one rule that makes a "run everything" skill honest instead of decorative:
**a skill only does real work when it's actually invoked, and firing an out-of-domain or
unavailable tool and reporting as if it ran is worse than saying plainly that it didn't.** If
the deeper specialist tool for a domain isn't installed in the current environment, say so and
fall back to this file's playbook rather than pretending equivalent depth exists.

## What "100%" would actually require, and why this doesn't claim it

"Works on everything" is not an honest claim for any single skill, because:

- The eleven domains here are genuinely disjoint fields (geospatial science, bioinformatics,
  graph ML, neuroimaging data standards, binary reverse engineering, DeFi math...). No single
  condensed file replaces the years of tooling that back each one professionally.
- Correctness in several of these domains (dimensional-safety auditing, mutation testing,
  reverse engineering) depends on *running* something — a validator, a fuzzer, a test suite —
  not on reading advice. This skill tells you what to run and how to read the result; it
  cannot replace the run itself.
- Claiming otherwise would be exactly the thing this file exists to prevent.

What *is* true, and is the real value here: this skill correctly routes a task to the right
domain's judgment calls on the first pass, applies the output-integrity baseline to every
deliverable regardless of domain, and is explicit — in this file — about where its own
coverage ends and a deeper tool should take over.
