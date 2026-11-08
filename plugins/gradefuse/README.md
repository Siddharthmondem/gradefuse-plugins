# Gradefuse for Claude Code

Ask your coding agent about your AI feature's evals, and get answers from
your [Gradefuse](https://www.gradefuse.com) project with links back to the
dashboard:

- _"Is it safe to move deal copy from Sonnet to Haiku? Accuracy must hold."_
- _"Why did item 7 regress in last night's run?"_
- _"Does this branch pass the release gate?"_

## Install

In Claude Code:

```
/plugin marketplace add Siddharthmondem/gradefuse-plugins
/plugin install gradefuse@gradefuse-plugins
```

Then give it a project API key. Create one in Gradefuse under **Settings →
Connect your coding agent** (or **Settings → API keys**), and set it in the
shell you start Claude Code from:

```bash
export GRADEFUSE_API_KEY=lp_…
```

Self-hosting Gradefuse? Also set `GRADEFUSE_BASE_URL` (for example
`https://gradefuse.example.com`). It defaults to `https://www.gradefuse.com`.

Restart Claude Code and run `/mcp`: `gradefuse` should be connected with 11
tools.

## What's included

**MCP server** (`.mcp.json`): the remote Gradefuse MCP server at
`$GRADEFUSE_BASE_URL/api/mcp`, authenticated with `GRADEFUSE_API_KEY`. The key
is scoped to one project.

- **It can:** read eval runs, release gates, evaluators and switch reports;
  create switch reports; run release-gate checks.
- **It can't:** delete anything, change evaluators, gates or settings, or
  spend LLM budget.

**Skills:**

| Skill               | Use it for                                                                                                                                        |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `switch-decision`   | "Can we switch to model or prompt B?" Creates a switch report from two eval runs and answers with the verdict, reasons, critical items and a link |
| `regression-triage` | "Why did this run or item get worse?" Compares with the baseline and explains the top regressed items                                             |
| `ship-check`        | "Can I merge this?" Checks the branch's latest eval run against the release gate, and triages if it fails                                         |

Claude picks a skill from what you ask; you don't need to name it.

## Without the plugin

The MCP server works on its own:

```bash
claude mcp add --transport http gradefuse https://www.gradefuse.com/api/mcp --header "Authorization: Bearer $GRADEFUSE_API_KEY"
```

Cursor and other clients: see
[the MCP server docs](https://github.com/Siddharthmondem/Gradefuse/blob/main/docs/integrations/mcp.md).

## Data handling

Tool results can include your eval items' inputs and outputs, and evaluator
reasoning. They're marked as untrusted customer content, and the skills tell
Claude to treat them as data, never as instructions.
