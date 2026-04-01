# agent-config-test

A test harness for empirically validating which `.claude` configuration features are supported by different AI coding IDEs (Claude Code, Cursor, Windsurf, GitHub Copilot, etc.).

Each configuration mechanism embeds a unique magic word. Running the test prompts confirms which layers are actually loaded and active in a given IDE.

## How it works

Each magic word maps to one configuration concept:

| Word      | Source file                              | Config concept                  |
|-----------|------------------------------------------|---------------------------------|
| TRELLIS   | `CLAUDE.md` (root)                       | Persistent instructions (root)  |
| WISTERIA  | `.claude/CLAUDE.md`                      | Persistent instructions (alt)   |
| OBSIDIAN  | `.claude/rules/conventions.md`           | Rules directory                 |
| STONEFLY  | `.claude/settings.json` hooks            | Hooks (UserPromptSubmit)        |
| CORMORANT | `.claude/skills/config-test/SKILL.md`    | Skills                          |
| GARNET    | `.claude/agents/config-test-agent.md`    | Subagents                       |
| PELICAN   | `.claude/mcp-servers/config-test-server.js` | MCP servers (via `.mcp.json`) |
| FERRET    | `.claude/commands/legacy-test.md`        | Legacy slash commands           |
| LARKSPUR  | `docs/CLAUDE.md`                         | Nested CLAUDE.md (non-special)  |

If a word appears in the AI's response, the corresponding config layer was loaded.

## Setup

The MCP server test requires installing dependencies first:

```bash
cd .claude/mcp-servers && npm install
```

### Cursor

Cursor requires the **"Include third-party Plugins, Skills, and other configs"** setting to be enabled for `.claude/` configuration files to be loaded. Without this setting, most magic words will not be detected.

## Running the test

### Single-shot test (8 of 9 magic words)

Paste the prompt from [TEST-PROMPT.md](TEST-PROMPT.md) into the IDE chat. It tests TRELLIS, WISTERIA, OBSIDIAN, STONEFLY, CORMORANT, GARNET, PELICAN, and LARKSPUR in one shot.

### Manual test: FERRET

Type `/legacy-test` using the IDE's slash command menu. FERRET appearing in the response confirms `.claude/commands/` slash commands are supported.

## Results

| Config feature                           | Claude Code | VSCode Ext | Cursor | Windsurf | GitHub Copilot |
|------------------------------------------|-------------|------------|--------|----------|----------------|
| TRELLIS — `CLAUDE.md` (root)             | ✅           | ✅          | ✅      | ✅        | ✅              |
| WISTERIA — `.claude/CLAUDE.md`           | ✅           | ✅          | ❌      | ✅        | ✅              |
| OBSIDIAN — `.claude/rules/`              | ✅           | ✅          | ❌      | ✅        | ✅              |
| STONEFLY — `.claude/settings.json` hooks | ✅           | ✅          | ❌      | ✅        | ❌              |
| CORMORANT — `.claude/skills/`            | ✅           | ✅          | ✅      | ✅        | ✅              |
| GARNET — `.claude/agents/`               | ✅           | ✅          | ✅      | ❌        | ✅              |
| PELICAN — `.mcp.json`                    | ✅           | ✅          | ❌      | ❌        | ❌              |
| FERRET — `.claude/commands/`             | ✅           | ✅          | ✅      | ❌        | ❌              |
| LARKSPUR — `docs/CLAUDE.md`              | ✅           | ✅          | ❌      | ❌        | ❌              |

## Repository structure

```
.
├── CLAUDE.md                          # Root persistent instructions (TRELLIS)
├── TEST-PROMPT.md                     # Test prompts and results table
├── .mcp.json                          # MCP server registration
├── docs/
│   └── CLAUDE.md                      # Non-special subdirectory (LARKSPUR)
└── .claude/
    ├── CLAUDE.md                      # Alt persistent instructions (WISTERIA)
    ├── settings.json                  # Hooks config (STONEFLY)
    ├── rules/
    │   └── conventions.md             # Auto-loaded rules (OBSIDIAN)
    ├── skills/
    │   └── config-test/SKILL.md       # Skills (CORMORANT)
    ├── agents/
    │   └── config-test-agent.md       # Subagent definition (GARNET)
    ├── commands/
    │   └── legacy-test.md             # Legacy slash command (FERRET)
    └── mcp-servers/
        └── config-test-server.js      # MCP tool server (PELICAN)
```
