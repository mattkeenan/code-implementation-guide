---
description: Create sub-implementation task within existing task
argument-hint: <parent-task-name> <subtask-name>
allowed-tools: Write, Read, LS, Bash(egrep:*), Bash(echo:*), Bash(find:*)
---

## Context
- Current task hierarchy: !`egrep -rn '^#+ ' implementation-guide/ --include="*.md" 2>/dev/null || echo "No existing tasks"`
- Parent task structure: !`find implementation-guide -maxdepth 5 -type d -name '[0-9]*' 2>/dev/null || echo "No numbered directories"`
- Existing subtasks: !`find implementation-guide -mindepth 3 -maxdepth 5 -type d -name '[0-9]*' 2>/dev/null || echo "No existing subtasks"`

## Your task
Create subtask "**$ARGUMENTS**" with hierarchical numbering (2.1.3 format)

**Parse arguments**: `<parent-task-name> <subtask-name>`
- parent-task-name: Name or partial name of parent task (e.g., "user-auth", "login-validation")
- subtask-name: Name for the new subtask (e.g., "user-model")

**Steps**:
1. **Find parent task path** by searching Current task hierarchy for matching parent-task-name
2. **Validate parent task exists** and is a valid implementation guide directory  
3. **Determine next subtask number**:
   - Scan existing subtasks in parent directory
   - Generate next number in sequence (e.g., if 2.1 exists, create 2.2)
   - Handle multiple levels (e.g., parent 2.1 creates 2.1.1, 2.1.2, etc.)
4. **Create subtask directory**: `{parent-path}/{next-number}-{subtask-name}/`
5. **Generate basic template files**:
   - `plan.md` - Basic planning template with Task Reference
   - `implementation.md` - Concrete implementation steps
   - `testing.md` - Validation and testing approach
6. **Update parent task documentation**:
   - Add subtask reference to parent's plan.md in Major Steps or Dependencies
   - Link with section reference: "See {subtask-number}-{name}/plan.md 'Goal' section"

**Hierarchical Numbering Rules**:
- **Level 1**: 1, 2, 3 (main tasks)
- **Level 2**: 1.1, 1.2, 1.3 (subtasks)
- **Level 3**: 1.1.1, 1.1.2, 1.1.3 (micro-tasks)
- **Filesystem sync**: Directory structure must match numbering

**Template Population**:
- **Parent Task Reference**: Link subtask back to parent
- **Inherited Context**: Copy relevant context from parent task
- **Focused Scope**: Narrow scope appropriate for subtask level

**Error Handling**:
- **Error**: Parent task path doesn't exist
- **Cause**: Invalid path or task not yet created
- **Solution**: Verify parent task path and run /cig-new-task if needed
- **Example**: Valid parent paths: implementation-guide/feature/1-main-task
- **Uncertainty**: If subtask level is ambiguous, show current hierarchy and ask for clarification

**Success**: Ready-to-use subtask with proper hierarchical integration and parent task linking