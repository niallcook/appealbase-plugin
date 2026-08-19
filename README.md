# Appealbase Plugin for Claude

This plugin connects Claude to [Appealbase](https://www.appealbase.com), a searchable database of over 100,000 UK planning appeal decisions issued by the Planning Inspectorate (PINS) for England.

## What this plugin does

Installing this plugin gives Claude access to three tools backed by the Appealbase MCP server:

- **Search planning appeals** (`search_appeals`) — full-text search across the corpus with filters for LPA, inspector, procedure, development type, decision outcome, date range, and site designations. Supports exact-match and boolean query modes. The `main_issues_only` flag scopes search to the extracted Main Issues section of each decision letter for high-signal precedent research.
- **List appeals by date** (`list_appeals`) — enumerate decisions by date or date range, with the same metadata filters, for monitoring and date-based workflows.
- **Get appeal full text** (`get_appeal_full_text`) — retrieve the complete inspector's decision letter and all structured metadata for a specific appeal by its 7-digit PINS reference. Supports chunked retrieval for long decisions.

The bundled skill teaches Claude the UK planning appeal system: how to read decision outcomes, what the Main Issues section is and why it matters, when to use each filter, and common research patterns for planning consultants and legal practitioners.

## What Appealbase is

Appealbase is a service built on the Planning Inspectorate's public appeal decision data. Decision letter text is Crown Copyright, published by PINS under the Open Government Licence v3.0. Appealbase adds structured metadata extraction (including the Main Issues section), full-text search, and a consistent schema across all decision types and dates.

The corpus covers England only. It includes all appeal types: Planning, Householder, Enforcement, Commercial, Lawful Development, and Listed Building. It does not cover Scotland (DPEA), Wales (PEDW), or Northern Ireland (PAC).

## How to connect

The Appealbase MCP server requires authentication. After installing this plugin, Claude will prompt you to sign in.

1. Go to [appealbase.com](https://www.appealbase.com) and create an account (free tier available).
2. Navigate to **Settings → MCP** to find your personal MCP URL with an embedded API key, or connect via OAuth when prompted by Claude.
3. If Claude Code prompts for a server URL, use: `https://www.appealbase.com/api/mcp`

The server is registered under the scoped name `plugin:appealbase:appealbase` when installed via this plugin.

## MCP server

The remote MCP server is at `https://www.appealbase.com/api/mcp`. It uses OAuth for authentication. All tools carry `readOnlyHint: true` — Appealbase is a read-only search service; no tool modifies any data.

## Privacy policy

Appealbase's privacy policy is available at **https://www.appealbase.com/privacy**.

The plugin connects to `appealbase.com`. Search queries, filter parameters, and your authenticated user identity are transmitted to the Appealbase server to execute searches. Search activity is logged for usage metering (rate limiting by account tier) and service analytics. No search data is sold to third parties. Decision letter text returned by the server is Crown Copyright material sourced from PINS under OGL v3.0.

## Repository structure

```
appealbase-plugin/
├── .claude-plugin/
│   └── plugin.json       # Plugin manifest
├── .mcp.json             # MCP server configuration
├── skills/
│   └── appealbase/
│       └── SKILL.md      # Planning appeals domain knowledge for Claude
└── README.md
```

## License

Plugin configuration and skill content: MIT.

Decision letter text returned at runtime: Crown Copyright, Open Government Licence v3.0.
Appealbase enrichment fields returned at runtime: [Appealbase commercial licence](https://www.appealbase.com/terms).
