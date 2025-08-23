# Code Implementation Guide (CIG)

A structured system for managing software development tasks through Claude Code slash commands.

## Overview

The Code Implementation Guide (CIG) provides a standardised approach to planning, implementing, and tracking software development tasks. Currently targeted for Claude Code integration through slash commands, it offers automated task creation, progress tracking, and documentation generation.

While this system is designed specifically for Claude Code, the methodology isn't strictly tied to any particular tool. Pull requests to support other development environments are welcome.

## Features

- **Task Management**: Structured approach to feature, bugfix, hotfix, and chore tasks
- **Hierarchical Organisation**: Multi-level task breakdown with automatic numbering
- **Template System**: Consistent documentation templates for all task types
- **Progress Tracking**: Real-time status monitoring across project hierarchy
- **Section Extraction**: Targeted retrieval of specific documentation sections
- **Retrospective Analysis**: Post-completion variance tracking and lessons learned

## Installation

1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd code-implementation-guide
   ```

2. Initialise CIG for your project:
   ```
   /cig-init
   ```

## Commands

### Core Commands

- `/cig-init` - Initialise CIG system with project configuration
- `/cig-new-task <type> [task-id] <description>` - Create categorised implementation guide
- `/cig-status [path]` - Show progress across implementation guide hierarchy
- `/cig-subtask <parent-task-name> <subtask-name>` - Create sub-implementation task

### Utility Commands

- `/cig-extract <file-path> <section-name>` - Extract specific section from implementation guide
- `/cig-retrospective <task-path>` - Facilitate post-completion analysis
- `/cig-config [init|list|reset]` - Configure CIG system paths and settings

## Task Types

### Feature Tasks
Complete feature development lifecycle with 6 phases:
- Plan, Requirements, Design, Testing, Rollout, Maintenance

### Bugfix Tasks  
Streamlined bug resolution with 4 phases:
- Plan, Implementation, Testing, Rollout

### Hotfix Tasks
Rapid critical issue resolution with 3 phases:
- Plan, Implementation, Rollout

### Chore Tasks
Maintenance and operational tasks with 4 phases:
- Plan, Implementation, Validation, Maintenance

## Project Structure

```
implementation-guide/
├── feature/
│   ├── 1-task-name/
│   │   ├── plan.md
│   │   ├── requirements.md
│   │   └── ...
│   └── 2-another-task/
├── bugfix/
├── hotfix/
└── chore/

.cig/
├── autoload.yaml
├── utils/
│   ├── config-loader.md
│   ├── template-engine.md
│   └── ...
└── templates/
    ├── feature/
    ├── bugfix/
    ├── hotfix/
    └── chore/
```

## Configuration

The system uses hierarchical configuration:

1. **Global**: `~/.cig/autoload.yaml`
2. **Project**: `.cig/autoload.yaml` 
3. **Implementation Guide**: `implementation-guide/cig-project.json`

Example `cig-project.json`:
```json
{
  "name": "My Project",
  "taskManagement": {
    "system": "github",
    "taskIdPattern": "^[A-Z]+-\\d+$"
  },
  "git": {
    "defaultBranch": "main",
    "branchPrefix": "feature/"
  }
}
```

## Hierarchical Numbering

Tasks use hierarchical numbering that syncs with filesystem structure:

- **Level 1**: 1, 2, 3 (main tasks)
- **Level 2**: 1.1, 1.2, 1.3 (subtasks)  
- **Level 3**: 1.1.1, 1.1.2, 1.1.3 (micro-tasks)

Directory structure mirrors numbering exactly.

## Version Information

- **CIG Project Schema**: v1.0.0
- **Git Version**: v0.1.1

## Contributing

1. Create a feature branch following the CIG methodology
2. Use `/cig-new-task feature` to structure your work
3. Ensure hierarchical numbering matches directory structure
4. Test all commands before submission

## License

This project is licensed under the GNU General Public License v2.0 (GPL-2.0). See [LICENSE.md](LICENSE.md) for the full license text.

Commercial distribution licenses are available for organisations that wish to distribute this software without being bound by the GPL-2.0 terms. See [COMMERCIAL-LICENSE.md](COMMERCIAL-LICENSE.md) for details on enquiries.

## Trademark Notice

Claude Code is a trademark of Anthropic. This repository is not affiliated with Anthropic or Claude Code. The mention of Claude Code in this repository is not indicative of support or endorsement by Anthropic.

## Support

For issues and feature requests, please use the project's issue tracking system as configured in `cig-project.json`.