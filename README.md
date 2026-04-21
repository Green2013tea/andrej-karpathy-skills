# AI Coding Agent Behavioral Guidelines

Maintained by [green2013tea](https://github.com/green2013tea).

A small set of instruction files and reusable prompts to improve AI coding agent behavior.

## The Problems

Common failure modes with coding agents:

- Making assumptions silently instead of asking clarifying questions
- Overcomplicating code and APIs with unnecessary abstraction
- Making orthogonal edits (comments/formatting/adjacent code) unrelated to the request

## The Solution

Four principles in one file that directly address these issues:

| Principle | Addresses |
|-----------|-----------|
| **Think Before Coding** | Wrong assumptions, hidden confusion, missing tradeoffs |
| **Simplicity First** | Overcomplication, bloated abstractions |
| **Surgical Changes** | Orthogonal edits, touching code you shouldn't |
| **Goal-Driven Execution** | Leverage through tests-first, verifiable success criteria |

## The Four Principles in Detail

### 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

LLMs often pick an interpretation silently and run with it. This principle forces explicit reasoning:

- **State assumptions explicitly** — If uncertain, ask rather than guess
- **Present multiple interpretations** — Don't pick silently when ambiguity exists
- **Push back when warranted** — If a simpler approach exists, say so
- **Stop when confused** — Name what's unclear and ask for clarification

### 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

Combat the tendency toward overengineering:

- No features beyond what was asked
- No abstractions for single-use code
- No "flexibility" or "configurability" that wasn't requested
- No error handling for impossible scenarios
- If 200 lines could be 50, rewrite it

**The test:** Would a senior engineer say this is overcomplicated? If yes, simplify.

### 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:

- Don't "improve" adjacent code, comments, or formatting
- Don't refactor things that aren't broken
- Match existing style, even if you'd do it differently
- If you notice unrelated dead code, mention it — don't delete it

When your changes create orphans:

- Remove imports/variables/functions that YOUR changes made unused
- Don't remove pre-existing dead code unless asked

**The test:** Every changed line should trace directly to the user's request.

### 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform imperative tasks into verifiable goals:

| Instead of... | Transform to... |
|--------------|-----------------|
| "Add validation" | "Write tests for invalid inputs, then make them pass" |
| "Fix the bug" | "Write a test that reproduces it, then make it pass" |
| "Refactor X" | "Ensure tests pass before and after" |

For multi-step tasks, state a brief plan:

```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let the LLM loop independently. Weak criteria ("make it work") require constant clarification.

## Install

**Option A: Claude Code Plugin (recommended)**

From within Claude Code, first add the marketplace:
```
/plugin marketplace add <your_github>/<your_repo>
```

Then install the plugin:
```
/plugin install <plugin_name>@<tag>
```

This installs the guidelines as a Claude Code plugin, making the skill available across all your projects.

**Option B: CLAUDE.md (per-project)**

New project:
```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/<your_github>/<your_repo>/main/CLAUDE.md
```

Existing project (append):
```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/<your_github>/<your_repo>/main/CLAUDE.md >> CLAUDE.md
```

## Agent Differences

- **Claude Code:** `CLAUDE.md` is the primary “project memory”. Use `/green2013tea-guidelines` to keep the principles active during non-trivial tasks.
- **OpenAI Codex:** Approval-mode driven (Suggest vs Auto Edit vs Full Auto). Prefer Suggest for surgical diffs; if using these project-local prompts/skills, start with `CODEX_HOME=<repo>/.codex`.
- **Gemini CLI:** Custom commands are TOML prompts (no shared skill system). Add relevant files to context explicitly, then run `/green2013tea-guidelines`.
- **Qwen Code:** Custom commands are Markdown files and can accept args/dynamic injections. Keep it surgical; only add dynamic context when directly needed.

## Project-local artifacts (multi-agent)

This repo also ships “native” project-local wrappers so you can use the same principles across runtimes:

- **Claude Code:** `.claude/skills/green2013tea-guidelines/SKILL.md`
- **Codex:** `.codex/skills/green2013tea-guidelines/SKILL.md` and `.codex/prompts/green2013tea-guidelines.md` (set `CODEX_HOME=<repo>/.codex` for discovery)
- **Gemini CLI:** `.gemini/commands/green2013tea-guidelines.toml`
- **Qwen Code:** `.qwen/commands/green2013tea-guidelines.md`

## Using with Cursor

This repository includes a committed Cursor project rule ([`.cursor/rules/green2013tea-guidelines.mdc`](.cursor/rules/green2013tea-guidelines.mdc)) so the same guidelines apply when you open the project in Cursor. See **[CURSOR.md](CURSOR.md)** for setup, using the rule in other projects, and how this relates to Claude Code.

## Key Insight

The "Goal-Driven Execution" principle captures a practical workflow: transform imperative instructions into declarative goals with verification loops.

## How to Know It's Working

These guidelines are working if you see:

- **Fewer unnecessary changes in diffs** — Only requested changes appear
- **Fewer rewrites due to overcomplication** — Code is simple the first time
- **Clarifying questions come before implementation** — Not after mistakes
- **Clean, minimal PRs** — No drive-by refactoring or "improvements"

## Customization

These guidelines are designed to be merged with project-specific instructions. Add them to your existing `CLAUDE.md` or create a new one.

For project-specific rules, add sections like:

```markdown
## Project-Specific Guidelines

- Use TypeScript strict mode
- All API endpoints must have tests
- Follow the existing error handling patterns in `src/utils/errors.ts`
```

## Tradeoff Note

These guidelines bias toward **caution over speed**. For trivial tasks (simple typo fixes, obvious one-liners), use judgment — not every change needs the full rigor.

The goal is reducing costly mistakes on non-trivial work, not slowing down simple tasks.

## License

MIT
