# Reverse Engineering

Use for: understanding unknown binaries, protocols, or applications through static and dynamic
analysis. Authorized security research, CTF, and defensive/interoperability contexts only.

## When to use

- Analyzing a binary, mobile app, or network protocol with no (or incomplete) source/docs.
- Recovering the logic of an obfuscated or minified client (JS, native, or mobile) for
  interoperability, security research, or CTF purposes.

## Core approach: static first, dynamic to confirm

1. **Static analysis** — identify the format/platform, then survey structure before diving
   into details: file type/architecture, imports/exports, strings, obvious crypto or network
   calls. This builds a map before investing time in any one function.
2. **Dynamic analysis** — run the target under observation (debugger, instrumentation,
   network capture) to confirm hypotheses formed statically and to see runtime-only behavior
   (decrypted strings, dynamically loaded code, actual network payloads) that static analysis
   alone can't reveal.
3. **Iterate between the two.** Static analysis tells you where to put a breakpoint; dynamic
   analysis tells you what the static analysis actually meant at runtime.

## By target type

- **Native binaries**: identify architecture and compiler/toolchain fingerprints first; locate
  the entry point and work outward; use a decompiler to get pseudocode and treat it as a
  starting hypothesis, not ground truth — decompiler output is frequently wrong around
  optimized or hand-written assembly.
- **JavaScript applications**: deobfuscate/beautify first; identify framework/bundler
  fingerprints (webpack chunk patterns, common minifier signatures) to know what "normal"
  structure to expect; set breakpoints on sinks (network calls, `eval`, `JSON.parse`) rather
  than reading minified code top-to-bottom.
- **Android applications**: decompile to an intermediate readable form; check the manifest for
  declared components/permissions before diving into code — it's the fastest map of what the
  app can actually do; watch for native libraries doing the real logic behind a thin
  Java/Kotlin shell.
- **Network protocols**: capture real traffic first, diff requests that differ by exactly one
  input to isolate what each field controls, then build a minimal client that reproduces one
  request before generalizing.

## Evidence discipline

- Keep a running log of findings tied to concrete evidence (file offset, function address,
  packet capture reference) — reverse engineering findings that can't be re-derived from a
  specific artifact are not trustworthy months later.
- When a hypothesis is formed statically, state it as a hypothesis and confirm it dynamically
  before relying on it for the next step.

## Scope and ethics

This is for code/binaries/protocols the user owns, is explicitly authorized to analyze
(pentest engagement, CTF, bug bounty, interoperability under applicable law), or that are
clearly intended for public security research. Decline to help bypass DRM for infringement,
analyze malware for offensive reuse, or reverse-engineer a target when the request implies
unauthorized access to a third party's system.

## Escalating beyond this file

This is the method (static-then-dynamic, by target type), not a disassembler. Actually
decompiling a binary, instrumenting a running process, or walking DWARF debug info needs real
tooling. If dedicated skills for those (a decompiler workflow, `dwarf-expert`, a
connection-and-recovery/runtime-observation toolkit) are installed and available, use them for
the actual mechanics — see `escalation-and-scope.md`.
