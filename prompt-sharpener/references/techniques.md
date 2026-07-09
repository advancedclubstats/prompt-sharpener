# Fundamentals — the stable core (the evidence base)

This is the **fundamentals tier**: the stable, high-confidence techniques that hold across
models and change slowly. For the newer, higher-upside, faster-moving guidance from
Anthropic's model teams and applied engineers, see `frontier.md` — and apply that layer
with a bit more judgment, since it's more likely to shift.

Source: Anthropic's official prompt-engineering guidance
(https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices
and the overview at .../prompt-engineering/overview). Everything below is drawn from
that guidance so your rationale to the user stays grounded in what Anthropic actually
tested, not generic prompting folklore.

**Source reviewed: 2026-07-09.** When you refresh the skill, re-pull the pages above and
update this date.

## Contents
1. Be clear and direct
2. Add context and motivation
3. Use examples (multishot)
4. Structure with XML tags
5. Give the model a role
6. Long-context prompting
7. Control the output format
8. Thinking and self-checking
9. Tool use (for prompts that drive agents)
10. The over-prompting trap
11. What "before prompt engineering" assumes

---

## 1. Be clear and direct

Claude responds well to clear, explicit instructions. Being specific about the desired
output improves results. If you want "above and beyond" behavior, ask for it explicitly
rather than hoping the model infers it.

**Golden rule:** Show your prompt to a colleague with minimal context and ask them to
follow it. If they'd be confused, Claude will be too.

- Be specific about desired output format and constraints.
- When order or completeness of steps matters, give instructions as numbered lists or
  sequential steps.
- Tell the model what *to* do, not only what to avoid.

## 2. Add context and motivation

Explaining *why* an instruction matters helps the model understand the goal and deliver
more targeted responses. The model is smart enough to generalize from the explanation, so
a reason ("keep it short because it's going in a mobile notification") outperforms a bare
rule ("keep it short").

## 3. Use examples (multishot / few-shot)

Examples are one of the most reliable ways to steer output format, tone, and structure.
Make them:
- **Relevant** — mirror the actual use case closely.
- **Diverse** — cover edge cases; vary them so the model doesn't latch onto an unintended
  pattern.
- **Structured** — wrap each in `<example>` tags (multiple inside `<examples>`) so the
  model separates them from instructions.

Aim for 3-5 examples. You can also ask the model to critique your examples for relevance
and diversity, or to generate more from an initial set.

## 4. Structure with XML tags

XML tags help the model parse prompts that mix instructions, context, examples, and
variable input. Wrapping each content type in its own tag (`<instructions>`, `<context>`,
`<input>`, `<examples>`) reduces misinterpretation.

- Use consistent, descriptive tag names.
- Nest when there's a natural hierarchy (e.g. documents inside `<documents>`, each in
  `<document index="n">`).

Note: modern models parse structure well even without XML, so don't force tags onto a
short, simple prompt. Reach for them when the prompt genuinely mixes several kinds of
content.

## 5. Give the model a role

Setting a role — often in the system prompt — focuses behavior and tone. Even one sentence
("You are a helpful coding assistant specializing in Python") makes a measurable
difference. Especially valuable for system prompts that define a reusable assistant.

## 6. Long-context prompting (20k+ tokens)

- **Put long-form data at the top**, above the query, instructions, and examples. Queries
  placed at the end can improve response quality by up to ~30% on complex multi-document
  inputs.
- **Structure documents with XML** — wrap each in `<document>` with `<document_content>`
  and `<source>` subtags.
- **Ground responses in quotes** — ask the model to first quote the relevant parts of the
  source, then answer. This cuts through the noise of a long document.

## 7. Control the output format

- **Tell the model what to do, not what not to do.** Instead of "don't use markdown," say
  "write in smoothly flowing prose paragraphs."
- **Use XML format indicators** — e.g. "write the prose in `<prose>` tags."
- **Match your prompt style to the desired output.** Prompt formatting influences response
  formatting; removing markdown from your prompt can reduce markdown in the output.
- **Be detailed for specific formatting needs** — spell out when lists are and aren't
  allowed, when to use headings, etc.

## 8. Thinking and self-checking

- **Prefer general instructions over prescriptive step-by-step plans.** "Think thoroughly"
  often beats a hand-written procedure; the model's own reasoning frequently exceeds what a
  human would prescribe.
- **Show reasoning in examples** — use `<thinking>` tags inside few-shot examples to
  demonstrate the reasoning pattern.
- **Ask the model to self-check.** Append "Before you finish, verify your answer against
  [criteria]." This reliably catches errors, especially in coding and math.
- When extended thinking is off, some models are sensitive to the word "think"; alternatives
  like "consider," "evaluate," or "reason through" can work better.

## 9. Tool use (prompts that drive agents)

- Be explicit when you want action. "Can you suggest some changes" may yield only
  suggestions; if you want the change made, say so.
- For proactive agents, a `<default_to_action>` block tells the model to implement rather
  than merely suggest. For cautious agents, a `<do_not_act_before_instructions>` block does
  the opposite.
- For parallel efficiency, instruct the model to make independent tool calls in parallel and
  dependent ones sequentially.

## 10. The over-prompting trap (important)

Modern Claude models (Opus 4.5+ and later, Sonnet 5, Fable/Mythos 5) follow instructions
precisely and are *more* responsive to the system prompt than earlier models. Prompts that
were written to fight under-triggering on older models now cause **over-triggering** and
rigid behavior.

- Dial back aggressive language. Replace "CRITICAL: You MUST use this tool when…" with plain
  "Use this tool when…".
- Remove blanket defaults like "If in doubt, use [tool]" — they cause over-triggering now.
- A sharpened prompt is clearer and tighter, not louder or longer. Stacking MUSTs and
  ALL-CAPS is a smell, not a strength.

## 11. What "before prompt engineering" assumes

Anthropic's overview notes that prompt engineering assumes you have (a) clear success
criteria, (b) a way to test against them, and (c) a first-draft prompt. This is exactly why
the sharpener asks about success criteria and desired output up front — without a target for
"good," even a well-structured prompt is aiming at nothing.
