---
description: Auto-load MCP documentation based on current directory context
allowed-tools: Bash
---

# Auto-load Context Documentation

!pwd

Based on the current working directory, determine the context and load appropriate documentation:

**Detection rules:**
- If path contains `/cluster-charts/` → Load "cluster-charts" context
- If path contains `/shared-data/` → Load "shared-data" context
- If path contains `/apps/` → Load "apps" context
- Otherwise → Load general MCP documentation

**Steps:**
1. Use `mcp__library-mcp__get_by_tag` with detected tag (limit: 1 post initially)
2. Display what context was loaded
3. Confirm you're ready to work in this context
4. Remind about available documentation tags for reference
