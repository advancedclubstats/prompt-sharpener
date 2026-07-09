# Frontier layer — higher-upside moves endorsed by Anthropic

**What this file is.** The fundamentals in `techniques.md` are the stable, high-confidence
core. This file is the *frontier* tier: newer, sharper guidance that Anthropic's own model
teams and applied engineers publish, which is more powerful but less universal and more
likely to shift as models change. Use these when they fit — and, per the skill's philosophy,
flag to the user when a change draws on this tier so they know it's a higher-upside bet
rather than settled fundamentals.

**Sources reviewed: 2026-07-09**
- Prompting Claude Opus 4.8 — platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-4-8
- Prompting Claude Sonnet 5 — .../prompting-claude-sonnet-5
- Prompting Claude Fable 5 — .../prompting-claude-fable-5
- Effective context engineering for AI agents (Anthropic Engineering, Sep 2025) — anthropic.com/engineering/effective-context-engineering-for-ai-agents

When you refresh the skill, re-pull these and update the date above.

---

## A. Cross-cutting frontier moves (apply broadly)

These generalize across current models and are the most useful upgrades to a normal prompt.

### 1. Give the reason, not only the request

The single highest-leverage move on current models. It's the fundamentals' "add context"
turned up: state the larger goal, who it's for, and what the output enables, *then* the ask.
Fable 5's guidance gives a clean template:

> "I'm working on [larger task] for [who it's for]. They need [what the output enables].
> With that in mind: [request]."

Models connect the task to relevant knowledge instead of guessing your intent. Bake this
shape into sharpened prompts whenever the user's goal is implicit.

### 2. State scope explicitly (models are now literal)

Current models (Opus 4.8, Sonnet 5, Fable 5) follow instructions literally and do **not**
silently generalize one instruction to related cases, especially at lower effort. If an
instruction should apply broadly, say so: "Apply this to every section, not just the first."
The upside of this literalism is precision — so a sharpened prompt should make scope,
inputs, and expected outputs explicit rather than assuming the model will infer them.

### 3. Steer with a brief instruction, not an enumeration

Because instruction-following is strong, a short directive usually beats a long list of
every case. Fable 5's docs are explicit: a single brevity instruction is as effective as
listing each pattern to avoid. This reinforces the fundamentals' anti-over-prompting rule —
prefer one clear sentence over ten defensive ones.

### 4. Prefer positive examples over "don't" instructions

For verbosity, tone, and format, a short *positive* example of the target ("communicate at
this level of concision: …") steers better than negative instructions ("don't over-explain").
Current models auto-calibrate verbosity to task complexity, so if you need a specific length
or style, show it rather than forbidding the opposite.

### 5. Don't ask the model to echo its private reasoning

On the newest models (notably Fable 5 / Mythos 5), instructions that tell the model to
transcribe, echo, or "show your thinking" as response text can trigger a
`reasoning_extraction` refusal. If a user's draft prompt says "explain your reasoning step
by step in the output," prefer "briefly justify your final answer" or rely on the API's
structured thinking blocks. Note this only matters for those frontier models; classic
chain-of-thought is still fine elsewhere (see fundamentals §8).

---

## B. System-prompt & agent frontier moves (context engineering)

For system prompts and anything that runs in a loop, the frame shifts from "write the perfect
prompt" to **context engineering**: curating the whole set of tokens the model sees. Guidance
from Anthropic's Applied AI team:

### 6. Aim for the "right altitude"

The best system prompts sit between two failure modes: brittle hardcoded if/else logic that's
fragile and hard to maintain, and vague high-level guidance that gives no concrete signal.
Aim for specific enough to guide behavior, flexible enough to leave the model good heuristics.
When sharpening a system prompt, push an over-specified one up and an under-specified one down
toward this middle.

### 7. Minimal high-signal, not maximal

Treat context as a finite "attention budget" — more tokens dilute focus ("context rot").
The goal is the *smallest set of high-signal tokens* that reliably produces the outcome.
"Minimal" doesn't mean short; it means every line earns its place. Start minimal, then add
instructions and examples to fix observed failure modes, rather than front-loading every
edge case.

### 8. Organize into labeled sections

For multi-part system prompts, use clear sections — `<background_information>`,
`<instructions>`, `## Tool guidance`, `## Output description` — via XML tags or Markdown
headers. Exact formatting matters less as models improve, but delineation still helps.

### 9. Curate canonical examples, don't stuff edge cases

Few-shot examples remain strongly advised, but the failure mode is dumping a laundry list of
every rule. Curate a few diverse, canonical examples that portray the expected behavior — for
a model, examples are "pictures worth a thousand words." (This refines fundamentals §3.)

### 10. Long-horizon tasks: compaction, notes, subagents

When a task exceeds a single context window, three techniques preserve coherence:
*compaction* (summarize and reinitialize), *structured note-taking* (persist a NOTES.md /
to-do list the agent re-reads), and *sub-agent architectures* (focused agents with clean
context that each return a short distilled summary). Relevant when sharpening prompts for
agents doing hours-long work.

---

## C. Model-behavior notes worth surfacing to the user

These aren't prompt edits so much as facts about current models that change what the user
should do. Surface them when relevant — they're often the *real* fix.

- **Effort is frequently the right lever, not wording.** On Opus 4.8 and Sonnet 5, if reasoning
  is too shallow, raising the `effort` setting (to `high`/`xhigh`) beats prompting around it;
  if it's overthinking or too slow, lower it. Fable 5 treats effort as the primary
  intelligence/latency/cost control. If a user is tuning a system prompt via the API and
  fighting depth or verbosity, point them at effort before piling on prompt text.
- **Thinking defaults differ by model.** Sonnet 5 has adaptive thinking on by default; Opus 4.8
  has it off unless enabled. Triggering is steerable by prompt. Relevant if a user's prompt
  assumes a thinking behavior they aren't getting.
- **Voice shifts between models.** Each new model's prose style differs; a style/voice prompt
  tuned for an older model should be re-checked against the new baseline rather than trusted.
- **Design briefs need a concrete alternative, not a negation.** Telling a model "don't use
  purple gradients / don't use cream" tends to swap one fixed default for another. Instead,
  specify the concrete direction you want, or ask the model to propose 3-4 directions and pick
  one. (Setting `temperature` for variety is rejected on Sonnet 5, so the propose-options
  approach is the supported way to get variety.)
- **Coverage vs. filtering for review/extraction.** Because current models follow "only report
  high-severity" literally, they may suppress real findings. For code review or extraction,
  ask for full coverage plus a confidence/severity tag and filter downstream, rather than
  asking the model to self-filter with vague words like "important."
- **Remove old scaffolding.** Forced-status-update tricks ("summarize after every 3 tool
  calls") and heavy prescriptive skill instructions written for older models can now *degrade*
  output. When sharpening a prompt migrated from an older model, look for scaffolding to delete.
