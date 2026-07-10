---
name: prompt-sharpener
description: Turn a rough, half-formed prompt into a sharpened, high-performing one using Anthropic's own prompt-engineering evidence. Use this whenever the user hands over a draft prompt, pastes a prompt and asks for feedback, describes a task they want to prompt Claude (or another model) to do, or says things like "help me write a prompt", "improve/fix/tighten this prompt", "make this prompt better", "sharpen this", "turn this into a system prompt", or "why isn't this prompt working". Also use when the user is building a system prompt for an app, agent, custom assistant, or GPT. Handles both one-off chat/task prompts and reusable system prompts. Reach for this even when the user doesn't say the word "prompt" but is clearly drafting instructions for a model to follow.
metadata:
  version: 1.0.1
---

# Prompt Sharpener

A good prompt is the difference between a mediocre answer and a great one, but most people write prompts the way they'd jot a note to themselves: compressed, vague, missing the context the model actually needs. Your job is to take that rough material and turn it into a prompt that is clear, specific, and structured according to what Anthropic's own testing shows actually works — without over-engineering it.

**The philosophy: strong fundamentals, willing to reach for the frontier.** Ground every sharpened prompt in the settled fundamentals, but be willing to apply higher-upside, newer techniques that Anthropic's own model teams and engineers endorse — the ideas from people who spend all day thinking about this. Not reactive to every hot take, but not stuck on the basics either. When a change draws on that frontier layer rather than settled fundamentals, say so in your rationale, so the user knows which bets are rock-solid and which are higher-upside.

The evidence base lives in two files, and you should read the relevant one whenever you need detail or the reasoning behind a move:
- `references/techniques.md` — the **fundamentals**: stable, high-confidence techniques that hold across models.
- `references/frontier.md` — the **frontier layer**: sharper, newer, model-specific guidance (effort calibration, literal instruction following, context engineering, "give the reason not just the request", and model-behavior facts worth surfacing). Higher upside, applied with judgment.

Both files carry a "source reviewed" date. Prompt-engineering guidance changes as models change, so treat these as living references — if the dates look stale, it's worth re-pulling the sources before relying on the finer points.

## What you produce: a prompt, not an answer

Hold onto this throughout: your output is a *sharpened prompt*, not an answer to whatever the prompt is about. It matters most when the raw material carries its own request — "do some research and think this through," "analyze this idea." Encode that request into the prompt; don't carry it out. Don't run searches, perform the analysis, or draft the deliverable yourself unless the user explicitly asks you to both sharpen *and* run it. The surrounding environment leans toward taking action and starting research, so treat this as the deliberate exception: sharpen first, act only when asked.

If it's genuinely unclear whether the user wants the prompt or the answer, ask — and when you can't, default to the prompt. A "just sharpen it, don't ask" instruction (see stage 2) is itself that signal: they want the prompt, so sharpen it without executing the request inside it.

## The core loop

Work through four stages: understand the raw material, ask before you sharpen, apply the techniques, then deliver the prompt with a short rationale. Don't skip the asking stage — it's where most of the quality comes from.

### 1. Understand the raw material

Read what the user gave you and figure out three things:

**What are they trying to accomplish?** The underlying goal, not just the literal words. A rough prompt like "summarize this" hides a lot: summarize for whom, how long, focused on what.

**Is this a chat/task prompt or a system prompt?** This changes how you sharpen. A *chat/task prompt* is a one-off instruction to get a specific job done — it centers on the task, the input, and the desired output. A *system prompt* is reusable and defines a role, persistent behaviors, and boundaries for an app, agent, or assistant that will run many times against many inputs. If it's ambiguous, that's a good thing to confirm in stage 2.

**What's already good?** Preserve the user's intent, voice, and any specifics they bothered to include. You're sharpening their prompt, not replacing it with a generic template.

### 2. Ask before you sharpen

The single biggest lever on prompt quality is knowing what "good output" looks like — and that's usually the thing missing from a rough draft. Before writing, ask 1-3 targeted questions to fill the gaps that would most change the result. Common gaps worth asking about:

- **Success criteria** — how will they judge whether the output is good?
- **Output format and length** — prose, table, JSON, bullet list; short or thorough?
- **Audience** — who reads this, and what do they already know?
- **Hard constraints** — tone, things to avoid, length limits, must-include elements.
- **Examples** — do they have an example of a good (or bad) output to steer toward?
- **Model / surface** (system prompts especially) — where will this run, and against what kind of input?

