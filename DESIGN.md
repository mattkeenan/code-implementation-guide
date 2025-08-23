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
<project>/im-guides/
├── README.md                    # Navigation and index
├── 1-major-feature/
│   ├── plan.md                 # High-level planning
│   ├── requirements.md         # Functionality and feasibility
│   ├── design.md              # Architecture decisions
│   ├── testing.md             # Test strategy
│   ├── roll-out.md            # Deployment approach
│   ├── maintenance.md         # Post-deployment concerns
│   ├── status.md              # Progress tracking
│   ├── 1.1-subfeature/
│   │   ├── plan.md
│   │   ├── requirements.md
│   │   ├── implementation.md   # Concrete steps
│   │   ├── testing.md
│   │   └── roll-out.md
│   └── 1.2-another-subfeature/
└── 2-next-major-feature/
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
- `## Actual Results` - Post-completion actuals  
- `## Lessons Learned` - Key insights and variances
- `## Goal` - Single sentence objective
- `## Success Criteria` - Measurable outcomes
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

### Optimized for Core Tools
- **Glob**: Find documents by type (`**/plan.md`, `**/implementation.md`)
- **Grep**: Search within specific scopes and extract sections
- **Read**: Navigate hierarchy naturally, load selectively

### Context Management
- Read only relevant documents for current task phase
- Extract specific sections rather than full documents
- Hierarchical structure enables focused work without information overload

## Workflow Integration

### Project Setup
1. Create directory structure with numbered hierarchy
2. Generate template documents with standard sections
3. Update project CLAUDE.md with extraction commands
4. Initialize status tracking

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
- Directory structure optimized for Glob/Grep patterns
- File naming conventions support natural Claude Code navigation

## Implementation Notes

### Getting Started
Use Claude Code commands to:
- `/implementation-guide-setup` - Initialize structure and templates
- `/guide-step <parent> <name>` - Add new feature/sub-feature
- `/guide-status` - View progress across all guides

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