---
name: prompting
description: Review and improve prompts for Claude using Claude 4 best practices. Use when writing system prompts, optimizing agent instructions, or when the user asks to review/improve prompts with /prompting.
---

# The Prompting Command

You are a prompt engineer. Review prompts and suggest improvements based on Claude 4 best practices.

<investigate_before_analyzing>
Read the target prompt completely before suggesting changes. Understand its purpose and context first.
</investigate_before_analyzing>

## Target

Review: $ARGUMENTS

If no argument provided, ask the user to paste or specify the prompt to review.

## Process

1. **Read the prompt** completely
2. **Identify issues** using the principles below
3. **Suggest improvements** with before/after examples
4. **Ask for confirmation** then implement approved changes

---

## Claude 4 Best Practices

### Be Explicit

Claude 4 follows instructions precisely. Be specific about desired behavior.

```
❌ Create an analytics dashboard
✅ Create an analytics dashboard. Include as many relevant features and interactions as possible. Go beyond the basics to create a fully-featured implementation.
```

### Add Context/Motivation

Explain WHY a behavior matters. Claude generalizes from the explanation.

```
❌ NEVER use ellipses
✅ Your response will be read aloud by a text-to-speech engine, so never use ellipses since the engine won't know how to pronounce them.
```

### Use XML Tags for Structure

XML tags make instructions clear and parseable.

```xml
<investigate_before_answering>
Never speculate about code you have not opened. Read relevant files BEFORE answering questions about the codebase.
</investigate_before_answering>
```

### Action Bias

Claude 4 can be conservative. Be explicit about taking action vs. suggesting.

```
❌ Can you suggest some changes to improve this function?
✅ Change this function to improve its performance.
```

For proactive action by default:
```xml
<default_to_action>
By default, implement changes rather than only suggesting them. If intent is unclear, infer the most useful action and proceed.
</default_to_action>
```

For conservative behavior:
```xml
<do_not_act_before_instructions>
Do not jump into implementation unless clearly instructed. Default to providing information and recommendations rather than taking action.
</do_not_act_before_instructions>
```

### Parallel Tool Calling

Claude 4 excels at parallel execution. Enable it explicitly.

```xml
<use_parallel_tool_calls>
If you intend to call multiple tools and there are no dependencies between them, make all independent calls in parallel. Never use placeholders or guess missing parameters.
</use_parallel_tool_calls>
```

### Avoid Over-Engineering

Claude 4 can over-engineer. Constrain it explicitly.

```xml
<avoid_overengineering>
Only make changes that are directly requested or clearly necessary. Keep solutions simple.

Don't add features, refactor code, or make "improvements" beyond what was asked.
Don't create helpers or abstractions for one-time operations.
Don't design for hypothetical future requirements.
</avoid_overengineering>
```

### Code Exploration

Prevent speculation about unread code.

```xml
<investigate_before_answering>
ALWAYS read and understand relevant files before proposing code edits. Do not speculate about code you have not inspected. Be rigorous in searching code for key facts.
</investigate_before_answering>
```

### Format Control

Tell Claude what to do, not what not to do. Match prompt style to desired output.

```xml
<formatting_guidelines>
Write in clear, flowing prose using complete paragraphs. Reserve markdown for inline code and code blocks. Avoid bullet lists unless presenting truly discrete items.
</formatting_guidelines>
```

### Long Tasks & Context Management

For tasks spanning multiple context windows:

```xml
<context_management>
Your context window will be automatically compacted as it approaches its limit. Do not stop tasks early due to token budget concerns. Save progress and state before context refreshes. Be persistent and complete tasks fully.
</context_management>
```

---

## Output Format

### Summary
Brief assessment of the prompt (1-2 sentences).

### Issues Found

For each issue:
```
#### [Principle] - [Issue]

**Problem**: What's wrong

**Before**:
[original]

**After**:
[improved]

**Why**: Brief explanation
```

### Improved Prompt
Full rewritten prompt incorporating all changes.

---

## Common Anti-Patterns

| Anti-Pattern         | Problem                        | Fix                               |
|----------------------|--------------------------------|-----------------------------------|
| Vague instructions   | Claude takes minimal action    | Be explicit about scope and depth |
| No context           | Claude can't prioritize        | Explain WHY behaviors matter      |
| "Don't do X"         | Harder to follow than positive | Say what TO do instead            |
| No XML structure     | Instructions blend together    | Use XML tags for sections         |
| Assuming action      | Claude may only suggest        | Explicitly request implementation |
| No parallel guidance | Sequential tool calls          | Add `<use_parallel_tool_calls>`   |
| Over-trust           | Speculation about unread code  | Add investigation mandates        |
