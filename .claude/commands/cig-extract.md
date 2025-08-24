---
description: Extract specific section from implementation guide
argument-hint: <file-path> <section-name>
allowed-tools: Read, Bash(awk:*), Bash(egrep:*), Bash(echo:*)
---

## Context
- Available sections: !`egrep -rn '^#+ ' implementation-guide/ --include="*.md" 2>/dev/null || echo "No implementation guides found"`
- Section contents: awk '/^{section level and text}/{p=1; print; next} p && /^## [^#]/{p=0} p' {filename}
- Example: awk '/^## Goal/{p=1; print; next} p && /^## [^#]/{p=0} p' implementation-guide/feature/1-task/plan.md

## Your task
Extract section "**$ARGUMENTS**" from implementation guides

**Parse arguments**: `<file-path> <section-name>`
- file-path: Path to implementation guide document
- section-name: Section to extract (e.g., "Goal", "Success Criteria", "Current Status")

**Steps**:
1. **Validate file exists** and is readable
2. **Identify section level** (##, ###, ####) and exact text
3. **Extract section content** using awk pattern:
   ```bash
   awk '/^## {section-name}/{p=1; print; next} p && /^## [^#]/{p=0} p' {file-path}
   ```
4. **Handle variations**:
   - Different heading levels (adjust pattern as needed)
   - Section not found (clear error message)
   - Multiple matches (extract all instances)

**Common Sections to Extract**:
- "Original Estimate" - Initial planning estimates
- "Task Reference" - Task tracking integration  
- "Goal" - Single sentence objective
- "Success Criteria" - Measurable outcomes
- "Current Status" - Progress tracking
- "Actual Results" - Post-completion actuals
- "Lessons Learned" - Key insights and variances
- "Major Steps" - High-level breakdown
- "Dependencies" - External requirements

**Error Handling**:
- **Error**: Section not found in specified file
- **Cause**: Section name mismatch or file doesn't contain section
- **Solution**: Show available sections and suggest closest match
- **Example**: Available sections in file: Goal, Success Criteria, Current Status
- **Uncertainty**: If section name is ambiguous, show multiple matches and ask for clarification

**Success**: Section content extracted cleanly for further analysis or reference