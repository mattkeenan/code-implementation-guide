# Implementation Guide Commands

## Goal

Provide comprehensive command reference for Claude Implementation Guide (CIG) system to enable efficient project documentation workflow.

## Setup Command

### `/cig-init`
**Purpose**: Initialise implementation guide system in current project

**Usage**: `/cig-init`

**Actions**:
- Creates `<git-root>/implementation-guides/` directory structure
- Generates README.md with navigation index
- Creates `.templates/` directory with template files
- Updates CLAUDE.md with section extraction hints and standard section names
- Provides command reference and next steps

**Output**: Confirmation message with available commands and setup completion status

## Core Document Creation Commands

### `/cig-plan <feature-name>`
**Purpose**: Create new major feature planning document

**Usage**: `/cig-plan auth-system`

**Creates**:
- Directory: `implementation-guides/N-auth-system/`
- File: `plan.md` with Goal, Success Criteria, Major Steps, Dependencies sections
- Template includes Original Estimate placeholder

**Example**: `/cig-plan payment-integration` creates `1-payment-integration/plan.md`

### `/cig-requirements <feature-path>`
**Purpose**: Create requirements specification document

**Usage**: `/cig-requirements 1-auth-system`

**Creates**: `requirements.md` with Functional Requirements, Technical Feasibility, Constraints sections

### `/cig-design <feature-path>`
**Purpose**: Create architecture and design document

**Usage**: `/cig-design 1-auth-system`

**Creates**: `design.md` with Key Decisions, Constraints, Approach sections

### `/cig-implementation <feature-path>`
**Purpose**: Create concrete implementation steps document

**Usage**: `/cig-implementation 1.1-user-model`

**Creates**: `implementation.md` with Files to Modify, Implementation Steps, Validation sections

### `/cig-testing <feature-path>`
**Purpose**: Create test strategy and test cases document

**Usage**: `/cig-testing 1-auth-system`

**Creates**: `testing.md` with Test Approach, Test Cases, Success Criteria sections

### `/cig-rollout <feature-path>`
**Purpose**: Create deployment and rollout strategy document

**Usage**: `/cig-rollout 1-auth-system`

**Creates**: `roll-out.md` with Deployment Strategy, Prerequisites, Rollback Plan sections

### `/cig-maintenance <feature-path>`
**Purpose**: Create ongoing maintenance and support document

**Usage**: `/cig-maintenance 1-auth-system`

**Creates**: `maintenance.md` with Monitoring Requirements, Common Issues, Performance Considerations sections

## Hierarchy Management Commands

### `/cig-substep <parent-path> <substep-name>`
**Purpose**: Create numbered subdirectory under existing feature

**Usage**: `/cig-substep 1-auth-system user-model`

**Creates**: 
- Directory: `1-auth-system/1.1-user-model/`
- Basic template files: `plan.md`, `requirements.md`, `implementation.md`

**Auto-numbering**: Automatically assigns next available number (1.1, 1.2, etc.)

## Utility Commands

### `/cig-status [feature-path]`
**Purpose**: Show completion status across implementation guide hierarchy

**Usage**: 
- `/cig-status` - Shows all guides
- `/cig-status 1-auth-system` - Shows specific feature status

**Output**: Progress summary with completion percentages, blockers, next actions

### `/cig-extract <file-path> <section-name>`
**Purpose**: Extract specific section from implementation guide document

**Usage**: `/cig-extract 1-auth-system/plan.md "Original Estimate"`

**Method**: Uses `sed -n '/^## <section-name>/,/^## /p' <file> | head -n -1`

**Common Sections**:
- "Original Estimate" - Initial planning estimates
- "Actual Results" - Post-completion actuals
- "Lessons Learned" - Key insights and variances
- "Success Criteria" - Measurable outcomes
- "Documentation" - Links to related documents and references

### `/cig-retrospective <feature-path>`
**Purpose**: Facilitate post-completion analysis and variance tracking

**Usage**: `/cig-retrospective 1-auth-system`

**Actions**:
- Prompts for Actual Results in all feature documents
- Captures variance between Original Estimate and Actual Results
- Facilitates Lessons Learned documentation
- Updates status.md with completion markers

## Workflow Integration

### Typical Command Progression

1. **Project Setup**: `/cig-init`
2. **Feature Planning**: `/cig-plan major-feature`
3. **Requirements Analysis**: `/cig-requirements 1-major-feature`
4. **Architecture Design**: `/cig-design 1-major-feature`
5. **Break Down**: `/cig-substep 1-major-feature first-component`
6. **Implementation**: `/cig-implementation 1.1-first-component`
7. **Testing**: `/cig-testing 1.1-first-component`
8. **Deployment**: `/cig-rollout 1-major-feature`
9. **Post-Launch**: `/cig-maintenance 1-major-feature`
10. **Analysis**: `/cig-retrospective 1-major-feature`

### Status Monitoring

- Use `/cig-status` regularly to track progress
- Use `/cig-extract` to review specific sections without loading full documents
- Update Original Estimate → Actual Results progression through implementation

## Integration with CLAUDE.md

Commands automatically update project CLAUDE.md with:
- Section extraction patterns
- Standard section names
- Directory structure navigation hints
- Command usage examples

This ensures future Claude Code instances can work efficiently with the generated documentation structure.

## Key Decisions

### Command Design Principles
- **Namespace Safety**: `cig-` prefix prevents command collisions
- **Filename Matching**: Command names correspond to generated files
- **Hierarchical Support**: Commands support nested feature breakdown
- **Template Consistency**: All commands use standardised section templates

### Directory Structure
- Numbered hierarchy encoding (1-feature/1.1-subfeature)
- Single file per concern (planning vs execution vs deployment)
- Predictable patterns for tool integration

### Section Standardisation
- Universal tracking sections across all documents
- Human-readable section names with reliable extraction patterns
- Consistent template structure for rapid navigation

