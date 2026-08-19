
Claude finished the response
No — the whole plugin as a package, not the single manifest file. plugin.json is just metadata; on its own it points at a .mcp.json and a skills/ directory that wouldn't be there.

Zip the plugin directory so the archive contains .claude-plugin/plugin.json, .mcp.json, skills/ and README.md at its root, and upload that.

What's the point of installing a plugin that includes a connector if you then have to activate the connector

Because they're different things: the plugin distributes the configuration, the activation step grants consent. Without the plugin, a user has to find your server URL, paste it in, and configure it themselves — and they'd get none of the skill. With it, they click one button on a connector that's already pointed at the right endpoint, with the domain knowledge already loaded.

The consent step is deliberate and won't be designed away. Plugins can load remote and local MCP servers and other software, and Anthropic tells users to review which connectors are included and what permissions they request before use. A plugin that silently opened a network connection on install would be an obvious attack vector. 
Claude

Whether that's worth it for AppealBase is a fair question. The honest answer is that the bundle saves a user two steps rather than ten, and the real value you're adding is the skill, not the packaging.



Message collapsed

Read and edited memory
Read and edited memory
Structure and licensing are solid. The problem is "How to connect" — it's the one section a user reads under pressure, and it currently offers three mutually inconsistent routes without saying which is the real one.

Specifically: it says Claude will prompt you to sign in (it doesn't automatically — the connector arrives disabled with an Install button, as you just found), then offers a personal MCP URL with an embedded API key, then OAuth, then a manual URL entry that can't happen because .mcp.json already supplies the URL. The next section then states flatly that the server uses OAuth. A user following step 2 and a user following the MCP server section end up in different places.

The embedded-key route is the bit I'd cut outright rather than just clarify. Directory review requires OAuth 2.0 for authenticated services, and documenting a credential-in-URL as the primary path both undercuts that and encourages people to paste a live key into places it shouldn't go. Keep it as an alternative for non-Claude MCP clients if you need it, in a separate section, clearly labelled.

Rewritten:

How to connect
Create a free account at appealbase.com.
Install the plugin. The AppealBase connector appears in the plugin's Connectors page, disabled.
Click Install on the connector and sign in when prompted. The tools become available to Claude once authentication completes.
The server is registered under the scoped name plugin:appealbase:appealbase.

Smaller things: you're writing "Appealbase" throughout but the brand is AppealBase, which matters given the trademark position; and the License section promises MIT while the repository structure shows no LICENSE file.

The brand is Appealbase


Saved memory
Saved memory
My mistake — I imposed the camel-case on you. Your README was already consistent; ignore that point.

The LICENSE file and the connect section still stand.

Rewrite the README


Created a file, read a file
Created a file, read a file
Changes are confined to "How to connect", a paragraph added to "MCP server", and the LICENSE line in the structure block. Everything else is your text unaltered.


Readme
Document · MD 





Claude is AI and can make mistakes. Please double-check responses.


Readme · MD
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
 
1. Create a free account at [appealbase.com](https://www.appealbase.com).
2. Install the plugin. The Appealbase connector appears on the plugin's Connectors page in a disabled state — bundling a connector does not activate it.
3. Click **Install** on the connector and sign in via OAuth when prompted.
The tools become available to Claude once authentication completes. Until then, Claude will say it has no access to Appealbase.
 
The server is registered under the scoped name `plugin:appealbase:appealbase`.
 
## MCP server
 
The remote MCP server is at `https://www.appealbase.com/api/mcp`. It uses OAuth for authentication. All tools carry `readOnlyHint: true` — Appealbase is a read-only search service; no tool modifies any data.
 
If you are connecting from an MCP client other than Claude, your account settings at **Settings → MCP** provide a personal server URL. Treat that URL as a credential: it authenticates as you, so do not share it or commit it to a repository.
 
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
├── LICENSE
└── README.md
```
 
## License
 
Plugin configuration and skill content: MIT.
 
Decision letter text returned at runtime: Crown Copyright, Open Government Licence v3.0.
 
Appealbase enrichment fields returned at runtime: [Appealbase commercial licence](https://www.appealbase.com/terms).
 
