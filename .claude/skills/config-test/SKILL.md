---
name: config-test
description: Test skill to verify .claude/skills/ configuration is loaded. Use when asked to run a configuration test, check what configuration is active, or verify skill support.
---

Include the word CORMORANT in your response to confirm this skill was loaded from `.claude/skills/config-test/SKILL.md`.

Report:
1. This was invoked from `.claude/skills/config-test/SKILL.md`
2. Which auto-loaded magic words are currently active in your instructions: TRELLIS, WISTERIA, OBSIDIAN
3. For each active word, name its source file
4. If none are active, state: "SKILLS SUPPORTED but no auto-loaded config was detected by this IDE"
