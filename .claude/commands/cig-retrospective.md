---
description: Facilitate post-completion analysis and variance tracking
argument-hint: <task-path>
allowed-tools: Read, Write, Bash(egrep:*), Bash(echo:*), Bash(find:*)
---

## Context
- Task sections: !`egrep -rn '^## (Original Estimate|Actual Results|Lessons Learned)' implementation-guide/ --include="*.md" 2>/dev/null || echo "No estimate/results sections found"`
- Completion status: !`egrep -rn '^## Current Status' implementation-guide/ --include="*.md" -A 2 2>/dev/null || echo "No status sections found"`
- Available tasks: !`find implementation-guide -maxdepth 5 -type d -name '[0-9]*' 2>/dev/null || echo "No tasks found"`

## Your task
Generate retrospective analysis for: **$ARGUMENTS**

**Parse arguments**: `<task-path>`
- task-path: Path to completed task (e.g., "feature/1-user-auth" or full path)

**Steps**:
1. **Locate task documents** by searching for task-path in directory structure
2. **Extract planning data**:
   - Original Estimate sections from all task documents
   - Success Criteria and goals from plan.md
   - Dependencies and constraints identified during planning
3. **Gather actual results**:
   - Current Status sections showing completion
   - Any existing Actual Results documentation
   - Implementation timeline and effort data
4. **Calculate variances**:
   - Time estimates vs. actual time spent
   - Scope changes and additions during implementation
   - Dependency resolution vs. original assumptions
5. **Generate retrospective report** with:
   - **What Went Well**: Successes and positive outcomes
   - **What Could Be Improved**: Areas for enhancement
   - **Key Learnings**: Insights for future similar work
   - **Process Improvements**: Suggested changes to approach
6. **Update task documents**:
   - Fill in any missing Actual Results sections
   - Add comprehensive Lessons Learned content
   - Mark task as completed with retrospective date

**Retrospective Report Structure**:
```markdown
# Retrospective: {Task Name}

## Executive Summary
- **Duration**: X days (estimated: Y days, variance: ±Z%)
- **Scope**: Original vs. final scope comparison
- **Outcome**: Success level and business impact

## Variance Analysis
### Time and Effort
- **Estimated**: Original time estimates by phase
- **Actual**: Actual time spent by phase
- **Variance**: Analysis of over/under estimates

### Scope Changes
- **Additions**: Features/requirements added during implementation
- **Removals**: Items descoped or deferred
- **Impact**: Effect on timeline and complexity

## What Went Well
- Successes and positive outcomes
- Effective processes and practices
- Team collaboration highlights

## What Could Be Improved
- Challenges faced and their impact
- Process inefficiencies identified
- Resource or skill gaps encountered

## Key Learnings
- Technical insights gained
- Process learnings for future work
- Risk mitigation strategies discovered

## Recommendations
- Process improvements for similar future tasks
- Tool or technique recommendations
- Team skill development suggestions
```

**Retrospective Facilitation**:
- **Prompt for missing data**: If Actual Results are incomplete, guide user to fill them in
- **Ask clarifying questions**: Help identify lessons learned through guided questions
- **Suggest improvements**: Based on variance analysis, suggest process improvements
- **Document insights**: Ensure learnings are captured for future reference

**Error Handling**:
- **Error**: Task not found or incomplete
- **Cause**: Task path doesn't exist or task not marked completed
- **Solution**: Verify task path and completion status before proceeding
- **Example**: Use /cig-status to see available completed tasks
- **Uncertainty**: If task completion status unclear, ask user to confirm task is ready for retrospective

**Success**: Comprehensive retrospective completed with actionable insights for future work