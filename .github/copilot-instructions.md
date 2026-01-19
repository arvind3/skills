# Copilot Instructions for Agent Skills

## Project Overview

Agent Skills is a template repository of skills, prompts, and MCP configurations for AI coding agents working with Azure SDKs and Microsoft AI services.

## Repository Structure

```
.github/
├── skills/           # Domain-specific knowledge packages
│   └── */SKILL.md    # Each skill has YAML frontmatter + markdown body
├── copilot-instructions.md
```

## Skills

Skills are domain-specific knowledge packages in `.github/skills/`. Each skill has a `SKILL.md` with:
- **YAML frontmatter** (`name`, `description`) — triggers skill loading
- **Markdown body** — loaded only when skill activates

### Available Skills
- `azure-ai-search-python` — Search SDK patterns, vector/hybrid search, agentic retrieval
- `foundry-iq-agent` — Foundry agents with knowledge bases
- `mcp-builder` — Building MCP servers
- `skill-creator` — Guide for creating new skills
- `foundry-nextgen-frontend` — NextGen Design System UI patterns

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

## Conventions

- Prefer `async/await` for all Azure SDK I/O
- Use context managers: `with client:` or `async with client:`
- Close clients explicitly or use context managers
- Use `create_or_update_*` for idempotent operations

## Creating New Skills

1. Create a new directory under `.github/skills/<skill-name>/`
2. Add a `SKILL.md` file with YAML frontmatter:
   ```yaml
   ---
   name: skill-name
   description: Brief description of what the skill does
   ---
   ```
3. Add detailed instructions in the markdown body

## Do's and Don'ts

### Do
- ✅ Use `DefaultAzureCredential` for authentication
- ✅ Use async/await for all Azure SDK operations
- ✅ Include clear examples in skill documentation
- ✅ Keep skills focused on a single domain

### Don't
- ❌ Hardcode credentials or endpoints
- ❌ Create overly broad skills that cover multiple domains
- ❌ Skip YAML frontmatter in SKILL.md files
