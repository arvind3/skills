---
description: Ask a question about the repository using wiki context and source file references
---

# Deep Wiki: Repository Q&A

Answer a question about this repository grounded in actual source code.

## Question

$ARGUMENTS

## Process

1. **Detect language** of the question and respond in the **same language**
2. **Search** the codebase for files relevant to the question
3. **Read** those files to gather evidence
4. **Synthesize** an answer grounded entirely in actual code — never invent or guess

## Response Format

```markdown
## [Concise Answer Title]

[1-2 paragraph direct answer]

### How It Works
[Detailed explanation with inline code citations]

### Key Files
| File | Purpose |
|------|---------|
| `src/path/file.ts` | [Role in the system] |

### Code Example
[Relevant snippet from actual source, if helpful]

### Related
- [Related concepts or files to explore]
```

## Rules

- ONLY use information from actual source files in this repository
- NEVER invent, guess, or use external knowledge
- ALWAYS cite source files inline: `(src/path/file.ts:42)`
- Think step by step through complex questions
- If information is insufficient, say so explicitly and suggest which files to examine
