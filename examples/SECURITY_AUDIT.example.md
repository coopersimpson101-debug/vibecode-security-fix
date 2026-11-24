# SECURITY_AUDIT.md (example)

> **This is an illustrative sample** for a fictional app called *Notely*, made to show the format the skill produces. It is not a real audit, and the findings are invented.

## Intake and assumptions

- Live at `notely.example.com` with about 1,200 real users. Stripe is in test mode.
- There's no staging environment. Read-only live checks were approved.
- Payments are off-limits this week.

## Stack

Next.js 15 (App Router) on Vercel · Supabase (Postgres, Auth, Storage) · OpenAI for note summaries · Stripe Checkout

## Threat model

Valuable: users' private notes, the OpenAI bill, and accounts. Attackers: the anonymous internet, plus any logged-in user going after other users. Trust boundaries: browser → route handlers, browser → Supabase (anon key), server → OpenAI.

## Endpoint inventory

| Handler | Method | File | Auth | Ownership check | Validation | Rate limit | Notes |
|---|---|---|---|---|---|---|---|
| `/api/notes/[id]` | DELETE | `app/api/notes/[id]/route.ts` | ✅ | ✅ *(fixed)* | ✅ | — | Was IDOR |
| `/api/summarize` | POST | `app/api/summarize/route.ts` | ✅ *(fixed)* | n/a | ✅ *(fixed)* | ✅ *(fixed)* | Was open AI proxy |
| `/api/stripe/webhook` | POST | `app/api/stripe/webhook/route.ts` | signature | n/a | ✅ | — | OK |

## Summary

| Severity | Found | Fixed | Awaiting approval | User action |
|---|---|---|---|---|
| Critical | 2 | 1 | 1 | 1 |
| High | 2 | 2 | 0 | 0 |
| Medium | 3 | 3 | 0 | 0 |
| Low | 2 | 2 | 0 | 0 |

## Findings

### [CRITICAL] Notes table readable by any logged-in user
- **Where:** `supabase/migrations/0003_notes.sql:12`
- **Exploit:** policy `using (true)` lets any user run `select * from notes` with the public anon key from the bundle
- **Confidence:** Confirmed (live read-only test as a throwaway user returned another user's row)
- **Fix:** `supabase/migrations/0011_notes_rls.sql` scopes reads to `auth.uid() = user_id`, with a rollback migration
- **Status:** Awaiting approval (migration). Incident check in section 7 is recommended.

### [CRITICAL] IDOR on note deletion
- **Where:** `app/api/notes/[id]/route.ts:14`
- **Exploit:** any logged-in user can `DELETE /api/notes/<id>` belonging to another user
- **Confidence:** Confirmed (regression test added)
- **Fix:** query scoped to the session user; `tests/notes.authz.test.ts`
- **Status:** Fixed

### [HIGH] `/api/summarize` is an unauthenticated OpenAI proxy
- **Where:** `app/api/summarize/route.ts:5`
- **Exploit:** anyone can send arbitrary prompts and run up the OpenAI bill
- **Confidence:** Confirmed
- **Fix:** `requireUser()`, a zod input cap of 20k chars, `max_tokens: 400`, and an Upstash rate limit of 20/hour per user
- **Status:** Fixed

*…remaining findings omitted from this sample.*

## User must do

1. Rotate the OpenAI key, which was committed in `a1b2c3d` (Dashboard → API keys), following the order in `SECURITY_DEPLOY_PLAN.md`.
2. Set a monthly spend cap in OpenAI billing.
3. Turn on MFA for the GitHub, Vercel and Supabase accounts.

## Skipped categories

G (no file uploads) · N (no self-hosted infra)
