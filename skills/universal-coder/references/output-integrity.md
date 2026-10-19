# Output Integrity

Use this on any task with more than one deliverable, any "give me the full file" request, or
any long-running generation that risks hitting a length limit mid-way.

## Baseline rule

A partial deliverable is a broken deliverable. Optimize for completeness over brevity. If the
request is for 5 components, deliver 5 finished components — not 3 finished plus 2 described.

## Banned patterns

Never let these stand in for real content:

- **In code:** `// ...`, `// rest of code`, `/* implement here */`, `// TODO`, `// similar to
  above`, `// continue the pattern`, a bare `...` used as an ellipsis for omitted logic.
- **In prose:** "let me know if you'd like me to continue," "I can add more detail if needed,"
  "for brevity," "the rest follows the same pattern," "and so on" used to skip real content.
- **Structural shortcuts:** showing the first and last section of a sequence and skipping the
  middle; writing one example plus "repeat for the others"; describing what code should do
  instead of writing the code.

## Process

1. **Scope.** Before writing anything, count the distinct deliverables the request implies
   (files, functions, test cases, chart panels, migration steps). Write that number down as the
   target.
2. **Build.** Produce every deliverable to completion in one pass. No skeletons with the
   intention to "fill in later" unless the user explicitly asked for a skeleton.
3. **Cross-check.** Before finishing, re-read the original request and compare the delivered
   count against the scoped count. Anything missing gets added before the response ends.

## Handling genuinely long outputs

When a response is going to exceed a practical length limit:

- Do not compress remaining sections to cram them in — that produces broken, half-finished code.
- Finish the current unit cleanly (end of function / file / section), then stop.
- End with an explicit marker naming exactly what is done and what's left, e.g.:
  `[3 of 7 files complete — next: auth/middleware.ts]`
- On resumption, continue from exactly that point with no re-summary of finished work.

## Final check before claiming "done"

- No banned pattern appears anywhere in the output.
- Every item the user asked for is present, in full, and runnable/usable as-is.
- Code blocks contain real implementations, not prose descriptions of what the code would do.
- Nothing was shortened "to save space."
