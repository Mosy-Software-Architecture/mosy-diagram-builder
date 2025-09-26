# Flow Specifications - MOSY Diagram Builder

## System Overview

The MOSY Diagram Builder is a web application that allows users to create system model specs using the MOSY framework. Users can create flows, define actors, add actions in sequence, and visualize them as swimlane diagrams with automatic arrow connections. The system supports multiple flows as workspaces with persistent state.

## Flow 1: Create Flow

**Description**: User creates a new flow (workspace) to start building a MOSY diagram.

| Aspect | Step 1 | Step 2 |
|--------|--------|--------|
| **Actor** | UserActor | SystemActor |
| **Action** | CreateFlowAction | StoreFlowAction |
| **Entities** | Flow [write] | Flow [write] |
| **Metrics** | - | flowsCreated |
| **Technical components and APIs** | FlowPanel, CreateFlowForm, FlowService (frontend), POST /api/flows | FlowController, StoreFlowAction, FlowRepository |
| **Triggered by** | User interaction (form submit) | API call from frontend |

### Flow Details

**Step 1: CreateFlowAction**
- User opens the Flow panel in the left sidebar
- User enters a name for the new flow
- CreateFlowForm validates that name is not empty
- FlowService sends POST request to `/api/flows` with flow name

**Step 2: StoreFlowAction**
- FlowController receives the flow creation request
- StoreFlowAction validates name (required, max 100 characters)
- Creates Flow entity with:
  - Auto-generated ID
  - Provided name
  - Reference to projectId
  - Current timestamp as createdAt/updatedAt
- FlowRepository persists flow to database
- Returns created flow with 201 Created status
- Frontend updates flow list with new flow

## Flow 2: Select Flow

**Description**: User switches between different flows to work on different diagrams.

| Aspect | Step 1 |
|--------|--------|
| **Actor** | UserActor |
| **Action** | SelectFlowAction |
| **Entities** | Flow [read], Action [read], Actor [read] |
| **Metrics** | flowSwitches |
| **Technical components and APIs** | FlowSelector, FlowList, DiagramStore (frontend state), GET /api/flows/{id} |
| **Triggered by** | User interaction (flow selection) |

### Flow Details

**Step 1: SelectFlowAction**
- User clicks on a flow name in the Flow panel
- FlowList component triggers flow selection
- DiagramStore saves current flow state
- DiagramStore loads selected flow data
- ReactFlow diagram updates with new flow's actors and actions
- UI panels refresh with selected flow's data

## Flow 3: Add Actor

**Description**: User adds a new actor to the current flow, which creates a new swimlane in the diagram.

| Aspect | Step 1 | Step 2 |
|--------|--------|--------|
| **Actor** | UserActor | SystemActor |
| **Action** | AddActorAction | StoreActorAction |
| **Entities** | Actor [write] | Actor [write], Project [read] |
| **Metrics** | - | actorsCreated |
| **Technical components and APIs** | ActorPanel, CreateActorForm, ActorService (frontend), POST /api/projects/{id}/actors | ActorController, StoreActorAction, ActorRepository |
| **Triggered by** | User interaction (form submit) | API call from frontend |

### Flow Details

**Step 1: AddActorAction**
- User opens the Actor panel in the left sidebar
- User enters a name for the new actor
- CreateActorForm validates that name is not empty and unique
- ActorService sends POST request to `/api/projects/{id}/actors` with actor data

**Step 2: StoreActorAction**
- ActorController receives the actor creation request
- StoreActorAction validates actor name uniqueness within project
- Creates Actor entity with:
  - Auto-generated ID
  - Provided name
  - Reference to projectId
  - Optional color for swimlane
  - Swimlane position
- ActorRepository persists actor to database
- Returns created actor with 201 Created status
- Frontend updates actor list and refreshes diagram swimlanes

## Flow 4: Create Action

**Description**: User creates a new action belonging to a specific actor, which appears as a node in the diagram.

| Aspect | Step 1 | Step 2 |
|--------|--------|--------|
| **Actor** | UserActor | SystemActor |
| **Action** | CreateActionAction | StoreActionAction |
| **Entities** | Action [write] | Action [write], Flow [read], Actor [read] |
| **Metrics** | - | actionsCreated |
| **Technical components and APIs** | ActionPanel, CreateActionForm, ActionService (frontend), POST /api/flows/{id}/actions | ActionController, StoreActionAction, ActionRepository |
| **Triggered by** | User interaction (form submit) | API call from frontend |

### Flow Details

**Step 1: CreateActionAction**
- User opens the Action panel in the right sidebar
- User selects an actor from dropdown
- User enters a name for the new action
- CreateActionForm validates inputs
- ActionService sends POST request to `/api/flows/{id}/actions` with action data

**Step 2: StoreActionAction**
- ActionController receives the action creation request
- StoreActionAction validates action name and actor existence
- Creates Action entity with:
  - Auto-generated ID
  - Provided name
  - Reference to flowId
  - Reference to actorId (executedBy)
  - Calculated sequence position
