# Migration Tools to Migrate v1.0 to v2.0 - Requirements

## Task Reference
- **Task ID**: internal-4
- **Task URL**: N/A (internal task)
- **Parent Task**: N/A
- **Branch**: feature/4-migration-tools
- **Template Version**: 2.0

## Goal
Define functional and non-functional specifications for migration tools that safely migrate existing v1.0 tasks to v2.0 hierarchical structure.

## Functional Requirements
### Core Features
- **FR1**: Directory Structure Migration
  - **Acceptance Criteria**: Migrate v1.0 directories from `implementation-guide/{type}/{num}-{description}/` to v2.0 format `implementation-guide/{num}-{type}-{description}/`
  - **Acceptance Criteria**: Preserve all file contents during migration
  - **Acceptance Criteria**: Update git history to track moved files (not delete/create)

- **FR2**: Workflow File Renaming
  - **Acceptance Criteria**: Rename v1.0 files (plan.md, requirements.md, etc.) to v2.0 lettered format (a-plan.md, b-requirements.md, etc.)
  - **Acceptance Criteria**: Maintain file content integrity during rename
  - **Acceptance Criteria**: Map files correctly: plan→a-plan, requirements→b-requirements, design→c-design, implementation→d-implementation, testing→e-testing, rollout→f-rollout, maintenance→g-maintenance

- **FR3**: Template Version Tagging
  - **Acceptance Criteria**: Add "Template Version: 2.0" to Task Reference section in all migrated files
  - **Acceptance Criteria**: Preserve all existing Task Reference fields (Task ID, URL, etc.)
  - **Acceptance Criteria**: Insert version tag without corrupting markdown structure

- **FR4**: Content Validation
  - **Acceptance Criteria**: Validate all markdown sections preserved during migration
  - **Acceptance Criteria**: Report any content loss or corruption
  - **Acceptance Criteria**: Verify all internal links still resolve correctly

- **FR5**: Rollback Capability
  - **Acceptance Criteria**: Create backup of pre-migration state
  - **Acceptance Criteria**: Provide rollback script to restore v1.0 structure if needed
  - **Acceptance Criteria**: Test rollback on sample task before production use

### User Stories
- **As a** CIG user **I want** to migrate my existing v1.0 tasks to v2.0 format **so that** I can use hierarchical workflow features without losing existing work
- **As a** CIG user **I want** validation that my content is preserved **so that** I can trust the migration process
- **As a** CIG user **I want** a rollback option **so that** I can safely recover if migration fails

## Non-Functional Requirements
### Performance (NFR1)
- Migration time: < 5 seconds per task (even large tasks with many files)
- Batch processing: Support migrating all tasks in single command
- Resource usage: < 100 MB memory during migration

### Usability (NFR2)
- Learning curve: < 5 minutes to understand migration process from documentation
- Error recovery: Clear error messages identifying which task/file failed and why
- Consistency: Follow same validation patterns as other CIG scripts
- Dry-run mode: Preview changes before applying them

### Maintainability (NFR3)
- Code clarity: Self-documenting script with clear function names
- Modularity: Separate concerns (backup, migrate directory, rename files, tag version, validate, rollback)
- Testability: Test migration on sample tasks before production
- Documentation: Include usage examples and troubleshooting guide

### Security (NFR4)
- File permissions: Maintain u+rx minimum on migrated files
- No data loss: Atomic operations where possible (backup before migrate)
- Hash verification: Optional SHA256 check that file content unchanged
- Git safety: Use git mv for tracked files to preserve history

### Reliability (NFR5)
- Availability: Script must handle partial failures gracefully
- Error handling: Stop migration on first error, don't continue corrupting data
- Data integrity: Validate markdown structure before and after migration
- Rollback guarantee: Rollback script tested and confirmed working before release

## Constraints
- **Git History Preservation**: Must use `git mv` for tracked files to maintain git history
- **Backward Compatibility**: v2.0 commands must still support v1.0 format during transition period (already implemented in hierarchy-resolver.sh)
- **No Manual Intervention**: Migration must be fully automated, no manual file edits required
- **Idempotency**: Running migration twice should be safe (detect already-migrated tasks and skip)
- **No Breaking Changes to Existing Tasks**: Migration must not corrupt or lose data from v1.0 tasks

## Acceptance Criteria
- [ ] AC1: Migration script successfully migrates tasks 1-3 from v1.0 to v2.0 format
- [ ] AC2: All file contents verified identical before and after migration (hash comparison)
- [ ] AC3: Rollback script tested and confirmed to restore v1.0 structure
- [ ] AC4: Validation script confirms Template Version 2.0 in all migrated files
- [ ] AC5: Git history preserved for all tracked files (git log --follow shows continuity)
- [ ] AC6: Migration script provides dry-run mode showing what would change
- [ ] AC7: Error messages clearly identify failure point and provide recovery steps
- [ ] AC8: Documentation includes step-by-step migration guide with examples

## Extended Requirements: Status System Enhancement

### Background
During rollout phase preparation, discovered that:
- `cig-project.json` has `workflow.status-values` array (not used)
- `status-aggregator.sh` has hardcoded status map (disconnected from config)
- No validation or documentation of valid status values
- Templates use arbitrary status strings ("Ready for Rollout", etc.)

### Additional Functional Requirements

- **FR9**: Configurable Status Values
  - **Acceptance Criteria**: Status values defined in `cig-project.json` as JSON object (status name → percentage)
  - **Acceptance Criteria**: `status-aggregator.sh` loads status map from config file instead of hardcoded values
  - **Acceptance Criteria**: Unknown status values default to 0% (backward compatible)
  - **Acceptance Criteria**: Support for projects to customize workflow stages

- **FR10**: Status Value Migration
  - **Acceptance Criteria**: Update `cig-project.json` from array format to object format
  - **Acceptance Criteria**: Migrate existing hardcoded status values to config file
  - **Acceptance Criteria**: Preserve backward compatibility (unknown statuses = 0%)

- **FR11**: Status Validation and LLM Guidance
  - **Acceptance Criteria**: Templates use only valid status values from config
  - **Acceptance Criteria**: Workflow command documentation instructs LLM to read valid statuses from cig-project.json
  - **Acceptance Criteria**: LLM instructions include jq commands to list valid status values
  - **Acceptance Criteria**: status-aggregator.sh warns to stderr when unknown status encountered
  - **Acceptance Criteria**: Warning shows: actual value from file, mapped result ("UNKNOWN"), effective percentage (0%)
  - **Acceptance Criteria**: Documentation in workflow-steps.md shows how to query valid status values

### Configuration Format
```json
"workflow": {
  "status-values": {
    "Backlog": 0,
    "To-Do": 0,
    "In Progress": 25,
    "Implemented": 50,
    "Testing": 75,
    "Finished": 100
  }
}
```

### Additional Acceptance Criteria
- [ ] AC9: `cig-project.json` updated with status-values object format
- [ ] AC10: `status-aggregator.sh` loads status map from config file using jq
- [ ] AC11: `status-aggregator.sh` warns to stderr when unknown status encountered (shows actual, mapped, effective values)
- [ ] AC12: All templates updated to use valid status values from config
- [ ] AC13: Status values self-documenting (visible in cig-project.json)
- [ ] AC14: `workflow-steps.md` documents how to query valid status values with jq commands
- [ ] AC15: Each workflow command (.claude/commands/cig-*.md) instructs LLM to use valid status values from config

## Status
**Status**: Finished
**Next Action**: N/A (Requirements phase completed)
**Blockers**: None

## Actual Results
*To be filled upon completion*

## Lessons Learned
*To be captured during implementation*
