# Mutation Testing / Fuzzing

Use for: measuring real test-suite strength (mutation testing) and coverage-guided bug hunting
(fuzzing). Authorized testing and defensive security contexts only.

## When to use

- "Is my test suite actually catching bugs, or just hitting lines?" → mutation testing.
- "Find crashes/memory-safety bugs in this parser/binary" → fuzzing.
- Hardening a codebase before a release or security review.

## Mutation testing

Mutation testing measures test quality, not code coverage. It works by introducing small,
deliberate changes ("mutants") into the code — flipping a comparison operator, off-by-one a
boundary, negate a condition — and checking whether the existing test suite fails. A mutant
that survives (tests still pass) means the suite isn't actually verifying that piece of logic.

Workflow:
1. Run the existing test suite once to get a baseline (it must pass before mutating).
2. Generate mutants using a mutation-testing framework appropriate to the language (rather than
   hand-writing mutants, which doesn't scale).
3. Re-run the suite against each mutant; classify each as *killed* (a test failed — good) or
   *survived* (all tests passed — a gap in coverage).
4. For each survivor, write the specific test case that would have caught it, rather than a
   vague "add more tests" note.
5. Report mutation score (killed / total) alongside, not instead of, line coverage — the two
   numbers tell different stories and a high line-coverage/low-mutation-score codebase has
   tests that execute code without actually asserting on its behavior.

## Fuzzing

Coverage-guided fuzzing feeds a target function or binary a stream of mutated inputs, using
code-coverage feedback to steer input generation toward unexplored paths.

Workflow:
1. Identify a fuzzable entry point: a function that parses untrusted input end-to-end (a file
   parser, a deserializer, a network message handler) — not something with heavy external
   side effects (network calls, disk writes) that will make the fuzzer slow or non-deterministic.
2. Write a harness that feeds raw bytes into that entry point and build it with sanitizers
   enabled (AddressSanitizer for memory errors, UndefinedBehaviorSanitizer for UB) — sanitizers
   turn silent memory corruption into an immediate, debuggable crash.
3. Seed the corpus with a handful of valid, diverse real-world inputs — fuzzers find bugs far
   faster starting from valid structure than from pure randomness.
4. Run, triage crashes by deduplicating on stack trace, and minimize each crashing input to the
   smallest reproducer before filing it.
5. For found bugs, write a regression test from the minimized input before fixing — this is
   what prevents the bug from coming back.

## Scope discipline

Both techniques are for the user's own code or code they are explicitly authorized to test.
Never target third-party services, production systems without authorization, or anything that
implies denial-of-service against infrastructure that isn't the user's own test environment.

## Escalating beyond this file

This is the method, not a fuzzer. Actually running coverage-guided fuzzing needs a real
engine (libFuzzer, AFL++, Atheris, cargo-fuzz — pick by language) and, for language-specific
mutation testing, a real mutation framework. If dedicated skills for those tools are installed
and available, invoke them directly for the actual run; this file is for deciding what to
fuzz/mutate and how to read the results — see `escalation-and-scope.md`.
