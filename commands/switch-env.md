---
description: Switch to specific environment (e.g., b2b-dev, cmmndr-prod)
argument-hint: <environment-alias>
---

# Switch Environment

Target environment: **$ARGUMENTS**

**Steps:**

1. Load environment aliases documentation using `mcp__library-mcp__get_by_tag` with tag="aliases"

2. Find the alias **$ARGUMENTS** in the documentation

3. Show the user what this alias does:
   - Kubernetes context it switches to
   - Vault configuration
   - Environment variables set

4. Remind user to run: `$ARGUMENTS` in their shell to activate the environment

5. After user confirms the environment is switched, load the appropriate MCP context based on current directory
