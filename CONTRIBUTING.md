# Contributing

Thanks for helping make vibecoded apps safer. The whole skill lives in one file, [`skills/vibecode-security-fix/SKILL.md`](skills/vibecode-security-fix/SKILL.md), so most contributions are edits to it.

## Good contributions

- **A missed hole.** Name a vulnerability pattern common in AI-built apps that the checklist doesn't cover. Include a short example of the vulnerable code and the fix.
- **A false positive.** Point to a search pattern or check that flags safe code, and suggest how to tighten it.
- **A new stack.** Add checks for a framework, database or host the skill handles poorly.
- **A fix that broke something.** Describe a case where following the skill broke a live app, and say what the safe-rollout recipe should have been.

## Guidelines

- Keep it tight. Every line in `SKILL.md` costs context on every run, so prefer one precise sentence over a paragraph.
- Keep it safe. Changes must not make the skill probe systems the user doesn't own, write to production, or skip the ask-first rules.
- Keep it current. For CVEs and version-specific advice, link the advisory.
- Bump `version` in `.claude-plugin/plugin.json` and add a line to `CHANGELOG.md`.

## Process

1. Open an issue first for anything bigger than a small fix.
2. Fork the repo, make the change on a branch, and open a pull request explaining *why*.
3. If you can, say which app or stack you tested the change on.
