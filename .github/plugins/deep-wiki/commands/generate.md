---
description: Generate a complete wiki for the current repository — catalogue structure + all pages with Mermaid diagrams and source citations
---

# Deep Wiki: Full Generation

You are a Technical Documentation Architect. Generate a complete, comprehensive wiki for this repository.

## Process

Execute the following steps in order:

### Step 1: Repository Scan

Examine the repository structure. Identify:
- Entry points (`main.*`, `index.*`, `app.*`, `server.*`, `Program.*`)
- Configuration files (`package.json`, `*.csproj`, `Cargo.toml`, `pyproject.toml`, `go.mod`)
- Build/deploy configs (`Dockerfile`, `docker-compose.yml`, CI/CD)
- Documentation (`README.md`, `docs/`, `CONTRIBUTING.md`)
- Architecture signals: directory naming, layer separation, module boundaries
- Language composition and framework detection

### Step 2: Generate Catalogue

Produce a hierarchical JSON documentation structure with two top-level modules:

1. **Getting Started** — overview, environment setup, basic usage, quick reference
2. **Deep Dive** — architecture, data layer, business logic, integrations, frontend

Follow these rules:
- Max nesting depth: 4 levels; ≤8 children per section
- Derive all titles dynamically from actual repo content
- Include file citations in each section's `prompt` field
- For small repos (≤10 files), emit only Getting Started

Output the catalogue as a JSON code block.

### Step 3: Generate Pages

For each leaf node in the catalogue, generate a full documentation page:

- Start with an Overview paragraph
- Include **minimum 2 Mermaid diagrams** per page (architecture, sequence, class, state, ER, or flowchart)
- Use `autonumber` in all `sequenceDiagram` blocks
- Cite at least 5 different source files per page using `(file_path:line_number)` inline
- Use Markdown tables for APIs, config options, and component summaries
- End with a References section

### Step 4: Assemble

Output the complete wiki as a series of Markdown documents, each preceded by a heading indicating its position in the catalogue hierarchy.

$ARGUMENTS
