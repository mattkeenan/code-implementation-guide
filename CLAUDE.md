# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Status

The Code Implementation Guide (CIG) system is **implemented and operational**. Core functionality includes structured task management, hierarchical documentation, and security-verified helper scripts.

## Development Commands

### CIG System Commands
- **Build**: Not applicable (documentation system)
- **Test**: Manual validation through command execution
- **Lint**: File integrity via `/cig-security-check`

### Available CIG Commands  
- `/cig-init` - Initialize CIG system
- `/cig-new-task` - Create structured implementation guides
- `/cig-status` - Show project progress
- `/cig-security-check` - Verify system integrity
- `/cig-extract`, `/cig-retrospective`, `/cig-config` - Utility commands

## Architecture Overview

**Script-Based Helper System**: Compound bash operations encapsulated in security-verified scripts with git-based versioning.

**Implementation Guide Structure**: Hierarchical task organization with standardized templates and progress tracking.

**Security Model**: 0500 permissions, SHA256 verification, specific script paths instead of wildcards.

## System Integration

- **Helper Scripts**: `.cig/scripts/command-helpers/` with self-documenting names
- **Configuration**: Hierarchical config system with `cig-project.json`
- **Version Tracking**: Git-based versioning (`v0.1.1-5-gcea1c19` format)
- **Task Management**: Support for GitHub/GitLab/JIRA with internal fallback