# agentcash skill — Scratchpad

## What works well
- Single entry point skill that covers the CLI workflow
- Available Services table gives quick overview of all origins with full URLs
- CLI-first approach: `npx agentcash fetch/discover/check` — no MCP dependency

## Potential improvements
- Could split into references/ files for each service's detailed API docs
- Could add a "common workflows" section (e.g., "research a company", "generate and upload an image")
- Wallet funding instructions could be more detailed (deposit link, invite codes)
- Could add error handling guidance (what to do when payment fails, insufficient balance, etc.)
- Consider adding a "cost estimation" section for common multi-step workflows
- Could mention `npx agentcash report-error` for reporting bugs

## Relationship to service-specific skills
- This skill is the "router" — it tells Claude which service to use
- Individual service skills (stableenrich, stablestudio, etc.) have detailed endpoint docs
- If both are installed, this skill handles discovery/routing while service skills handle endpoint details
- Could consider merging everything into this one skill with references/ files per service

## Context window considerations
- Current SKILL.md is ~80 lines — well under the 500 line limit
- If we add detailed API docs inline, it would bloat significantly
- Better to keep this lean and let `npx agentcash discover` + `npx agentcash check` provide runtime docs
- The CLI returns instructions in discovery output — so the skill mainly needs to teach *when* and *how* to use the CLI, not duplicate API docs

## Design decision: CLI over MCP
- All skills use `npx agentcash` CLI commands, not MCP tool calls
- This makes skills portable — works regardless of whether MCP is installed
- Full URLs with https:// prefix everywhere for clarity and copy-paste reliability
