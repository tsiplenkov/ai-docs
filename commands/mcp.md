---
description: Show MCP documentation guide and available contexts
---

# MCP Documentation System

**How to work with MCP documentation:**

1. **Auto-load context:** Use `/load-context` to automatically detect and load documentation for current directory

2. **Available tags in library-mcp:**
   Use `mcp__library-mcp__list_all_tags` to see all available documentation tags

3. **Search documentation:**
   - By tag: `mcp__library-mcp__get_by_tag` (e.g., tag="kubernetes", tag="shared-data")
   - By text: `mcp__library-mcp__get_by_text` (e.g., "helmfile diff")
   - By URL: `mcp__library-mcp__get_by_slug_or_url` (e.g., "/mcp-config-apps")

**Main contexts:**
- `cluster-charts` - Cluster-level infrastructure charts
- `shared-data` - Shared ConfigMaps, Secrets, PVCs
- `apps` - Application deployments
- `aliases` - Environment switching aliases

**Workflow:**
1. Navigate to project directory
2. Run `/load-context` to load appropriate documentation
3. Claude will have full context from documentation
4. Ask Claude to perform tasks - it will follow the documentation guidelines

**Documentation location:** `/home/tyler/projects/ai-docs/`
