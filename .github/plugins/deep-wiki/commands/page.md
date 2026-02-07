---
description: Generate a single wiki page with Mermaid diagrams and citations for a specific topic or section
---

# Deep Wiki: Single Page Generation

Generate a comprehensive wiki page for the specified topic.

## Inputs

The user will provide a topic/title and optionally specific file paths. Use $ARGUMENTS to determine what to document.

## Mandatory Three-Phase Process

### Phase 1: Strategic Planning (ALWAYS FIRST)

1. Clarify the page's goals, audience, and deliverables
2. Determine scope based on relevant file count:
   - ≤50 files: full coverage
   - 50–300 files: prioritize critical paths
   - >300 files: tiered sampling (entry points, domain models, data access, integration edges)
3. Set documentation budget:
   - Small scope: ~2,000–3,000 words, 1–3 diagrams
   - Medium scope: ~3,000–5,000 words, 2–4 diagrams
   - Large/Complex: ~5,000–8,000+ words, 3–6 diagrams

### Phase 2: Deep Code Analysis

1. Read ALL relevant source files completely
2. Identify: architecture patterns, design patterns, algorithms, data flow, state management
3. Map: component dependencies, external integrations, API contracts
4. Record citation anchors: `file_path:line_number` for every claim

### Phase 3: Document Generation

Structure the page with:

- **Overview**: purpose, scope, executive summary
- **Architecture / System Design**: with `graph TB/LR` Mermaid diagram
- **Core Components**: purpose, implementation, design patterns, citations per component
- **Data Flow / Interactions**: with `sequenceDiagram` (use `autonumber`)
- **Implementation Details**: key algorithms, error handling, state management
- **Configuration & Deployment**: if applicable
- **References**: inline citations throughout using `(file_path:line_number)`

### Mermaid Requirements

Include **minimum 2 diagrams**, choosing from:

| Type | Best For |
|------|----------|
| `graph TB/LR` | Architecture, component relationships |
| `sequenceDiagram` | API flows, interactions (always use `autonumber`) |
| `classDiagram` | Class hierarchies, interfaces |
| `stateDiagram-v2` | State machines, lifecycle |
| `erDiagram` | Database schema, entities |
| `flowchart` | Data pipelines, decision trees |

### Citation Rules (MANDATORY)

- Every non-trivial claim: `(src/path/file.ts:42)`
- Approximate: `(src/path/file.ts:~ClassName)`
- Missing evidence: `(Unknown – verify in path/to/check)`
- Minimum 5 different source files cited per page

$ARGUMENTS
