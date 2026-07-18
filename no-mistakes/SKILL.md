---
name: no-mistakes
description: Pre-push gate for AI-generated code — runs typecheck, lint, and build, then reviews the diff against your stated intent for security, logic, and best-practice issues. Lightweight, no worktrees or forced PR flow. Use when the user explicitly asks to review a diff before pushing or committing (e.g. "review this before I push", "pre-push check", "review my diff", "run no-mistakes", "/no-mistakes") — especially for security-sensitive code like auth, payments, or permissions.
---

# No-Mistakes

A lightweight, model-agnostic pre-push review gate. Just a repeatable checklist any coding agent can run
before code ships. 

## When to use

Only when explicitly asked — "review this before I push", "no-mistakes check",
"review my diff", or equivalent. This skill does not fire just because a
feature was finished; say the word.

Treat as mandatory once invoked for anything security- or money-sensitive:
auth, payments, permissions, data mutations, anything touching user PII.

## Step 1 — Capture intent

Before reviewing anything, write 1–3 sentences describing what you set out to
do — the goal, not a summary of the diff. This is the single most important
step: it's what lets the review step tell a deliberate decision apart from a
mistake.

Good intent statement:
> "Add authentication using better-auth: magic link email sign-in and Google
> OAuth, with session handling for protected dashboard routes. Email
> verification is intentionally skipped for OAuth sign-ups since Google
> already verifies the email."

Bad intent statement (too thin — just restates the diff):
> "Added auth."

If you're an agent running this skill and the user hasn't given you an
intent, ask for one sentence before continuing. Don't guess.

## Step 2 — Mechanical checks

Run these in order. Stop and fix before continuing if any fail.

```bash
pnpm tsc --noEmit
pnpm lint
pnpm run build
```

## Step 3 — Tests

```bash
pnpm test
```

If the project has no test command, don't skip this silently — say so
explicitly in the final report. For security-sensitive diffs (auth,
payments), recommend writing at least a few focused tests before shipping:
the core happy path, one expiry/invalid-token case, and one duplicate/
conflict case (e.g. same email via two sign-in methods).

## Step 4 — Intent-aware review

Get the diff against the base branch:

```bash
git diff main...HEAD
```

Review it against the Step 1 intent. Don't just describe the diff back —
actively look for the following. Skip categories that clearly don't apply to
this diff (e.g. skip "auth/session" checks for a pure CSS change).

**Security — always check when the diff touches auth, payments, or permissions:**
- Token/session handling: expiry set, single-use where it should be,
  cookies `httpOnly` + `secure`
- Redirect/callback URLs validated against an allowlist (open-redirect risk)
- No secrets leaked to the client bundle (e.g. a stray `NEXT_PUBLIC_` prefix
  on something that should stay server-only)
- CSRF protection on state-changing routes
- Account-linking edge cases: what happens if the same email signs in via
  two different methods — merge, reject, or silently duplicate?

**Logic & correctness:**
- Race conditions: double-submit, concurrent requests, duplicate side effects
- Failure mode: does an error path fail closed (deny/reject) or fail open
  (silently allow)?
- Does the diff match the stated intent — anything skipped that was implied,
  or added that wasn't asked for?

**Best practice:**
- Consistent with existing patterns already in the codebase (hooks, query
  key factories, error boundaries, etc. — check neighboring files if unsure)
- No leftover debug statements, commented-out code, or dead branches

Done when every non-skipped category above has an explicit finding or an
explicit "clear" judgment — not a general impression of the diff.

## Step 5 — Triage findings

Sort everything found into three buckets and report them that way — don't
just dump a wall of text:

- **Fix now** — clear bugs or security gaps; fix and re-run steps 2–3
- **Note & ship** — a deliberate tradeoff, logged so it's not forgotten
  (e.g. "no rate-limiting on magic-link requests yet — acceptable for beta,
  revisit before public launch")
- **Ask the human** — anything genuinely ambiguous; don't decide it yourself
