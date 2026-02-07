---
description: Generate a structured changelog from recent git commits, categorized by change type
---

# Deep Wiki: Changelog Generation

Analyze the git commit history of this repository and generate a structured changelog.

## Process

1. Examine recent git commits (messages, dates, authors)
2. Group by date: daily for last 7 days, aggregated weekly for older
3. Classify each commit into categories
4. Generate concise, user-facing descriptions using project terminology from README

## Categories

| Emoji | Category | Signal Keywords |
|-------|----------|----------------|
| 🆕 | New Features | `feat`, `add`, `new`, `implement`, `introduce` |
| 🐛 | Bug Fixes | `fix`, `bug`, `patch`, `resolve`, `hotfix` |
| 🔄 | Refactoring | `refactor`, `restructure`, `reorganize`, `clean` |
| 📝 | Documentation | `docs`, `readme`, `comment`, `jsdoc`, `docstring` |
| 🔧 | Configuration | `config`, `env`, `setting`, `ci`, `build` |
| 📦 | Dependencies | `deps`, `upgrade`, `bump`, `package`, `lock` |
| ⚠️ | Breaking Changes | `breaking`, `BREAKING`, `migrate`, `deprecate` |

## Output

For each time period, output:

```markdown
## [Date or Date Range]

**[Summary Title]**

[1-2 sentence overview]

### 🆕 New Features
- [Change description]

### 🐛 Bug Fixes
- [Change description]

### ⚠️ Breaking Changes
- [Change description with migration notes]
```

Focus on **user-facing changes**. Merge related commits. Highlight breaking changes prominently.

$ARGUMENTS
