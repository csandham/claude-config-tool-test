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

| Config feature                           | Claude Code | Cursor  | Windsurf | GitHub Copilot |
|------------------------------------------|-------------|---------|----------|----------------|
| TRELLIS — `CLAUDE.md` (root)             | ✅           | ✅       | ✅        | ✅              |
| WISTERIA — `.claude/CLAUDE.md`           | ✅           | ✅¹      | ✅        | ✅              |
| OBSIDIAN — `.claude/rules/`              | ✅           | ❌       | ✅        | ✅              |
| STONEFLY — `.claude/settings.json` hooks | ✅           | ❌       | ✅        | ❌              |
| CORMORANT — `.claude/skills/`            | ✅           | ✅       | ✅        | ✅              |
| GARNET — `.claude/agents/`               | ✅           | ✅       | ❌        | ✅              |
| PELICAN — `.mcp.json`                    | ✅           | ❌       | ❌        | ❌              |
| FERRET — `.claude/commands/`             | ✅           | ✅       | ❌        | ✅³             |
| LARKSPUR — `docs/CLAUDE.md`              | ✅²          | ✅¹      | ❌        | ❌              |

> ¹ **Cursor on-demand loading:** WISTERIA and LARKSPUR are not auto-loaded at session start. Instead, Cursor surfaces them via its "relevant cursor rules" system when files in the corresponding directory are accessed. The words appear, but the loading mechanism is on-demand rather than pre-loaded.
>
> ² **Claude Code on-demand loading:** LARKSPUR (directory-scoped `docs/CLAUDE.md`) is loaded on-demand when files in that directory are accessed, not at session start. WISTERIA (`.claude/CLAUDE.md`) is auto-loaded at session start.
>
> ³ **GitHub Copilot slash commands:** FERRET is detected when `/legacy-test` is invoked, but this works via the AI agent searching for `.claude/commands/legacy-test.md` rather than the IDE natively registering slash commands from `.claude/commands/`. The command is not surfaced in autocomplete.

## Repository structure

```
.
├── README.md                          # This file
├── CLAUDE.md                          # Root persistent instructions (TRELLIS)
├── TEST-PROMPT.md                     # Test prompts and results table
├── .mcp.json                          # MCP server registration
├── docs/
│   ├── CLAUDE.md                      # Directory-scoped config (LARKSPUR)
│   └── test-file.md                   # Test file to trigger docs/CLAUDE.md loading
└── .claude/
    ├── CLAUDE.md                      # Alt persistent instructions (WISTERIA)
    ├── settings.json                  # Hooks config (STONEFLY)
    ├── rules/
    │   └── conventions.md             # Auto-loaded rules (OBSIDIAN)
    ├── skills/
    │   └── config-test/
    │       └── SKILL.md               # Skills (CORMORANT)
    ├── agents/
    │   └── config-test-agent.md       # Subagent definition (GARNET)
    ├── commands/
    │   └── legacy-test.md             # Legacy slash command (FERRET)
    └── mcp-servers/
        ├── config-test-server.js      # MCP tool server (PELICAN)
        ├── package.json               # MCP server dependencies
        └── package-lock.json          # MCP server lockfile
```
