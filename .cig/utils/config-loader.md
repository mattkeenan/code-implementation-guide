# CIG Configuration Loader

## Purpose
Load and validate `cig-project.json` configuration with fallback handling.

## Configuration Loading Priority
1. `implementation-guide/cig-project.json` (project-specific)
2. Built-in defaults if file missing or invalid

## Required Fields Validation
- `project.name` and `project.description`
- `source-management.type` and `source-management.url`
- `task-management.type` and `task-management.url`
- `supported-task-types` array (must include: feature, bugfix, hotfix)

## Task ID Validation
Use `task-id-template` regex pattern from config:
- GitHub: `#[::digit::]{1,}`
- Jira: `[::upper::]{2,10}-[::digit::]{1,}`
- Validate provided task IDs against pattern

## Branch Name Generation
Apply `branch-naming-convention` template:
- Replace `{task-type}` with actual task type
- Replace `{task-id}` with provided task ID (if available)  
- Replace `{task-description}` with slugified description
- Respect `branch-name-max-length` limit

## Error Handling
**Error**: Configuration file missing or invalid
**Cause**: `cig-project.json` not found or malformed JSON
**Solution**: Run `/cig-init` to generate default configuration
**Example**: Valid minimal configuration structure
**Uncertainty**: If task ID format unclear, ask for clarification rather than guessing