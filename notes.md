## Steps to reproduce
1. Install dependencies with `corepack enable && corepack prepare pnpm@10.12.1 --activate && pnpm install --frozen-lockfile`.
2. Run `pnpm vitest run packages/zod/src/v4/classic/tests/error.test.ts -t "no abort early on refinements"` to confirm baseline behavior keeps collecting multiple issues.
3. Validate the new option with `pnpm vitest run packages/zod/src/v4/classic/tests/error.test.ts -t "abortEarly"`.
4. Optionally run `pnpm test` for a full check.

## Observed
Without `abortEarly`, parsing continues and returns multiple issues for the same payload, including follow-up refinement failures. With the new per-parse option enabled, the parser now returns a single issue (`error.issues.length === 1`) and later refinements are not executed in the targeted regression test, which demonstrates first-error behavior for parse/safeParse and related methods.

## Expected
Users validating expensive HTTP payloads should be able to opt into first-error behavior so Zod stops after the first issue and surfaces one `ZodIssue`. The desired behavior is opt-in (not default), so existing multi-issue error collection remains unchanged unless `{ abortEarly: true }` is explicitly passed to parse APIs.
