# Migration Tools to Migrate v1.0 to v2.0 - Implementation

## Task Reference
- **Task ID**: internal-4
- **Task URL**: N/A (internal task)
- **Parent Task**: N/A
- **Branch**: feature/4-migration-tools
- **Template Version**: 2.0

## Goal
Implement migration tools following the approved pipeline architecture with git-first backup strategy.

## Workflow
Patterns first → Test → Minimal impl → Refactor green → Commit message explains "why"

## Files to Modify
### Primary Changes (New Scripts)
- `.cig/scripts/migrate-v1-to-v2.sh` - Main migration orchestrator with pipeline stages
- `.cig/scripts/validate-migration.sh` - Content integrity validation
- `.cig/scripts/rollback-migration.sh` - Git-aware rollback functionality

### Supporting Changes
- `.cig/security/script-hashes.json` - Add SHA256 hashes for new scripts
- `.cig/templates/pool/*.md.template` - Add Migration field placeholder (optional for future tasks)
- `README.md` - Update migration instructions section
- `.gitignore` - Add `.cig/migration-backup/` and `.cig/migration-state.json`

## Implementation Steps

### Step 1: Preparation and Pattern Study
- [ ] Review existing helper script patterns (hierarchy-resolver.sh, format-detector.sh)
- [ ] Identify v1.0 tasks in current repository (tasks 1-3)
- [ ] Document v1.0 → v2.0 path transformation rules
- [ ] Create `.cig/migration-backup/` directory structure

### Step 2: Implement migrate-v1-to-v2.sh (Main Pipeline)
- [ ] Create script skeleton with usage help and argument parsing
- [ ] Implement Phase 1: Discovery (find v1.0 tasks)
- [ ] Implement Phase 2: Pre-flight checks (git status, backup strategy)
- [ ] Implement Phase 3: Backup creation (git tag or manual copy)
- [ ] Implement Phase 4: Migration pipeline per task
  - [ ] Compute pre-migration SHA256 hashes
  - [ ] Determine v2.0 target path
  - [ ] Execute directory move (git mv or mv)
  - [ ] Execute file renames (git mv or mv)
  - [ ] Insert Template Version field
  - [ ] Insert Migration field with backup reference
  - [ ] Compute post-migration SHA256 hashes
  - [ ] Validate integrity
- [ ] Implement Phase 5: Validation (call validate-migration.sh)
- [ ] Implement Phase 6: Reporting (success/failure with rollback command)
- [ ] Add --dry-run mode (show changes without applying)
- [ ] Set permissions: chmod u+rx migrate-v1-to-v2.sh