Ask only about what you genuinely can't infer — peppering the user with obvious questions is its own failure. Use the AskUserQuestion tool if it's available (it's faster for the user than free-text), otherwise ask in prose. Briefly say why you're asking: the golden rule is that a colleague with no context should be able to follow the prompt, and these are the gaps that would confuse them.

If the user has explicitly said "just sharpen it, don't ask," respect that and move to stage 3, making reasonable assumptions and listing them in your rationale.

### 3. Apply the techniques

Now rebuild the prompt using the moves that Anthropic's testing supports. You won't use all of them every time — pick the ones that fit the task. See `references/techniques.md` for the full detail and the "why" behind each.

The high-value moves, in rough priority order:

1. **Be clear and direct.** Replace vague asks with specific instructions about the desired output. If the order of steps matters, lay them out as numbered steps. State what you *do* want rather than only what to avoid.
2. **Give the reason, not only the request.** Explain *why* the task matters, who it's for, and what the output enables — then make the ask. This is the highest-leverage move on current models: they connect the task to relevant knowledge instead of guessing intent. A useful shape: "I'm working on [larger goal] for [audience]; they need [what it enables]. With that in mind: [request]." (Fundamentals call this "add context"; the frontier layer shows how far it goes.)
3. **State scope explicitly.** Current models follow instructions literally and won't silently generalize one instruction to related cases. If something should apply broadly, say so ("apply to every section, not just the first"). Make inputs and expected outputs explicit rather than assuming inference.
4. **Assign a role (system prompts especially).** A single sentence establishing who the model is acting as focuses tone and behavior.
5. **Provide or invite examples.** Concrete examples of good output are one of the most reliable steering tools. Curate a few diverse, canonical examples (ideally 3-5) rather than stuffing in every edge case, or leave a clearly marked slot for the user to add one. Wrap them in `<example>` tags. For steering tone or verbosity, a short *positive* example ("write at this level of concision: …") beats a "don't be verbose" instruction.
6. **Structure with XML tags when the prompt mixes content types.** When instructions, context, input data, and examples all live in one prompt, wrap each in descriptive tags (`<instructions>`, `<context>`, `<input>`, `<examples>`) so the model doesn't confuse them. Don't add tags to a simple one-line prompt that doesn't need them.
7. **Specify output format precisely.** Name the format and, where useful, show a skeleton of it. Match the style of your prompt to the style you want out (e.g., write the prompt in flowing prose if you want prose back).
8. **For long inputs, put the data first.** If the prompt includes a long document, place it near the top, above the instructions, and consider asking the model to ground its answer in quotes from the source.
9. **Add a self-check for high-stakes tasks.** A closing line like "Before finishing, verify X against Y" catches errors reliably in reasoning, coding, and math.

**A critical caution — don't over-prompt.** Modern Claude models follow instructions precisely and are sensitive to forceful language. Stacking ALL-CAPS "CRITICAL: YOU MUST" directives makes them over-trigger and behave rigidly. Prefer normal, explanatory phrasing ("Use this format because…") over shouting. A short, clear instruction usually beats a long enumeration of every case. A sharpened prompt is tighter and clearer, not louder or longer. If your rewrite is three times the length of the original and bristling with MUSTs, you've overcooked it.

**Reach for the frontier when it fits.** Beyond the moves above, consult `references/frontier.md` for higher-upside techniques and, importantly, *model-behavior facts that are often the real fix* — for example: if the user is tuning a system prompt via the API and fighting shallow reasoning or verbosity, the `effort` setting is frequently the right lever rather than more prompt text; design briefs need a concrete alternative direction rather than a "don't use X" negation; and review/extraction prompts do better asking for full coverage plus a confidence tag than asking the model to self-filter. Surface these when they apply, and label them as the frontier bets they are.

### 4. Deliver

What you're handing back is the sharpened prompt, not the answer to it. If the raw material contained a request to research or analyze, that request now lives *inside* the prompt for the user to run in a fresh session — you don't execute it here unless they explicitly asked you to sharpen and run.

Return two things:

**The sharpened prompt**, in a fenced code block so it's easy to copy. Make it ready to paste — no placeholders except where the user genuinely needs to drop in their own content (label those clearly, e.g. `[paste your document here]`).

**A brief rationale** — a few short bullets naming the key changes you made and which technique each reflects, so the user learns the pattern and can trust the result. Keep it to the changes that matter; don't annotate every word. For example: "Added a role line to fix tone (role prompting); split the task into 3 ordered steps (clarity); wrapped the sample data in `<input>` tags to separate it from instructions (XML structure)."

Keep the rationale proportional to the prompt. A two-line prompt doesn't need a five-paragraph explanation.

## Adapting to prompt type

**Chat/task prompts** lean on: clear task statement, relevant context, concrete output format, and an example or two. They're often short — resist bloating them.

**System prompts** additionally want: a clear role, persistent behavioral rules, scope boundaries (what the assistant should and shouldn't do), consistent tone guidance, and structured configuration (labeled sections like `<background_information>`, `<instructions>`, `## Tool guidance`, `## Output description`). Because they run repeatedly against varied input, they benefit from explicit handling of edge cases and ambiguity — e.g. what to do when the user's request is unclear.

Aim for the **"right altitude"** (a context-engineering idea from Anthropic's applied team): sit between brittle, hardcoded if/else logic that's fragile and hard to maintain, and vague high-level guidance that gives no concrete signal. Specific enough to guide behavior, flexible enough to leave the model good heuristics. Aim for the *minimal set of high-signal instructions* — not necessarily short, but with every line earning its place, because a bloated prompt dilutes the model's attention. A good practice to pass along: start minimal, then add instructions and examples to fix the specific failures you actually observe, rather than front-loading every hypothetical edge case. See `references/frontier.md` for the fuller treatment, including long-horizon agent techniques (compaction, note-taking, subagents).

## Example

**Input (user's rough prompt):** "write something to get claude to review my python code"

**After asking** what kind of review matters most (they say: catching bugs and security issues, for a small Flask app, output as a prioritized list), a sharpened version:

```
You are an experienced Python code reviewer with a focus on correctness and security.

Review the Flask application code in <code> tags below. Focus on, in priority order:
1. Bugs that would cause incorrect behavior or crashes
2. Security vulnerabilities (injection, auth, unsafe input handling)
3. Error handling gaps

For each issue, give: the location, why it's a problem, and a concrete fix.
Present findings as a list ordered from most to least severe. If you find no issues
in a category, say so briefly rather than inventing minor nitpicks.

<code>
[paste your Flask code here]
</code>
```

Rationale: added a reviewer role to set focus (role prompting); replaced "review my code" with three prioritized, specific goals (be clear and direct); specified the per-issue output shape and overall ordering (output format); wrapped the code in `<code>` tags to separate it from instructions (XML structure); added the "don't invent nitpicks" line to prevent low-value output (context/motivation).
