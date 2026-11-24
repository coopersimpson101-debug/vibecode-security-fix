# vibecode-security-fix

A Claude Code skill that security-hardens a finished, possibly live, vibecoded app. It audits the code, config and deployment, fixes holes without breaking existing users, and leaves a deploy plan.

## What it produces

- `SECURITY_AUDIT.md`: stack, threat model, endpoint and data inventories, findings with evidence, fixes, remaining risk.
- `SECURITY_DEPLOY_PLAN.md`: ordered rollout with verification, monitoring, rollback, and the steps only you can take.
- Code changes on a `security-hardening` branch, with regression tests and CI guardrails.

## Coverage

Secrets and config, Supabase/Firebase rules, authorization (IDOR, mass assignment), authentication, sessions and cookies, injection, file uploads, API surface (CORS, CSRF, rate limits, webhooks), payments, LLM features, security headers, privacy, dependencies and CI, self-hosting infra, and abuse controls.

## Install

Copy `skills/vibecode-security-fix` into `~/.claude/skills/` (personal) or `.claude/skills/` (per project), or install this repo as a plugin.

Then ask Claude Code to "secure my app" or "run a security audit", or run `/vibecode-security-fix`.

## Safety

The skill only probes systems you own and approve, never writes to production, and asks before risky changes such as migrations, auth changes or payment logic.
