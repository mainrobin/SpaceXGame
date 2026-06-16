# DevCortex Agent Constitution
<!-- Generated: 2026-06-16T05:11:06.339Z -->

## MCP Connection

Configure `.mcp.json` in the project root:
```json
{
  "mcpServers": {
    "devcortex": {
      "type": "http",
      "url": "http://localhost:3001/mcp",
      "headers": { "x-api-key": "dc_live_<key>" }
    }
  }
}
```

## First Action — Always

1. `dc_health` — confirm MCP connection is live
2. Check the **Session Checkpoint** section at the bottom of this file
3. Fetch the first requirement to implement: `dc_get_requirement("REQ-NNN")`

**Do NOT load everything upfront.**
Do NOT call `dc_get_backlog`, `dc_get_spec`, or `dc_list_issues` at session start.
Fetch each requirement on demand as you implement it — one at a time.

## Tool Reference

| Tool | When to use |
|------|-------------|
| `dc_health` | First call — confirm MCP is live |
| `dc_get_requirement` | Before implementing a REQ — fetch ACs on demand |
| `dc_get_requirement_artifacts` | Fetch artifact refs only (lightweight) |
| `dc_get_backlog` | Only if you need to see full backlog (avoid at session start) |
| `dc_get_spec` | Only if spec content is needed for implementation |
| `dc_list_issues` | Only when debugging a specific issue |
| `dc_get_audit_trail` | Only when reviewing recent activity for a specific REQ |
| `dc_update_requirement_status` | Set IN_PROGRESS when you start a REQ |
| `dc_record_verification` | After tests pass — log PASS or FAIL per AC |
| `dc_create_issue` | When an AC cannot be implemented |
| `dc_resolve_issue` | When you fix a previously logged issue |
| `dc_log_implementation` | After completing a REQ |

**dc_get_requirement params:**
- `reqId` — e.g. `"REQ-001"`
- `includeNotes: true` — also returns `implementationNotes` and `artifactReferences` (omit to keep context minimal)

## Completion Checklist

- [ ] `date +%Y-%m-%d` run — today's date obtained
- [ ] All source files written — strict types, no `any`
- [ ] All files pass `npm run typecheck` (or your build tool's equivalent)
- [ ] All files pass `npm run lint` (or your linter's equivalent)
- [ ] Tests pass: `npm test` (or your test runner's equivalent)
- [ ] `dc_update_requirement_status` called with IN_PROGRESS at start of each REQ
- [ ] `dc_record_verification` called for each AC (PASS/FAIL/BLOCKED)
- [ ] `dc_log_implementation` called with AGENT and model name

## Session Checkpoint

_Update this section if session ends mid-sprint so the next session can resume._

```
Last completed:  — (fresh start)
In progress:     —
Next action:     Run dc_health to confirm MCP is live, then fetch first REQ
```

## Active Requirements

No approved requirements yet.

## Open Issues

No open issues.

## TypeScript Rules

See CLAUDE.md for TypeScript rules.
