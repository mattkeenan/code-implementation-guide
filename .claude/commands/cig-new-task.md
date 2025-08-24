---
description: Create categorised implementation guide
argument-hint: <task-type> [task-id] <description>
allowed-tools: Write, Read, LS, Bash(git:*), Bash(cat:*), Bash(egrep:*), Bash(echo:*), Bash(find:*)
---

## Context
- Autoload config: !`cat .cig/autoload.yaml 2>/dev/null || echo "No autoload config found"`
- Project config: !`cat implementation-guide/cig-project.json 2>/dev/null || echo "Run /cig-init first"`
- Existing tasks: !`egrep -rn '^#+ ' implementation-guide/ --include="*.md" 2>/dev/null || echo "No existing tasks"`
- Directory numbering: !`find implementation-guide -maxdepth 5 -type d -name '[0-9]*' 2>/dev/null || echo "Starting at 1"`

## Your task
Create new implementation guide for: **$ARGUMENTS**

**Parse arguments**: `<task-type> [task-id] <description>`
- task-type: feature|bugfix|hotfix|chore  
- task-id: optional (e.g., JIRA-1234, #567)
- description: brief task description

**Steps**:
1. **Validate task type** against `cig-project.json` supported-task-types
2. **Generate directory**: `implementation-guide/{task-type}/N-{description-slug}/`
3. **Create template files** based on task type:
   - Feature: plan.md, requirements.md, design.md, testing.md, rollout.md, maintenance.md
   - Bugfix: plan.md, implementation.md, testing.md, rollout.md  
   - Hotfix: plan.md, implementation.md, rollout.md
   - Chore: plan.md, implementation.md, validation.md, maintenance.md
4. **Populate Task Reference** section with tracking integration
5. **Suggest git branch** using `branch-naming-convention` from config

**Success**: Ready-to-use implementation guide with proper task tracking