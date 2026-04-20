# CLAUDE.md — dafny

Dafny: verification-aware programming language that compiles to Boogie and then to target
languages (C#, Java, Python, Go, JavaScript). Used by Project-Metic as a high-level
specification and proof frontend for formal verification of compiler transformations.

Upstream: dafny-lang/dafny (open source, MIT license).

## What This Repo Is

The Dafny verifier and compiler — used by Project-Metic to write high-level proof
obligations and specifications that are then lowered to Boogie IR for SMT verification.

## Key Invariants

- **Dafny specifications must type-check and verify before being promoted.** Never commit
  a Dafny spec to the pipeline if `dafny verify` exits non-zero. Specs with unresolved
  verification conditions must be marked `assume` with an explicit TODO linking to the
  open PO.
- **No `assume false` in shipped specs.** `assume false` trivially verifies any property.
  It is permitted during development with a TODO, but must never reach a production build.
- **Lean 4 is the canonical certificate format.** Dafny proofs that feed Metic certificates
  must ultimately be backed by Lean 4 proofs (via Infera). Dafny is a development-time aid,
  not the final trust anchor.

## Dev Commands

```bash
# Install (requires .NET SDK)
dotnet tool install -g dafny

# Verify a Dafny file
dafny verify input.dfy

# Compile to C#
dafny build --target cs input.dfy

# Run tests
dafny test input.dfy
```

## Tech Stack

- Dafny language, .NET, Boogie (backend), Z3 (SMT solver)

## Integration with Project-Metic

- Dafny specs feed into the `project-metic-mono` Dafny/Boogie formal verification backend
- Verification conditions lowered to Boogie, then Z3 for discharge
- Final certificates require Infera co-signing (Lean 4 format)

## What NOT to Do

- Do not use `assume false` in production specs
- Do not commit Dafny specs that fail `dafny verify`
- Do not treat Dafny verification output as a final Metic certificate without Infera co-signing
