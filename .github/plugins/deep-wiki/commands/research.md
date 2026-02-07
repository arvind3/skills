---
description: Conduct multi-turn deep research on a specific topic within the repository codebase
---

# Deep Wiki: Deep Research

Conduct a comprehensive, multi-turn investigation of a specific topic within this codebase.

## Research Topic

$ARGUMENTS

## Process: 5-Iteration Research Cycle

You will perform 5 progressive research iterations. Each builds on all previous ones. NEVER repeat prior findings. ALWAYS provide substantive analysis.

### Iteration 1: Research Plan

- State the specific topic under investigation
- Outline key aspects to research
- Identify relevant files and components to examine
- Provide initial findings with file citations
- End with "Next Steps" for iteration 2

### Iterations 2–4: Progressive Deep Dives

Each iteration takes a different analytical lens:
- **Iteration 2**: Data flow and state management perspective
- **Iteration 3**: Integration, dependency, and API contract perspective
- **Iteration 4**: Pattern analysis — design patterns, anti-patterns, trade-offs

For each:
- Build upon ALL previous iterations
- Focus on one specific unexplored aspect
- Provide new insights with `(file_path:line_number)` citations
- Include Mermaid diagrams when they clarify discoveries
- End with remaining areas to investigate

### Iteration 5: Final Synthesis

- Synthesize ALL findings from iterations 1–4
- Include specific code references and implementation details
- Highlight the most important discoveries
- Provide actionable insights and recommendations
- List key findings as numbered items with citations

## Rules

- NEVER respond with just "Continue the research" — always provide substantive findings
- ALWAYS cite specific files: `(file_path:line_number)`
- ALWAYS build on previous iterations — do not repeat
- Stay focused on the specific topic — do not drift
