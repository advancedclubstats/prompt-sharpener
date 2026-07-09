# prompt-sharpener

A [Claude Agent Skill](https://www.claude.com/skills) that turns a rough, half-formed prompt into a sharper, more effective one — grounded in Anthropic's own published prompt-engineering guidance.

You describe the task you want to prompt a model to do (or paste a draft prompt). The skill asks a couple of targeted clarifying questions, then returns a cleaned-up prompt you can copy and paste, plus a short note on what changed and why.

## What it does

- Detects whether you're writing a **one-off chat/task prompt** or a **reusable system prompt** and adapts accordingly.
- Asks 1–3 clarifying questions first (success criteria, output format, audience) — because the biggest lever on prompt quality is knowing what "good" looks like.
- Applies the well-established fundamentals: be clear and direct, give the reason behind the ask, state scope explicitly, use examples and XML structure where they help, specify output format, and add a self-check for high-stakes tasks.
- Guards against **over-prompting** — the stacked ALL-CAPS "YOU MUST" style that backfires on modern models.
- Returns the sharpened prompt in a copy-paste block plus a brief, technique-labeled rationale.

It's organized in two tiers: a stable **fundamentals** layer (`references/techniques.md`) and a **frontier** layer (`references/frontier.md`) with newer, higher-upside guidance from Anthropic's model teams — clearly labeled as the higher-upside bet, so you always know which is which.

## Install

**Option A — the packaged skill.** Download `prompt-sharpener.skill` and add it via your Claude client's skill upload (Settings → Capabilities → Skills, where available).

**Option B — the folder.** The `prompt-sharpener/` folder is a standard Agent Skill (`SKILL.md` + `references/`). Zip that folder and upload it, or drop it into your Claude Code skills directory.

Once installed, it triggers on its own whenever you're drafting or improving a prompt — or you can invoke it directly.

## Credit and caveats

This skill is a synthesis of **Anthropic's own prompt-engineering documentation** — I organized and packaged it; the underlying guidance is theirs. Primary sources:

- [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- The model-specific prompting pages (Opus 4.8, Sonnet 5, Fable 5)
- [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)

**On staleness:** the reference files reflect guidance as of **July 2026** and carry a "source reviewed" date. Prompt-engineering advice — especially the frontier layer, which names specific current models — changes as new models ship. Treat the dated bits as a snapshot, and check Anthropic's docs for anything time-sensitive.

It's principled and grounded in that guidance; it hasn't been formally benchmarked to prove it produces better outputs. Borrow it, fork it, improve it.

## License

MIT — see [LICENSE](LICENSE).
