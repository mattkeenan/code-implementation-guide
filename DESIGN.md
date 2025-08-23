# Implementation Guide System Design

## Original Estimate
- Timeline: 2 hours design + 1 hour documentation
- Resources: 1 person (analysis and design)
- Complexity: Medium (novel system design)

## Goal

Create a context-efficient hierarchical documentation system for Claude Code that maximises signal-to-noise ratio while enabling precise information extraction and change management tracking.

## Success Criteria
- [ ] Claude Code can extract specific sections using sed commands
- [ ] Document structure scales without context explosion
- [ ] Change management tracking captures estimate variance
- [ ] System works with Claude Code's natural tool patterns
- [ ] Templates reduce setup overhead for new projects

## System Overview

This system provides a standardised approach to documenting software implementation projects that works optimally with Claude Code's tool preferences and context limitations.

### Core Architecture

```
<project>/implementation-guide/
├── cig-project.json            # Code Implementation Guide Project configuration
├── README.md                   # Navigation and index
├── feature/
│   ├── 1-user-authentication/
│   │   ├── plan.md             # High-level planning
│   │   ├── requirements.md     # Functionality and feasibility
│   │   ├── design.md           # Architecture decisions
│   │   ├── testing.md          # Test strategy
│   │   ├── rollout.md          # Deployment approach
│   │   ├── maintenance.md      # Post-deployment concerns
│   │   ├── 1.1-user-model/
│   │   │   ├── implementation.md
│   │   │   └── testing.md
│   │   └── 1.2-auth-middleware/
│   └── 2-payment-system/
├── bugfix/
│   ├── 1-login-validation-error/
│   └── 2-memory-leak-fix/
├── hotfix/
│   ├── 1-security-patch/
└── chore/
    ├── 1-dependency-update/
    └── 2-ci-improvement/
```

### Information Architecture Principles

#### Single Responsibility
- Each file serves one specific purpose
- No duplicate information across files
- Clear separation of concerns (planning vs execution vs deployment)

#### Hierarchical Organization
- Numbered hierarchy encodes scope and relationships
- Filesystem structure mirrors task breakdown
- Natural navigation through related documents

#### Context Efficiency
- Predictable section structure enables precise extraction
- Standard naming conventions reduce cognitive overhead
- Focused scope per document minimises information overload

## Document Types and Purpose

### Core Planning Documents
- **plan.md**: Strategic overview, goals, major steps
- **requirements.md**: Functional specs, feasibility analysis
- **design.md**: Architecture decisions, interfaces, trade-offs
- **status.md**: Current progress, blockers, next actions

### Execution Documents
- **implementation.md**: Concrete steps, file changes, validation
- **testing.md**: Test strategy, cases, automation approach
- **roll-out.md**: Deployment steps, monitoring, rollback plans
- **maintenance.md**: Post-deployment considerations, support info

## Section Standardization

### Universal Tracking Sections
Available for any document:
- `## Original Estimate` - Initial planning estimates
- `## Task Reference` - Task tracking integration (Task ID, URL, Parent Task, Branch)
- `## Goal` - Single sentence objective
- `## Success Criteria` - Measurable outcomes
- `## Actual Results` - Post-completion actuals
- `## Lessons Learned` - Key insights and variances
- `## Current Status` - Progress tracking

### Content-Specific Sections
Use as needed per document:
- `## Major Steps` - High-level breakdown
- `## Dependencies` - External requirements
- `## Constraints` - Limitations and boundaries
- `## Key Decisions` - Important choices and rationale
- `## Approach` - How something will be done
- `## Validation` - How to verify success
- `## Documentation` - Links to related documents and references

### Section Extraction Commands

**Precise extraction**:
```bash
sed -n '/^## <section name>/,/^## /p' <file> | head -n -1
```

## Change Management Strategy

### Estimate Tracking
Each document captures both planning and actual results:
- Original estimates remain unchanged after work begins
- Actual results recorded during/after completion
- Lessons learned document key variances and insights

### Predictable Structure
- Standard section names enable consistent tooling
- Human-readable names with uppercase first letter for grep reliability

## Claude Code Tool Integration

### Optimised for Core Tools
- **Glob**: Find documents by type (`**/plan.md`, `**/implementation.md`)
- **Grep**: Search within specific scopes and extract sections
- **Read**: Navigate hierarchy naturally, load selectively

### Context Management
- Read only relevant documents for current task phase
- Extract specific sections rather than full documents
- Hierarchical structure enables focused work without information overload

## Workflow Integration

### Project Setup
1. Initialise with `/cig-init` command
2. Configure `cig-project.json` with task management and source control settings
3. Create categorised directory structure (feature/, bugfix/, hotfix/, chore/)
4. Update project CLAUDE.md with extraction commands and CIG system hints

### Development Process
1. **Planning Phase**: Focus on plan.md, requirements.md, design.md
2. **Implementation Phase**: Work with implementation.md, testing.md
3. **Deployment Phase**: Execute roll-out.md, update maintenance.md
4. **Retrospective**: Fill in actual results and lessons learned

### Progress Tracking
- status.md provides current state overview
- Section-level estimates enable granular variance analysis
- Hierarchical completion status bubbles up to parent features

## Benefits

### For Claude Code
- Predictable structure reduces context switching
- Tool-optimised organisation works with natural usage patterns
- Precise information extraction minimises noise
- Scalable to large projects without context explosion

### For Development Teams
- Clear project structure and progress visibility
- Historical variance data improves future estimation
- Standardised approach reduces cognitive overhead
- Human-readable documentation supports collaboration

### For Project Management
- Granular tracking of estimates vs actuals
- Clear audit trail of decisions and changes
- Structured retrospectives enable process improvement
- Hierarchical organisation supports reporting at multiple levels

## Key Decisions

### Document Structure
- Hierarchical numbering (1-feature/1.1-subfeature) for clear scope encoding
- Single responsibility per file to minimise context switching
- Predictable section names for reliable extraction

### Section Standardisation
- Human-readable names with uppercase first letter for grep compatibility
- Universal tracking sections (Original Estimate, Actual Results, Lessons Learned)
- Content-specific sections used only when they add value

### Tool Integration
- Sed extraction commands for precise section retrieval
- Directory structure optimised for Glob/Grep patterns
- File naming conventions support natural Claude Code navigation

## Implementation Notes

### Getting Started
Use Claude Code CIG commands:
- `/cig-init` - Initialise CIG system with project configuration
- `/cig-new-task <type> [task-id] <description>` - Create categorised implementation guides
- `/cig-status [path]` - Show progress across task categories
- `/cig-extract <file> <section>` - Extract specific document sections
- `/cig-substep <path> <name>` - Add sub-implementation tasks
- `/cig-retrospective <path>` - Post-completion analysis and variance tracking

### Best Practices
- Don't modify original estimates after work begins
- Fill in actual results promptly after completion
- Use only sections that add value to each document
- Maintain clear hierarchy numbering as project evolves
- Update CLAUDE.md with project-specific section extraction patterns

## Actual Results
[To be filled after implementation]

## Lessons Learned
[To be filled after implementation and usage]