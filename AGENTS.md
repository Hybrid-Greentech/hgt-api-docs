> For Mintlify product knowledge (components, configuration, writing standards),
> install the Mintlify skill: `npx skills add https://mintlify.com/docs`

# Documentation project instructions

## About this project

- This is a documentation site built on [Mintlify](https://mintlify.com)
- Pages are MDX files with YAML frontmatter
- Configuration lives in `docs.json`
- Use the Mintlify MCP server, `https://mcp.mintlify.com`, to edit content and settings via MCP
- Use the Mintlify docs MCP server, `https://www.mintlify.com/docs/mcp`, to query information about using Mintlify via MCP

## Terminology

- "HGT" / "Hybrid Greentech" — the platform; "HGT platform" for the whole system.
- "DER" (Distributed Energy Resource) — an EV charger or heat pump integrating directly; keep distinct from "asset", the general term for anything dispatched (BESS, PV, DER, or delegated-operator-controlled equipment).
- "Delegated Operator" — a third party dispatching assets on HGT's behalf; not "aggregator" or "operator" alone.
- "Resource" — the EV-charger/heat-pump term for a registered device (`resourceId`); use "asset" (`assetId`) for delegated-operator and REST contexts. Don't mix the two within one API's docs.
- "Activation" — a real-time dispatch command; "task" — a planned/scheduled dispatch window (delegated operators only). Don't use interchangeably.

## Style preferences

{/* Add any project-specific style rules below */}

- Use active voice and second person ("you")
- Keep sentences concise — one idea per sentence
- Use sentence case for headings
- Bold for UI elements: Click **Settings**
- Code formatting for file names, commands, paths, and code references

## Content boundaries

- Document the three real-time integration surfaces (Delegated Operators, EV Chargers, Heat Pumps) and the OEM/PPC direct-hardware path. Don't document internal dashboard/reporting features — those are mentioned only as "available via our dashboard platform", never detailed here.
- AsyncAPI specs under `api-reference/asyncapi/` are the source of truth for schemas; MDX guides describe integration flow and policy, not a restatement of every field.
