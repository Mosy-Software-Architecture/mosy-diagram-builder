# Enhanced Task Template

This template defines the structure for all task files in the task-based development workflow. All agents must use this template and respect the phase restrictions.

## Template Structure

```markdown
# [Task Name]

**Status**: DRAFT (Reference: `agentguild://resources/client-instructions/task-based-development.md`)
**Type**: Improvement | Bugfix | Feature | Refactor | Documentation
**Date and time created**: [Use `date '+%Y-%m-%d %H:%M'` command - DO NOT guess dates]
**Date Completed**: TBD
**Related Specs**: [Links to relevant docs/system-specs files]

## Problem Statement
[ONLY what the user explicitly stated. Do NOT add assumptions or unstated requirements.]

### Non Functional Requirements
[Technical requirements that must be fulfilled too.]

### AI Agent Insights and Additions
[Additional insights or requirements identified by AI agent - NOT part of user's explicit requirements]

## System impact
[CAN rephrase user requirements using Mosy actors/actions for clarity, but NEVER add unstated requirements]
- **Actors involved**: [Rephrase user's mentioned actors using Mosy terminology]
- **Actions to implement**: [Rephrase user's requested actions using Actor→Action→Entity pattern]
- **Flows affected**: [Identify flows from user's requirements using Mosy flow patterns]
- **Entity changes needed**: [Yes/No - which entities will be affected]
- **Flow changes needed**: [Yes/No - which business flows will change]
- **Integration changes needed**: [Yes/No - which external systems affected]
- **New specifications required**: [List any new specs that need creation]

## Software Architecture
[New components or structures very high level, conceptually, from a systems architecture point of view. never add any specific code here. You must follow the structure below, including the sections that contain changes.]

### API

### Events

### Frontend

### Backend

### Data

### Other

## Technical Solution Overview
[High-level technical details, do not include all code here but only new files, modified ones, interfaces, most relevant logic, etc.]

## Log
[The task coordinator agent must add entries here for handoff and other important events. Other agents must also add entries for important decisions.]

## Acceptance Criteria

[Clear, measurable outcomes that define task completion. These should be specific and testable.]

- [ ] [Specific measurable outcome 1]
- [ ] [Specific measurable outcome 2]
- [ ] [Specific measurable outcome 3]
- [ ] All tests pass (unit, integration, end-to-end)
- [ ] Code review approved
- [ ] Performance benchmarks met
- [ ] Security review passed
- [ ] Documentation updated and current

---

© 2025 Mosy Software Architecture SL. All rights reserved.

Licensed to AgentGuild customers for internal use only. Distribution, copying, or derivative works prohibited without written permission. Contact: legal@mosy.tech