- ActionRepository persists action to database
- Returns created action with 201 Created status
- Frontend receives response and:
  - Updates action list in right panel
  - Calculates node position based on actor swimlane and sequence
  - Creates React Flow node for the action
  - Creates edge to connect with previous action (if any)
  - Re-renders diagram with new action

## Flow 5: Reorder Actions

**Description**: User reorders actions within an actor's sequence using drag and drop.

| Aspect | Step 1 | Step 2 |
|--------|--------|--------|
| **Actor** | UserActor | SystemActor |
| **Action** | DragDropAction | ReorderActionsAction |
| **Entities** | Action [read] | Action [write] |
| **Metrics** | actionReorders | - |
| **Technical components and APIs** | ActionList (sortable), DndKit, ActionService (frontend), PUT /api/flows/{id}/actions/reorder | ActionController, ReorderActionsAction, ActionRepository |
| **Triggered by** | User interaction (drag and drop) | API call from frontend |

### Flow Details

**Step 1: DragDropAction**
- User drags an action in the ActionList (right panel)
- DndKit handles drag and drop interaction
- User drops action in new position
- ActionService sends PUT request to `/api/flows/{id}/actions/reorder` with new order

**Step 2: ReorderActionsAction**
- ActionController receives the reorder request
- ReorderActionsAction updates sequence position values for affected actions
- ActionRepository persists updated actions
- Returns updated action list with 200 OK status
- Frontend receives response and:
  - Updates action positions in the list
  - Recalculates node positions in diagram
  - Removes existing edges for reordered actions
  - Creates new edges based on new sequence
  - Re-renders React Flow with updated layout

## Flow 6: Export Diagram

**Description**: User exports the current diagram as code or image.

| Aspect | Step 1 | Step 2 |
|--------|--------|--------|
| **Actor** | UserActor | SystemActor |
| **Action** | RequestExportAction | GenerateExportAction |
| **Entities** | Flow [read], Actor [read], Action [read] | Flow [read], Actor [read], Action [read], Export [write] |
| **Metrics** | exportRequests | exportsGenerated |
| **Technical components and APIs** | ExportButton, DiagramExporter (frontend), GET /api/flows/{id}/export | ExportController, GenerateExportAction, ExportService |
| **Triggered by** | User interaction (export button) | API call from frontend |

### Flow Details

**Step 1: RequestExportAction**
- User clicks Export button in the toolbar
- User selects export format (JSON, Mermaid, PNG)
- DiagramExporter prepares export request
- Sends GET request to `/api/flows/{id}/export?format={format}`

**Step 2: GenerateExportAction**
- ExportController receives the export request
- GenerateExportAction loads complete flow data with actors and actions
- Transforms data based on requested format:
  - JSON: Serializes flow structure
  - Mermaid: Generates Mermaid diagram syntax
  - PNG: Frontend renders diagram to image
- Creates Export entity for audit trail
- Returns export data with appropriate content-type
- Frontend downloads or displays the exported content

## Flow 7: Import Diagram

**Description**: User imports a previously exported diagram definition.

| Aspect | Step 1 | Step 2 | Step 3 |
|--------|--------|--------|--------|
| **Actor** | UserActor | SystemActor | SystemActor |
| **Action** | UploadImportAction | ParseImportAction | CreateFromImportAction |
| **Entities** | - | - | Flow [write], Action [write], Import [write] |
| **Metrics** | importAttempts | - | importsSuccessful |
| **Technical components and APIs** | ImportButton, FileUploader (frontend), POST /api/flows/import | ImportController, ParseImportAction | CreateFromImportAction, FlowRepository, ActionRepository |
| **Triggered by** | User interaction (file upload) | API call from frontend | Parse success |

### Flow Details

**Step 1: UploadImportAction**
- User clicks Import button in the toolbar
- User selects file to import (JSON or Mermaid format)
- FileUploader reads file content
- Sends POST request to `/api/flows/import` with file data

**Step 2: ParseImportAction**
- ImportController receives the import request
- ParseImportAction validates file format
- Parses content based on detected format:
  - JSON: Deserializes structure
  - Mermaid: Parses diagram syntax
- Validates parsed data structure
- Returns parsed flow definition

**Step 3: CreateFromImportAction**
- CreateFromImportAction receives parsed data
- Creates new Flow entity with imported data
- Creates all Action entities maintaining sequence
- Links actions to existing actors (actors are project-level)
- Creates Import entity for audit trail
- FlowRepository and ActionRepository persist data
- Returns created flow with 201 Created status
- Frontend navigates to new flow and renders diagram

## Metrics Summary

- **flowsCreated**: Number of new flows created
- **flowSwitches**: Number of times users switch between flows
- **actorsCreated**: Number of actors added to flows
- **actionsCreated**: Number of actions created
- **actionReorders**: Number of times actions are reordered
- **exportRequests**: Number of export requests initiated
- **exportsGenerated**: Number of successful exports
- **importAttempts**: Number of import attempts
- **importsSuccessful**: Number of successful imports

---

© 2025 Mosy Software Architecture SL. All rights reserved.