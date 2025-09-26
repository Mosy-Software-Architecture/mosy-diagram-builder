# Foundational System Specifications for MOSY Diagram Builder

**Status**: DRAFT (Reference: `agentguild://resources/client-instructions/task-based-development.md`)
**Type**: Documentation
**Date and time created**: 2025-09-26 07:08
**Date Completed**: TBD
**Related Specs**:
- `docs/system-specs/flow-specs.md`
- `docs/system-specs/entity-model.md`

## Problem Statement
Create comprehensive foundational system specifications for the MOSY diagram builder application - a web application for creating system model specifications using the MOSY framework.

### Core Functionality
- **Project Management**: Users organize their work within projects containing actors, components, entities, and flows
- **Visual Modeling**: Flows displayed as interactive swimlane diagrams with automatic arrow connections
- **CRUD Operations**: Full create, read, update, delete support for all model elements
- **Interface Layout**: Central diagram canvas with collapsible side panels for element management
- **Data Relationships**: Actions can create, update, or read entities through explicit relationships

### Non-Functional Requirements
- Must follow MOSY framework principles strictly
- React Flow for diagram visualization
- State persistence across flow switching
- Export/import functionality for portability
- Fully synchronous system (no event handling)
- Clear separation: Backend handles data, Frontend handles visualization

## System Impact
### Model Structure (Final State)
- **Project**: Top-level container for all elements
- **Actors**: Defined at project level, reusable across all flows
- **Components**: Defined at project level, implement actions and own entities
- **Entities**: Defined at project level, shared across flows with CRUD relationships to actions
- **Flows**: Belong to projects, contain sequences of actions
- **Actions**: Belong to flows, executed by actors, implemented by components

### Key Flows
- Project management (create, open, save)
- Flow creation and editing
- Actor and component definition
- Entity modeling with CRUD relationships
- Diagram visualization and interaction
- Export/import operations

## Software Architecture

### Frontend (Primary Focus)
**Component Structure:**
- **Layout**: CSS Grid with collapsible side panels (200-300px) and flexible center area
- **Left Panel**: Flow management, Actor/Component definition
- **Center Canvas**: React Flow diagram with swimlanes and action nodes
- **Right Panel**: Action creation and sequencing (sortable list)
- **Header**: Current flow indicator and project controls

**State Management:**
```typescript
interface AppState {
  project: Project
  currentFlowId: string | null
  panels: { left: PanelState, right: PanelState }
  diagramState: ReactFlowInstance
}
```

**Technology Stack:** React 18, React Flow 11+, Zustand, Framer Motion, DnD Kit, Tailwind CSS

### Backend (Data Layer)
- RESTful API for CRUD operations on all model elements
- Synchronous request-response pattern (no events)
- Controllers delegate to Actions (no Service layer)
- Repository pattern for data persistence

### Data Model (Final)
```
Project
├── Actors[] (project-level, reusable)
├── Components[] (project-level, implement actions)
├── Entities[] (project-level, shared across flows)
└── Flows[]
    └── Actions[]
        ├── executedBy → Actor
        ├── implementedBy → Component
        └── ActionEntityRelations[]
            ├── entity → Entity
            └── type: CREATES | UPDATES | READS
```

## Technical Solution Overview

### Key Design Decisions

**1. Hierarchical Data Model**
- Project as top-level container ensures all elements are properly scoped
- Actors, Components, and Entities at project level enables reusability across flows
- Actions belong to flows but reference project-level elements
- ActionEntityRelation junction table enables N:N CRUD relationships

**2. Frontend Architecture**
- React Flow for diagram rendering with custom nodes and edges
- Zustand for centralized state management
- CSS Grid layout with collapsible panels (48px collapsed, 200-300px expanded)
- Swimlane calculation based on actor assignments
- Automatic edge routing between sequential actions

**3. Synchronous System Design**
- No event handling or async messaging
- Frontend updates diagram after successful API responses
- Clear separation of concerns: Backend = data, Frontend = visualization
- All state changes through explicit API calls

**4. Performance Strategy**
- React Flow handles virtual rendering for large diagrams
- Memoization of computed positions and layouts
- Debounced drag and resize operations
- Lazy loading of flow data when switching

## Log
- 2025-09-26 07:08 - Task created by Taskie to document foundational system specifications
- 2025-09-26 07:08 - Rex's technical design captured: component hierarchy, state management, libraries
- 2025-09-26 07:08 - Archie revised entity model: Project parent, Actors at project level, Actions in Flows
- 2025-09-26 07:08 - User requested Entity support: Entities in Flows, related to Actions via CRUD operations
- 2025-09-26 07:15 - Added Entity and Component entities to model with full CRUD relationships and ownership patterns
- 2025-09-26 07:15 - Created ActionEntityRelation junction table for tracking creates/updates/reads relationships
- 2025-09-26 07:15 - Updated Action entity with implementedBy relationship to Component
- 2025-09-26 07:20 - CRITICAL CHANGE: Moved Entity from Flow level to Project level (sibling of Actor and Component)
- 2025-09-26 07:20 - Entities now shared across all flows in project, associations created at action level
- 2025-09-26 07:25 - Fixed ActionEntityRelation connections in ERD diagram
- 2025-09-26 07:30 - Removed all event handling from flows - system is fully synchronous
- 2025-09-26 07:30 - Clarified diagram updates happen in frontend after API responses
- 2025-09-26 07:30 - Removed technical backend steps like RecalculateEdgesAction

## Acceptance Criteria

- [x] Complete flow specifications created following MOSY patterns
- [x] Entity model defined with all relationships
- [x] Rex's technical design documented for preservation
- [x] Entity support added to model (created_by, updated_by, read_by relationships)
- [x] Component support added to model (implements actions, owns entities)
- [ ] Sequence diagrams created for each flow
- [ ] Architecture documentation completed
- [ ] All specifications follow MOSY framework templates
- [ ] Documentation ready for implementation phase

---

© 2025 Mosy Software Architecture SL. All rights reserved.

Licensed to AgentGuild customers for internal use only. Distribution, copying, or derivative works prohibited without written permission. Contact: legal@mosy.tech