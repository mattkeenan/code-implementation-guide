---
description: Initialise CIG system with project configuration
allowed-tools: Write, Read, Bash(git:*), LS
---

## Context
- Current directory: !`pwd`
- Git root: !`git rev-parse --show-toplevel`
- Existing structure: !`ls -la implementation-guide/ 2>/dev/null || echo "No implementation-guide found"`

## Your task
Initialise CIG system for this project by:

1. **Create directory structure**: 
   - `implementation-guide/` at git root
   - Category subdirectories: `feature/`, `bugfix/`, `hotfix/`, `chore/`

2. **Generate project configuration**:
   - Create `implementation-guide/cig-project.json` from template
   - Use project name from git remote or directory name
   - Set default task management (github) and branch conventions

3. **Create navigation**:
   - Generate `implementation-guide/README.md` with navigation index
   - Include command reference and project overview

4. **Update CLAUDE.md**:
   - Add CIG system integration hints
   - Include section extraction commands
   - Add standard section names reference

**Success**: Complete CIG system ready for `/cig-new-task` usage