### Step 3: Implement validate-migration.sh
- [ ] Create script skeleton with usage help
- [ ] Implement SHA256 hash comparison (pre vs post)
- [ ] Implement markdown structure validation (check for ## headers)
- [ ] Implement Template Version detection
- [ ] Implement Migration field detection
- [ ] Implement file existence checks
- [ ] Add clear error reporting (which file, what failed)
- [ ] Set permissions: chmod u+rx validate-migration.sh

### Step 4: Implement rollback-migration.sh
- [ ] Create script skeleton with usage help
- [ ] Implement backup reference detection (git tag vs directory)
- [ ] Implement git rollback (git reset --hard <tag>)
- [ ] Implement manual rollback (restore from .cig/migration-backup/)
- [ ] Implement migration-state.json cleanup
- [ ] Add safety checks (confirm rollback action)
- [ ] Set permissions: chmod u+rx rollback-migration.sh

### Step 5: Supporting Changes
- [ ] Update .gitignore with migration artifacts
- [ ] Update README.md with migration instructions
- [ ] Optionally update pool templates with Migration field placeholder
- [ ] Update script-hashes.json with new script hashes

### Step 6: Integration Testing
- [ ] Test migration with --dry-run on task 1
- [ ] Verify dry-run output shows expected changes
- [ ] Test actual migration on task 1 (smallest task)
- [ ] Validate migrated task 1 structure and content
- [ ] Test rollback functionality
- [ ] Verify rollback restored original state
- [ ] Test migration on tasks 2-3
- [ ] Test idempotency (running migration twice)

### Step 7: Documentation
- [ ] Add usage examples to script help text
- [ ] Document migration process in README.md
- [ ] Document rollback process
- [ ] Add troubleshooting section for common issues

### Step 8: Final Validation
- [ ] All acceptance criteria met (AC1-AC8 from requirements)
- [ ] All scripts have proper permissions (u+rx minimum)
- [ ] All scripts registered in script-hashes.json
- [ ] Manual test of full migration workflow
- [ ] Verify git history preserved with git log --follow

## Code Changes

### Example 1: Discovery Phase (Finding v1.0 Tasks)
```bash
# In migrate-v1-to-v2.sh
discover_v1_tasks() {
    # Find tasks in v1.0 format: implementation-guide/{type}/{num}-{desc}/
    find implementation-guide -mindepth 2 -maxdepth 2 -type d \
        -path "implementation-guide/feature/*" \
        -o -path "implementation-guide/bugfix/*" \
        -o -path "implementation-guide/hotfix/*" \
        -o -path "implementation-guide/chore/*" \
    | while read -r v1_path; do
        # Extract task number from directory name
        task_num=$(basename "$v1_path" | grep -oP '^\d+(?:\.\d+)*')

        # Check if already migrated (has Template Version: 2.0)
        if grep -q "Template Version.*2\.0" "$v1_path"/*.md 2>/dev/null; then
            echo "Skipping task $task_num (already migrated)" >&2
            continue
        fi

        echo "$v1_path|$task_num"
    done
}
```

### Example 2: Git-First Backup Strategy
```bash
# In migrate-v1-to-v2.sh
create_backup() {
    local timestamp=$(date +%Y%m%d-%H%M%S)

    # Check if git repo exists
    if git rev-parse --git-dir > /dev/null 2>&1; then
        # Check for uncommitted changes
        if [[ -n "$(git status --porcelain)" ]]; then
            echo "Error: Uncommitted changes detected. Commit or stash first." >&2
            exit 1
        fi

        # Create git tag backup
        git tag "migration-backup-$timestamp"
        BACKUP_REF="git:migration-backup-$timestamp"
        BACKUP_TYPE="git"
        echo "Backup created: git tag $BACKUP_REF"
    else
        # Manual backup
        local backup_dir=".cig/migration-backup/$timestamp"
        mkdir -p "$backup_dir"
        cp -r implementation-guide/ "$backup_dir/"
        BACKUP_REF="$backup_dir/"
        BACKUP_TYPE="manual"
        echo "Backup created: $backup_dir"
    fi
}
```

### Example 3: Path Transformation (v1.0 → v2.0)
```bash
# In migrate-v1-to-v2.sh
compute_v2_path() {
    local v1_path="$1"  # e.g., "implementation-guide/feature/1-initial-guide/"

    # Extract components
    local type=$(echo "$v1_path" | cut -d'/' -f2)  # "feature"
    local basename=$(basename "$v1_path")          # "1-initial-guide"
    local num=$(echo "$basename" | grep -oP '^\d+(?:\.\d+)*')  # "1"
    local desc=$(echo "$basename" | sed "s/^$num-//")  # "initial-guide"

    # Construct v2.0 path: implementation-guide/{num}-{type}-{desc}/
    local v2_path="implementation-guide/${num}-${type}-${desc}/"
    echo "$v2_path"
}
```

### Example 4: Insert Migration Metadata
```bash
# In migrate-v1-to-v2.sh
insert_migration_metadata() {
    local file="$1"
    local backup_ref="$2"

    # Find the line after "- **Branch**: ..." and insert metadata
    sed -i "/- \*\*Branch\*\*:/a\\
- **Template Version**: 2.0\\
- **Migration**: v1.0 ($backup_ref) → v2.0" "$file"
}
```

### Example 5: SHA256 Validation
```bash
# In validate-migration.sh
validate_content_integrity() {
    local task_dir="$1"
    local pre_hash_file=".cig/migration-state.json"  # Contains pre-migration hashes

    for md_file in "$task_dir"/*.md; do
        local current_hash=$(sha256sum "$md_file" | cut -d' ' -f1)
        local expected_hash=$(jq -r ".tasks[] | select(.v2_path == \"$task_dir\") | .files_migrated[] | select(.new == \"$(basename "$md_file")\") | .sha256_post" "$pre_hash_file")

        if [[ "$current_hash" != "$expected_hash" ]]; then
            echo "ERROR: Content mismatch in $md_file" >&2
            echo "  Expected: $expected_hash" >&2
            echo "  Got:      $current_hash" >&2
            return 1
        fi
    done

    return 0
}
```

### Example 6: Rollback with Git
```bash
# In rollback-migration.sh
rollback_migration() {
    local backup_ref="$1"

    if [[ "$backup_ref" == git:* ]]; then
        # Git-based rollback
        local tag="${backup_ref#git:}"

        if git tag | grep -q "^$tag$"; then
            echo "Rolling back to git tag: $tag"
            git reset --hard "$tag"
            git tag -d "$tag"  # Remove backup tag
            rm -f .cig/migration-state.json
            echo "Rollback successful"
            return 0
        else
            echo "Error: Git tag $tag not found" >&2
            return 1
        fi
    else
        # Directory-based rollback
        if [[ -d "$backup_ref" ]]; then
            echo "Rolling back from directory: $backup_ref"
            rm -rf implementation-guide/
            cp -r "$backup_ref/implementation-guide" .
            rm -f .cig/migration-state.json
            echo "Rollback successful"
            return 0
        else
            echo "Error: Backup directory $backup_ref not found" >&2
            return 1
        fi
    fi
}
```

## Test Coverage

### Manual Integration Tests (No automated test suite for migration)

#### Test Suite 1: Discovery Phase
- [ ] **TC1.1**: Find all v1.0 tasks (tasks 1-3) correctly
- [ ] **TC1.2**: Skip already-migrated tasks (task 4 is v2.0)
- [ ] **TC1.3**: Filter by task number correctly (e.g., migrate only task 1)

#### Test Suite 2: Backup Strategy
- [ ] **TC2.1**: Git repo with clean state → creates git tag backup
- [ ] **TC2.2**: Git repo with uncommitted changes → errors correctly
- [ ] **TC2.3**: No git repo → creates manual backup in `.cig/migration-backup/{timestamp}/`
- [ ] **TC2.4**: Backup reference correctly formatted (git: or directory path)

#### Test Suite 3: Migration Pipeline
- [ ] **TC3.1**: Directory structure migrated correctly (v1.0 → v2.0 path)
- [ ] **TC3.2**: All workflow files renamed (plan.md → a-plan.md, etc.)
- [ ] **TC3.3**: Template Version field inserted correctly
- [ ] **TC3.4**: Migration field inserted with correct backup reference
- [ ] **TC3.5**: SHA256 hashes match pre and post migration
- [ ] **TC3.6**: Git history preserved (git log --follow shows continuity)

#### Test Suite 4: Validation
- [ ] **TC4.1**: validate-migration.sh detects Template Version field
- [ ] **TC4.2**: validate-migration.sh detects Migration field
- [ ] **TC4.3**: validate-migration.sh detects content changes (hash mismatch)
- [ ] **TC4.4**: validate-migration.sh validates markdown structure

#### Test Suite 5: Rollback
- [ ] **TC5.1**: Rollback from git tag works correctly
- [ ] **TC5.2**: Rollback from manual backup works correctly
- [ ] **TC5.3**: Rollback removes migration-state.json
- [ ] **TC5.4**: Rollback restores original directory structure

#### Test Suite 6: Dry-Run Mode
- [ ] **TC6.1**: --dry-run shows expected changes without applying them
- [ ] **TC6.2**: --dry-run doesn't modify any files
- [ ] **TC6.3**: --dry-run doesn't create backups

#### Test Suite 7: Idempotency
- [ ] **TC7.1**: Running migration twice skips already-migrated tasks
- [ ] **TC7.2**: Second migration reports "already migrated" for all tasks

#### Test Suite 8: Error Handling
- [ ] **TC8.1**: Clear error message when task not found
- [ ] **TC8.2**: Clear error message on file permission issues
- [ ] **TC8.3**: Error suggests rollback command with backup reference

### Regression Tests
- [ ] **RT1**: Existing v2.0 commands (cig-plan, cig-requirements, etc.) still work after migration
- [ ] **RT2**: hierarchy-resolver.sh resolves migrated tasks correctly
- [ ] **RT3**: format-detector.sh detects v2.0 format in migrated tasks
- [ ] **RT4**: status-aggregator.sh shows progress for migrated tasks

## Validation Criteria
- [ ] All 8 acceptance criteria from requirements met (AC1-AC8)
- [ ] All manual test suites pass (TC1.1-TC8.3)
- [ ] All regression tests pass (RT1-RT4)
- [ ] Script permissions set correctly (u+rx minimum)
- [ ] Scripts registered in script-hashes.json with SHA256 hashes
- [ ] Git history preserved (verified with git log --follow on migrated files)
- [ ] Documentation complete (README.md migration section, script help text)
- [ ] Dry-run mode tested and working
- [ ] Rollback tested and confirmed working
- [ ] Migration-state.json format validated
- [ ] Migration field format validated in all migrated files

## Status
**Status**: Finished
**Next Action**: N/A (Implementation phase completed)
**Blockers**: None

## Implementation Notes

### Key Design Patterns Used
1. **Pipeline Architecture**: Discrete stages (discovery → backup → migration → validation → reporting)
2. **Git-First Strategy**: Prefer git tag over manual backup (90% simpler)
3. **Idempotency**: Check Template Version field to skip already-migrated tasks
4. **Fail-Fast**: Stop on first error, don't continue corrupting data
5. **Dry-Run Pattern**: Preview changes before applying (--dry-run flag)

### Critical Implementation Details
- Use `git mv` for all file operations when git repo exists (preserves history)
- SHA256 hash all files before and after to detect content changes
- Insert Migration field with backup reference for traceability
- migration-state.json tracks progress and enables rollback
- Clear error messages include rollback command with backup reference

### Migration Field Format
After migration, all workflow files contain:
```markdown
- **Template Version**: 2.0
- **Migration**: v1.0 (git:migration-backup-20250113-143022) → v2.0
```

### v1.0 → v2.0 Path Transformation
```
Before: implementation-guide/feature/1-initial-implementation-guide/
After:  implementation-guide/1-feature-initial-implementation-guide/
```

### File Renaming Mapping
```
plan.md         → a-plan.md
requirements.md → b-requirements.md
design.md       → c-design.md
implementation.md → d-implementation.md
testing.md      → e-testing.md
rollout.md      → f-rollout.md (if exists)
maintenance.md  → g-maintenance.md (if exists)
```

## Extended Implementation: Status System Enhancement

### Overview
Implement configuration-driven status system to replace hardcoded status values in status-aggregator.sh with values loaded from cig-project.json.

### Implementation Tasks

#### Task 1: Update cig-project.json Configuration
**File**: `implementation-guide/cig-project.json`

**Changes**:
```json
{
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
}
```

**Implementation Steps**:
1. Read current cig-project.json
2. Replace `"status-values": [...]` array with object format
3. Map existing hardcoded values from status-aggregator.sh to config
4. Validate JSON syntax with `jq .`

**Verification**:
```bash
jq '.workflow["status-values"]' implementation-guide/cig-project.json
# Should output object with 6 status entries
```

#### Task 2: Update status-aggregator.sh Script
**File**: `.cig/scripts/command-helpers/status-aggregator.sh`

**Changes**:
1. Add PROJECT_ROOT detection
2. Add load_status_map() function
3. Call load_status_map() before using STATUS_MAP
4. Preserve hardcoded defaults as fallback
5. Add unknown status warning to extract_status() function

**Implementation**:
```bash
# At top of script (after set -euo pipefail)
PROJECT_ROOT="$(cd "$(dirname "${BASH_SOURCE[0]}")/../.." && pwd)"

# Replace hardcoded STATUS_MAP with:
declare -A STATUS_MAP

# Function to load status values from config
load_status_map() {
    local config_file=""

    # Find cig-project.json
    for path in \
        "$PROJECT_ROOT/cig-project.json" \
        "$PROJECT_ROOT/implementation-guide/cig-project.json" \
        "$PROJECT_ROOT/.cig/cig-project.json"; do
        if [[ -f "$path" ]]; then
            config_file="$path"
            break
        fi
    done

    # If not found, use defaults
    if [[ -z "$config_file" ]]; then
        # Fallback to hardcoded values
        STATUS_MAP=(
            ["Backlog"]="0"
            ["To-Do"]="0"
            ["In Progress"]="25"
            ["Implemented"]="50"
            ["Testing"]="75"
            ["Finished"]="100"
        )
        return 0
    fi

    # Load from config using jq
    if command -v jq &> /dev/null; then
        local loaded=false
        while IFS='=' read -r key value; do
            STATUS_MAP["$key"]="$value"
            loaded=true
        done < <(jq -r '.workflow["status-values"] | to_entries[] | "\(.key)=\(.value)"' "$config_file" 2>/dev/null || echo "")

        # If nothing loaded, use defaults
        if [[ "$loaded" == "false" ]] || [[ ${#STATUS_MAP[@]} -eq 0 ]]; then
            STATUS_MAP=(
                ["Backlog"]="0"
                ["To-Do"]="0"
                ["In Progress"]="25"
                ["Implemented"]="50"
                ["Testing"]="75"
                ["Finished"]="100"
            )
        fi
    else
        # jq not available, use defaults
        STATUS_MAP=(
            ["Backlog"]="0"
            ["To-Do"]="0"
            ["In Progress"]="25"
            ["Implemented"]="50"
            ["Testing"]="75"
            ["Finished"]="100"
        )
    fi
}

# Call at script initialization
load_status_map

# Enhanced extract_status() with unknown status warning
extract_status() {
    local file="$1"
    local status=""

    if [[ ! -f "$file" ]]; then
        echo "Unknown"
        return
    fi

    # Try pattern 1: ## Status: <status>
    status=$(grep -m 1 -i '^## Status:' "$file" 2>/dev/null | sed -E 's/^## Status: *//i' | tr -d '\r\n' || echo "")

    # Try pattern 2: **Status**: <status>
    if [[ -z "$status" ]]; then
        status=$(grep -m 1 -i '\*\*Status\*\*:' "$file" 2>/dev/null | sed -E 's/.*\*\*Status\*\*: *//i' | tr -d '\r\n' || echo "")
    fi

    # Default to Unknown if not found
    if [[ -z "$status" ]]; then
        echo "Unknown"
        return
    fi

    # Warn if unknown status (not in STATUS_MAP)
    if [[ -z "${STATUS_MAP[$status]:-}" ]]; then
        local filename=$(basename "$(dirname "$file")")/$(basename "$file")
        echo "Warning: Unknown status \"$status\" in $filename (defaulting to 0%)" >&2
    fi

    echo "$status"
}
```

**Verification**:
```bash
# Test status loading
.cig/scripts/command-helpers/status-aggregator.sh

# Should show tasks with progress calculated from config values
# Should warn to stderr if unknown statuses encountered
```

#### Task 3: Update Template Files
**Files**: All templates in `.cig/templates/`

**Audit Results**:
```bash
# Find all Status fields in templates
grep -r "^\*\*Status\*\*:" .cig/templates/
```

**Changes Required**:
- Replace invalid status values with valid ones from config
- Standardize on "Backlog" for initial state
- Ensure consistency across all task types

**Template Pattern**:
```markdown
## Status
**Status**: Backlog
**Next Action**: [Specific next action]
**Blockers**: None identified
```

**Verification**:
```bash
# All templates should use valid status values
for template in .cig/templates/**/*.md.template; do
  status=$(grep "^\*\*Status\*\*:" "$template" | sed 's/.*: //')
  if ! jq -e ".workflow[\"status-values\"] | has(\"$status\")" implementation-guide/cig-project.json > /dev/null 2>&1; then
    echo "Invalid status in $template: $status"
  fi
done
```

#### Task 4: Add Status Documentation to workflow-steps.md
**File**: `.cig/docs/workflow/workflow-steps.md`

**Addition**: Add "Status Values" section with jq commands and valid status list

**Implementation**:
```markdown
## Status Values

When updating the **Status** field in workflow files, use ONLY valid status values from `cig-project.json`.

### Query Valid Statuses

List all valid status values:
```bash
jq -r '.workflow["status-values"] | to_entries[] | "\(.key): \(.value)%"' \
  implementation-guide/cig-project.json
```

Get percentage for specific status:
```bash
jq -r '.workflow["status-values"]["In Progress"]' \
  implementation-guide/cig-project.json
```

### Valid Status Values
- **Backlog**: 0% - Task not started
- **To-Do**: 0% - Task queued
- **In Progress**: 25% - Work underway
- **Implemented**: 50% - Code complete, not tested
- **Testing**: 75% - Testing in progress
- **Finished**: 100% - Fully complete

**IMPORTANT**: Do not use arbitrary status values. Always select from this list.
```

**Verification**:
```bash
# Test jq commands work
jq -r '.workflow["status-values"] | to_entries[] | "\(.key): \(.value)%"' \
  implementation-guide/cig-project.json
```

#### Task 5: Update Workflow Commands with Status Instructions
**Files**: `.claude/commands/cig-*.md` (all workflow commands)

**Addition**: Add status field instruction to each command

**Template to add**:
```markdown
**Status Field**: When updating status, use only valid values from the project configuration:
```bash
jq -r '.workflow["status-values"] | keys[]' implementation-guide/cig-project.json
```
See `.cig/docs/workflow/workflow-steps.md#status-values` for details.
```

**Files to update**:
- `.claude/commands/cig-plan.md`
- `.claude/commands/cig-requirements.md`
- `.claude/commands/cig-design.md`
- `.claude/commands/cig-implementation.md`
- `.claude/commands/cig-testing.md`
- `.claude/commands/cig-rollout.md`
- `.claude/commands/cig-maintenance.md`
- `.claude/commands/cig-retrospective.md`

**Verification**:
```bash
# Check all commands have status instruction
grep -l "status-values" .claude/commands/cig-*.md | wc -l
# Should return 8 (one for each workflow command)
```

#### Task 6: Update SHA256 Hash
**File**: `.cig/security/script-hashes.json`

**Implementation**:
```bash
# Calculate new hash
sha256sum .cig/scripts/command-helpers/status-aggregator.sh | cut -d' ' -f1

# Update in script-hashes.json
```

### Testing Plan for Status System

#### Unit Tests
1. **Config Loading**: Verify load_status_map() correctly parses JSON
2. **Fallback Behavior**: Test with missing config file, invalid JSON, missing jq
3. **Status Lookup**: Verify unknown statuses default to 0%

#### Integration Tests
1. **Status Aggregation**: Run on all tasks, verify percentages match config
2. **Template Validation**: Verify all templates use valid status values
3. **Backward Compatibility**: Test with old array format (should use defaults)

#### Test Commands
```bash
# Test status aggregator with new config
.cig/scripts/command-helpers/status-aggregator.sh

# Test specific task
.cig/scripts/command-helpers/status-aggregator.sh 4

# Verify config format
jq '.workflow["status-values"]' implementation-guide/cig-project.json
```

### Rollback Strategy
If status system changes cause issues:
1. Revert cig-project.json to array format
2. status-aggregator.sh will use hardcoded defaults (fallback)
3. No data loss - only affects progress calculation

### Migration Checklist
- [ ] Task 1: Update cig-project.json with status-values object
- [ ] Task 2: Modify status-aggregator.sh to load from config and warn on unknown statuses
- [ ] Task 3: Update all template files to use valid statuses
- [ ] Task 4: Add status documentation to workflow-steps.md with jq commands
- [ ] Task 5: Update all workflow commands with status field instructions
- [ ] Task 6: Update SHA256 hash for status-aggregator.sh
- [ ] Test status loading with valid config
- [ ] Test unknown status warning to stderr
- [ ] Test fallback with missing/invalid config
- [ ] Verify all templates pass validation
- [ ] Verify workflow commands include status instructions

## Actual Results
*To be filled upon completion*

## Lessons Learned
*To be captured during implementation*
