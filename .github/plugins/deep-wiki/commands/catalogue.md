---
description: Generate only the hierarchical wiki structure (table of contents) as JSON for the current repository
---

# Deep Wiki: Catalogue Generation

Analyze this repository and generate a hierarchical JSON documentation structure.

## Analysis Phase

1. Read the repository file tree and README
2. Detect project type, language composition, frameworks
3. Identify architectural layers and module boundaries
4. Map key components, services, controllers, and their relationships

## Output Requirements

Generate a JSON structure following this schema:

```json
{
  "items": [
    {
      "title": "getting-started",
      "name": "[Derived from project]",
      "prompt": "[Generation instruction]",
      "children": [
        {
          "title": "[auto-derived]",
          "name": "[Section Name]",
          "prompt": "[1-3 sentence instruction with file citations]",
          "children": []
        }
      ]
    },
    {
      "title": "deep-dive",
      "name": "[Derived from project]",
      "prompt": "[Generation instruction]",
      "children": []
    }
  ]
}
```

### Rules

- Max nesting depth: 4; ≤8 children per section
- Cite real files using `file_path:line_number` in every prompt
- Getting Started: overview, setup, basic usage, quick reference
- Deep Dive layers: Architecture → Subsystems → Components → Key methods/interfaces
- Component analysis: classes, services, controllers; dependencies; design patterns (Repository, Factory, Strategy, Observer)
- Small repo mode (≤10 files): Getting Started only, 1-2 children, skip Deep Dive

$ARGUMENTS
