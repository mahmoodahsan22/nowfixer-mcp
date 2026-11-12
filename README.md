# NowFixer MCP Server

**Debug ServiceNow scripts directly inside Claude, Cursor and VS Code.**

NowFixer analyzes ServiceNow scripts for bugs, performance problems and best-practice issues, then returns a corrected script and a code quality score. This repository hosts the public MCP (Model Context Protocol) endpoint for [NowFixer](https://nowfixer.dev).

- **MCP server URL:** `https://mcp.nowfixer.dev/mcp`
- **Claude connector directory:** [claude.ai/directory/nowfixer](https://claude.ai/directory/nowfixer)
- **Setup guide:** [nowfixer.dev/mcp](https://nowfixer.dev/mcp)
- **Web app:** [nowfixer.dev](https://nowfixer.dev)

Free. No account or sign-in needed.

---

## Quick start

### Claude (claude.ai, Desktop, mobile)

1. Open [NowFixer in Claude's connector directory](https://claude.ai/directory/nowfixer), or search for **NowFixer** under Connectors in Claude.
2. Click **Connect**.
3. Start a new chat and ask: `Use NowFixer to check this business rule:` followed by your script.

### Claude Code

```bash
claude mcp add --transport http nowfixer https://mcp.nowfixer.dev/mcp
```

### Cursor

Add this to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "nowfixer": {
      "url": "https://mcp.nowfixer.dev/mcp"
    }
  }
}
```

### VS Code

Add this to `.vscode/mcp.json`:

```json
{
  "servers": {
    "nowfixer": {
      "type": "http",
      "url": "https://mcp.nowfixer.dev/mcp"
    }
  }
}
```

### Other MCP clients

Any client that supports remote MCP servers over Streamable HTTP can connect to `https://mcp.nowfixer.dev/mcp`. No authentication is required.

---

## Tool

### `analyze_servicenow_script`

Read-only. Analyzes a ServiceNow script and returns:

- What the script does, step by step
- **Errors:** bugs, wrong API usage and logic problems
- **Risks:** performance, safety and best-practice issues
- A complete corrected script
- A code quality score from 1 to 10

| Parameter | Type | Required | Description |
|---|---|---|---|
| `script` | string | Yes | The ServiceNow script source code (max 20,000 characters) |
| `scriptType` | string | No | For example Business Rule, Script Include, Client Script, UI Action, Scheduled Job, Fix Script, Background Script |
| `expectedBehavior` | string | No | What the script is supposed to do, or field type hints such as fx_currency or journal fields |

Supported script types: Business Rules, Script Includes, Client Scripts, UI Actions, UI Policies, Scheduled Jobs, Fix Scripts and Background Scripts.

---

## What NowFixer checks

- GlideRecord queries inside loops and unused lookups
- `update()` loops that should be `updateMultiple()`, with correct `setValue()` usage
- Business Rules that bulk update records unrelated to the triggering record
- Safe use of `setWorkflow(false)` and `autoSysFields(false)`
- Journal fields (`work_notes`, `comments`) and `fx_currency` fields
- Counting with GlideAggregate instead of GlideRecord loops
- Hardcoded sys_ids, deprecated APIs and magic values
- GlideAjax Script Includes that don't extend `AbstractAjaxProcessor`

---

## Example prompts

- `Use NowFixer to review this Script Include before I deploy it: [script]`
- `Why is this Business Rule slow? Check it with NowFixer: [script]`
- `Use NowFixer to check this fix script and give me a safe version with a dry run: [script]`

---

## Privacy

Scripts sent through an AI assistant are analyzed and the result is returned to the assistant. **Nothing is stored in the NowFixer database.** Scripts are processed by Google's Gemini model through the Lovable AI Gateway. Requests to `mcp.nowfixer.dev` pass through Vercel, which forwards them to the NowFixer analyzer.

Don't include passwords, API keys, credentials or customer data in scripts.

Full privacy policy: [nowfixer.dev/privacy](https://nowfixer.dev/privacy)

---

## How this repository works

This repository contains the routing configuration for `mcp.nowfixer.dev`. The `vercel.json` file forwards MCP requests from `https://mcp.nowfixer.dev/mcp` to the NowFixer analyzer backend. Visiting `https://mcp.nowfixer.dev` in a browser redirects to the setup guide.

---

## Support

Questions, bugs or feedback: use the contact form at [nowfixer.dev](https://nowfixer.dev) or open an issue in this repository.
