## AgentGuild setup

This project uses AgentGuild agents for specialized development tasks.

### Resource Loading and Caching Protocol

**MANDATORY**: All agents MUST follow this strict resource loading protocol:

1. **Check Local Cache First**
   - Always check `agentguild/cache/[resource-path]` before attempting MCP fetch
   - Example: For `agentguild://resources/ai-kb/mosy/spec-templates/flow-specs.md`
   - Check: `agentguild/cache/resources/ai-kb/mosy/spec-templates/flow-specs.md`

2. **Fetch from MCP if Not Cached**
   - If resource not found in cache, attempt to fetch from MCP server
   - On successful fetch, save to cache for future use
   - Cache path mirrors the URI path after `agentguild://`

3. **CRITICAL FAIL-SAFE BEHAVIOR**
   - **NEVER create templates from memory or improvise resources**
   - If resource cannot be loaded (cache miss AND MCP fetch fails):
     - STOP immediately
     - Inform user: "Unable to load required resource [resource-name]. Please check MCP server connection."
     - Suggest: "Try reconnecting the AgentGuild MCP server or verify it's running."
   - **DO NOT proceed without required resources**

4. **Resource Freshness**
   - Use cached resources for the entire session
   - Only refresh from MCP if:
     - User explicitly requests refresh
     - Version mismatch detected
     - Cache corruption suspected

### Cache Structure

The `agentguild/cache/` directory mirrors the MCP resource structure after the `agentguild://` prefix:
- Resources: `agentguild/cache/resources/[path]`
- Templates: `agentguild/cache/templates/[path]` (if any)
- Agent instructions: Platform-specific directory (e.g., `.claude/agents/`)

**Note**: The MCP server name varies by installation - check your MCP connections to identify the AgentGuild server. Use appropriate MCP tools (e.g., `ReadMcpResourceTool`) when fetching.

### Agent Delegation Rules

**MANDATORY**: All prompts MUST be handled by appropriate sub-agents when available:
- Use specialized agents for their expertise domains
- Parent agent is ONLY for coordination or when no suitable sub-agent exists
- Always identify which agent should handle the prompt before processing

**CRITICAL**: The first time a sub-agent is called in a session, it MUST read its instructions from the local agent directory (e.g., `.claude/agents/` for Claude Code).

### Available Agents

**Essential Agents:**
- `task-coordinator` (Taskie) - Task management and workflow orchestration
- `mosy-system-architect` (Archie) - System design and MOSY framework specifications

**Quality Assurance:**
- `code-consistency-reviewer` (Revi) - Code quality and consistency validation

**Development Agents:**
- `tdd-react-typescript-engineer` (Rex) - Frontend React/TypeScript development
- `devops-integration-engineer` (Debbie) - Infrastructure, Docker, CI/CD setup

### Task-Based Development Workflow

**ALWAYS follow task-based development workflow for any implementation changes:**
- Create task files in `docs/tasks/` folder before any implementation work
- Use git branching and commit conventions specified in task-based development instructions
- Maintain synchronization between `docs/system-specs/` and `docs/tasks/` specifications

### Git Workflow Requirements

**NEVER add AI agent credits, "Co-Authored-By", or "Generated with" messages to commits**
- NEVER include links to AI platforms or tools in commit messages
- Keep commit messages focused on the actual changes made
- Use clean, professional commit messages without self-promotional content

### Git Branch Management - TASK-COORDINATOR ONLY

**Git branch operations are EXCLUSIVELY managed by task-coordinator agent (Taskie)**
- Other agents MUST NOT create, merge, or delete branches
- All agents MUST use task name prefix in commits when working within task branches

### Git Commit Review Requirements

**MANDATORY**: Always ask for user review before committing any changes
- Required for ALL commits: Specifications, implementation code, documentation, configuration
- Present changes summary, wait for user approval, then commit
- Never commit without approval - no exceptions
- **Format**: "Ready to commit [change-summary]. Proceed with commit? (y/n)"

### Agent Notes System

**File**: `agentguild/agent-notes.md` - Asynchronous communication between user and agents
- MUST check at the end of each interaction flow
- MUST process notes marked as complete (has another note below OR ends with a dot)
- MUST move processed notes to "Processed notes" section with format: `[YYYY-MM-DD HH:MM] [agent-name]: original note`
- MUST ignore incomplete notes (last note without dot)
- MUST remove processed notes from "Pending notes" section

### CRITICAL: Task Phase Enforcement Rules

- MUST respect task status and phase restrictions strictly
- NEVER suggest code implementations when task status is DRAFT
- ALWAYS check current task status before taking any action
- USE extended thinking mode during DRAFT phase for solution planning
- REQUIRE explicit user approval for DRAFT → IMPLEMENTATION transition only

### Date and Time Requirements - MANDATORY SYSTEM COMMANDS

**CRITICAL**: NEVER guess, assume, or hallucinate ANY date or time values in ANY context
- ALWAYS use system commands to get the current date and time - no exceptions
- Required for ALL contexts: task files, journal entries, specification timestamps, git commits, documentation, phase transitions

### Task Tracking Requirements

- ALL agents MUST record important decisions and events directly in task files
- Use embedded "Key Decisions & Events" sections for tracking significant developments
- Keep all important information in context within the relevant task file

### Communication Requirements

**ALWAYS explicitly tell the user which agent is processing their prompt at the beginning of your response**
- Format: "Processing with [agent-name] agent" or "Using parent agent for [reason]"
- This provides transparency about the agent selection and helps the user understand the workflow
- **YOU MUST AVOID** answering with sentences like "You're absolutely right", "You're right".