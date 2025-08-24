---
description: Show progress across implementation guide hierarchy
argument-hint: [path]
allowed-tools: Read, LS, Bash(egrep:*), Bash(echo:*), Bash(find:*)
---

## Context
- Full implementation structure: !`egrep -rn '^#+ ' implementation-guide/ --include="*.md" 2>/dev/null || echo "No implementation guides found"`
- Current status sections: !`egrep -rn '^## Current Status' implementation-guide/ --include="*.md" -A 3 2>/dev/null || echo "No status sections found"`
- Directory hierarchy: !`find implementation-guide -maxdepth 5 -type d -name '[0-9]*' 2>/dev/null || echo "No numbered directories found"`

## Your task
Analyse completion status for: **$ARGUMENTS** (or all tasks if no path specified)

**Steps**:
1. **Parse current status sections** from all relevant documents
2. **Calculate completion percentages** based on:
   - Success criteria checkboxes completed
   - Current status indicators (Not Started|In Progress|Completed)
   - Actual Results vs Original Estimate completion
3. **Show hierarchical progress** with parent task aggregation
4. **Identify blockers** from status sections
5. **Display categorised overview**:
   - Feature progress (X/Y tasks completed)
   - Bugfix progress (X/Y tasks completed) 
   - Hotfix progress (X/Y tasks completed)
   - Chore progress (X/Y tasks completed)

**Output Format**:
```
Implementation Guide Status Report

CATEGORY OVERVIEW:
✅ Feature: 2/3 tasks completed (67%)
🔧 Bugfix: 1/2 tasks completed (50%)
🚨 Hotfix: 0/1 tasks completed (0%)
⚙️ Chore: 3/3 tasks completed (100%)

ACTIVE TASKS:
📋 feature/1-user-auth [In Progress] - Next: Complete testing
🐛 bugfix/2-login-error [In Progress] - Blocker: Awaiting staging environment

COMPLETED RECENTLY:
✅ chore/3-dependency-update [Completed] - Lessons learned captured
```

**Success**: Clear visibility into project progress and current focus areas