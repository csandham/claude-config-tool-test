# .claude Configuration Test

This file tests which Claude Code configuration features are supported by the current IDE.
Each feature embeds a unique magic word. The presence of a word in a response proves that
configuration was loaded or invoked.

## Magic Word Legend

| Word      | Source file                                   | Config concept                    | How triggered              |
|-----------|-----------------------------------------------|-----------------------------------|----------------------------|
| TRELLIS   | `CLAUDE.md` (root)                            | Persistent instructions (root)    | Auto-loaded                |
| WISTERIA  | `.claude/CLAUDE.md`                           | Persistent instructions (alt)     | Auto-loaded                |
| OBSIDIAN  | `.claude/rules/conventions.md`                | Rules directory                   | Auto-loaded                |
| STONEFLY  | `.claude/settings.json` hooks                 | Hooks (UserPromptSubmit)          | Auto-injected on every prompt |
| CORMORANT | `.claude/skills/config-test/SKILL.md`         | Skills                            | `/config-test` or auto     |
| GARNET    | `.claude/agents/config-test-agent.md`         | Subagents                         | Agent delegation           |
| PELICAN   | `.claude/mcp-servers/config-test-server.js`   | MCP servers (via `.mcp.json`)     | `config_test` tool call    |
| FERRET    | `.claude/commands/legacy-test.md`             | Legacy slash commands             | `/legacy-test` (manual)    |
| LARKSPUR  | `docs/CLAUDE.md`                              | Nested CLAUDE.md (non-special)    | Loaded when docs/ file referenced |

---

## Setup

### MCP test dependency

```bash
cd .claude/mcp-servers && npm install
```

### Cursor

Cursor requires the **"Include third-party Plugins, Skills, and other configs"** setting to be
enabled for `.claude/` configuration files to be loaded. Without this setting, most magic words
will not be detected.

---

## Single-Shot Test (tests 8 of 9 magic words)

Paste the following prompt into the IDE chat exactly as written:

---

```
Please run a comprehensive .claude configuration test by completing all of the following steps in order:

1. Note any configuration keywords you have automatically been instructed to include — look for words like TRELLIS, WISTERIA, OBSIDIAN, STONEFLY, and LARKSPUR already present in your instructions.
2. Read the file docs/test-file.md to test if directory-scoped CLAUDE.md files are loaded when files in that directory are referenced.
3. Use the config-test-agent to verify subagent support.
4. Invoke the config-test skill.
5. Call the config_test MCP tool.

After completing all steps, produce a results table:

| Magic Word | Expected Source                                 | Detected? |
|------------|-------------------------------------------------|-----------|
| TRELLIS    | CLAUDE.md (root) — auto-loaded                  |           |
| WISTERIA   | .claude/CLAUDE.md — auto-loaded                 |           |
| OBSIDIAN   | .claude/rules/ — auto-loaded                    |           |
| STONEFLY   | .claude/settings.json hook (UserPromptSubmit)   |           |
| CORMORANT  | .claude/skills/config-test/ (skill)             |           |
| GARNET     | .claude/agents/config-test-agent (agent)        |           |
| PELICAN    | .mcp.json — config_test MCP tool                |           |
| LARKSPUR   | docs/CLAUDE.md — loaded when docs/ referenced   |           |

For each word, mark ✅ if it appeared at any point during this test, or ❌ if not detected.
For each ❌, add: "[WORD] NOT DETECTED — [config type] is not supported or not loaded by this IDE."
```

---

## Manual Test: FERRET (slash command UI)

FERRET cannot be triggered from within a message body. It tests whether the IDE renders
`.claude/commands/` as interactive slash commands in its UI.

> Type `/legacy-test` in the chat input using the IDE's slash command menu.
>
> **Pass:** FERRET appears in the response.
> **Fail:** Command not recognized — `.claude/commands/` slash commands are not supported.

---

## Concepts Not Testable via Magic Words

| Concept | Why no magic word | How to verify manually |
|---------|-------------------|------------------------|
| Permissions (`.claude/settings.json`) | Affects tool access, not text output | Check if `allow`/`deny` rules take effect on tool calls |
| Output styles | Affects response style, not specific words | Check if response formatting or tone changes |
| Plugins | Require a separate packaged extension | Not demonstrated in this repo |
| `~/.claude.json` | Internal app state, not a user-authored config surface | Not applicable |

---

## Results Table

Fill in after running the single-shot prompt and the manual FERRET test in each IDE:

| Config feature                           | Claude Code | Cursor | Windsurf | GitHub Copilot |
|------------------------------------------|-------------|--------|----------|----------------|
| TRELLIS — `CLAUDE.md` (root)             | ✅           | ✅      | ✅        | ✅              |
| WISTERIA — `.claude/CLAUDE.md`           | ✅           | ❌      | ✅        | ✅              |
| OBSIDIAN — `.claude/rules/`              | ✅           | ❌      | ✅        | ✅              |
| STONEFLY — `.claude/settings.json` hooks | ✅           | ❌      | ✅        | ❌              |
| CORMORANT — `.claude/skills/`            | ✅           | ✅      | ✅        | ✅              |
| GARNET — `.claude/agents/`               | ✅           | ✅      | ❌        | ✅              |
| PELICAN — `.mcp.json`                    | ✅           | ❌      | ❌        | ❌              |
| FERRET — `.claude/commands/`             | ✅           | ✅      | ❌        | ❌              |
| LARKSPUR — `docs/CLAUDE.md`             | ❌           |        |          |                |
