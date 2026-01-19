# Agent Skills

A template repository of skills, prompts, and MCP configurations for AI coding agents working with Azure SDKs and Microsoft AI services.

## SDK Quick Reference

| Package | Purpose | Install |
|---------|---------|---------|
| `azure-ai-projects` | Foundry project client, agents, evals, connections | `pip install azure-ai-projects` |
| `azure-ai-agents` | Standalone agents client (use via projects) | `pip install azure-ai-agents` |
| `azure-search-documents` | Azure AI Search SDK | `pip install azure-search-documents` |
| `azure-identity` | Authentication | `pip install azure-identity` |

## Authentication Pattern

Always use `DefaultAzureCredential` for production:

```python
from azure.identity import DefaultAzureCredential
from azure.ai.projects import AIProjectClient

credential = DefaultAzureCredential()
client = AIProjectClient(
    endpoint="https://<resource>.services.ai.azure.com/api/projects/<project>",
    credential=credential
)
```

## Environment Variables

```bash
AZURE_AI_PROJECT_ENDPOINT=https://<resource>.services.ai.azure.com/api/projects/<project>
AZURE_AI_MODEL_DEPLOYMENT_NAME=gpt-4o-mini
```

## Skills

Skills are domain-specific knowledge packages in `.github/skills/`. Each has a `SKILL.md` with:
- YAML frontmatter (`name`, `description`) — triggers skill loading
- Markdown body — loaded only when skill activates

For Azure SDK patterns, see:
- `azure-ai-search-python` — Search SDK patterns, vector/hybrid search, agentic retrieval
- `foundry-iq-agent` — Foundry agents with knowledge bases
- `mcp-builder` — Building MCP servers

## Conventions

- Prefer `async/await` for all Azure SDK I/O
- Use context managers: `with client:` or `async with client:`
- Close clients explicitly or use context managers
- Use `create_or_update_*` for idempotent operations
