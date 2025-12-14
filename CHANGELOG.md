# Changelog

All notable changes to the Code Implementation Guide (CIG) project are documented in this file, organized by task.

## Task 3: Hierarchical Workflow System with Dynamic Step Transitions

**Status**: Complete
**Impact**: Foundational change enabling infinite task nesting with 90% reduction in LLM context consumption

### Token-Efficient Context Inheritance

Reduces LLM context consumption by 90% through structural maps that enable progressive disclosure. Instead of reading full parent files (500-1000 tokens each), LLM receives navigable document structure with headers and line ranges (50-100 tokens), preserving agency to decide what details matter.

**Key Features**:
- Status markers prevent implementation confusion by indicating reliability of parent context
- Dual output formats (markdown/JSON) serve both human/LLM reasoning and programmatic automation
- Version checking ensures workflow files remain compatible with CIG software as system evolves
- Enables hierarchical task decomposition with infinite nesting while maintaining context efficiency
- LLM can understand parent task decisions without drowning in irrelevant details

### Core Infrastructure

Establishes foundation for infinite task nesting while maintaining LLM context efficiency through progressive disclosure.

**Central Template Pool**:
- Symlinks eliminate duplication across task types
- Single source of truth in `.cig/templates/pool/`
- Task-type-specific symlinks (feature: 8 files, bugfix: 5 files, hotfix: 5 files, chore: 4 files)

**Five Helper Scripts** (Automation Layer):
1. `hierarchy-resolver.sh` - Task path to directory resolution with metadata
2. `format-detector.sh` - Template version detection with upgrade suggestions
3. `status-aggregator.sh` - Progress calculation from status markers using defined formula
4. `template-version-parser.sh` - Standalone version field extraction
5. `context-inheritance.pl` - Parent context structural maps with headers and line ranges

**Eight Workflow Commands** (Complete Task Lifecycle):
- `/cig-plan` - Planning phase with decomposition signals
- `/cig-requirements` - Requirements gathering with acceptance criteria
- `/cig-design` - Architecture and design decisions
- `/cig-implementation` - Code changes and validation
- `/cig-testing` - Test strategy and execution
- `/cig-rollout` - Deployment strategy and monitoring
- `/cig-maintenance` - Ongoing support and optimization
- `/cig-retrospective` - Lessons learned and recommendations

**Design Principles**:
- Consistent 8-step pattern across all commands
- Each command references shared context documentation (DRY principle)
- Commands use helper scripts for deterministic operations
- Progressive disclosure pattern: Commands reference workflow step docs instead of duplicating content
- LLM receives structural information to make intelligent decisions

**Security Model**:
- SHA256 hashes stored for all helper scripts in `.cig/security/script-hashes.json`
- Permissions enforced (u+rx minimum, typically 0500)
- Git-based version tracking

### Documentation and User-Facing Guides

Finalizes v2.0 implementation with comprehensive workflow documentation and updated project guides. Users now have complete reference for 8-step workflow system, task decomposition principles, and migration from v1.0.

**Workflow Documentation** (3200 words total):
- Overview: 8-step hierarchical workflow and decomposition principles (400 words)
- Step-by-Step Guidance: Detailed focus/avoid patterns for each of 8 steps (2400 words)
- Decomposition Guide: 5 universal signals and hierarchical numbering explanation (400 words)

**README.md Updates** (v2.0 Features):
- Infinite task nesting with decimal numbering (1, 1.1, 1.1.1, ...)
- Token-efficient context inheritance (90% reduction in LLM context consumption)
- Progressive disclosure and central template pool
- All 8 workflow commands with examples
- Migration notes for breaking changes from v1.0

**CLAUDE.md Updates** (LLM Consumption):
- Concise architecture overview emphasizing token efficiency
- All 16 commands organized by category
- Progressive disclosure pattern explanation
- Security model and helper script descriptions

### Breaking Changes from v1.0

- **`/cig-new-task`**: New signature `<num> <type> "description"` for hierarchical numbering (was `<type> <num> <description>`)
- **`/cig-extract`**: Task-based paths instead of file paths (backward compatible during migration period)
- **`/cig-subtask`**: Context inheritance via helper scripts, not manual reading

### System Status

- Fully operational with complete documentation
- Users can leverage hierarchical workflows, context inheritance, and structured task progression
- 90% token reduction enables handling complex multi-level project structures
- Breaking changes clearly documented with migration path

---

## Previous Tasks

### Task 2: Script-Based CIG Command Helpers

Complete script-based CIG command helpers implementation with security fixes.

### Task 1: CIG Commands Implementation

Complete CIG commands implementation with official Anthropic patterns.

### Task 0: Initial System Design

- CIG project configuration system and unified task commands
- Comprehensive CIG command reference
- Initial implementation guide system design
