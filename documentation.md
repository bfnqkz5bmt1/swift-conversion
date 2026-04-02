I'll help you create documentation for the entire codebase. Let me first explore the repository structure and understand the code.Let me explore the repository structure more deeply:Now let me gather the actual file contents from key files to understand the codebase structure:Now I'll create comprehensive documentation for the codebase. Let me start by creating a main README and then organize documentation by module.I'll create comprehensive documentation for the swift-conversion codebase:

```markdown name=DOCUMENTATION.md
# Swift-Conversion Codebase Documentation

## Overview

**Swift-Conversion** is a TypeScript-based project (forked from `yasasbanukaofficial/claude-code`) that appears to be part of an AI assistant framework focused on code generation, query processing, and tool execution. The repository was created recently (32 minutes ago) and is a fork of the claude-code project.

**Primary Language:** TypeScript  
**Repository:** `bfnqkz5bmt1/swift-conversion`  
**Size:** ~9.8 MB

---

## Directory Structure

```
src/
├── QueryEngine.ts          # Core query execution engine
├── Tool.ts                 # Tool definitions and interfaces
├── Task.ts                 # Task management system
├── commands.ts             # Command registry and management
├── context.ts              # Context configuration
├── cost-tracker.ts         # Cost tracking functionality
├── costHook.ts             # Cost hook utilities
├── dialogLaunchers.tsx     # Dialog launching components
├── history.ts              # History management
├── ink.ts                  # Ink framework integration
├── interactiveHelpers.tsx  # Interactive UI helpers
├── main.tsx                # Application entry point
├── projectOnboardingState.ts # Onboarding state management
├── query.ts                # Query processing logic
├── replLauncher.tsx        # REPL launcher component
├── setup.ts                # Setup utilities
├── tasks.ts                # Task utilities
├── tools.ts                # Tool utilities
│
├── assistant/              # Assistant-related modules
├── bootstrap/              # Bootstrap initialization
├── bridge/                 # Bridge communication
├── buddy/                  # Buddy system
├── cli/                    # CLI interfaces
├── commands/               # Individual command implementations
├── components/             # React/Ink UI components
├── constants/              # Constant definitions (XML tags, etc.)
├── context/                # Context providers
├── coordinator/            # Coordinator mode
├── entrypoints/            # Application entry points
├── hooks/                  # React hooks and custom hooks
├── keybindings/            # Keyboard bindings
├── memdir/                 # Memory directory management
├── migrations/             # Database/state migrations
├── moreright/              # Additional utilities
├── native-ts/              # Native TypeScript modules
├── outputStyles/           # Output styling options
├── plugins/                # Plugin system
├── query/                  # Query-related modules
├── remote/                 # Remote execution support
├── schemas/                # Data schemas
├── screens/                # Screen components
├── server/                 # Server implementations
├── services/               # Business logic services
├── skills/                 # Skill definitions
├── state/                  # State management (AppState, etc.)
├── tasks/                  # Task implementations
├── tools/                  # Tool implementations
├── types/                  # TypeScript type definitions
├── upstreamproxy/          # Proxy utilities
├── utils/                  # General utilities
├── vim/                    # Vim mode support
└── voice/                  # Voice input/output
```

---

## Core Modules

### 1. **QueryEngine.ts** (46.6 KB)
**Purpose:** Central orchestration engine for query execution and conversation management.

**Key Responsibilities:**
- Manages the full query lifecycle and session state
- Processes user input and system prompts
- Handles tool execution and permission management
- Tracks API usage and costs
- Manages message history with support for snipping/compacting
- Implements result streaming and error handling

**Key Classes:**
- `QueryEngine`: Main class that manages a single conversation
  - `submitMessage()`: Async generator that yields SDK messages
  - `interrupt()`: Stops current execution
  - `getMessages()`: Retrieves conversation history
  - `getSessionId()`: Gets current session ID

**Dependencies:**
- Tool system (Tool.ts)
- Message types
- API/Claude service (claude.ts)
- Cost tracking
- File state management

---

### 2. **Tool.ts** (29.5 KB)
**Purpose:** Defines the tool system architecture and interfaces for extending functionality.

**Key Concepts:**

**Tool Interface:**
```typescript
Tool<Input, Output, P> = {
  call(): Promise<ToolResult>
  description(): Promise<string>
  inputSchema: Input
  checkPermissions(): Promise<PermissionResult>
  isConcurrencySafe(): boolean
  isReadOnly(): boolean
  // ...rendering, progress, validation methods
}
```

**ToolResult Structure:**
- `data`: Tool output
- `newMessages`: Additional conversation messages
- `mcpMeta`: Model Context Protocol metadata

**Key Functions:**
- `buildTool()`: Factory function with safe defaults
- `toolMatchesName()`: Check tool by name or alias
- `findToolByName()`: Tool lookup utility

**Tool Permission Context:**
- Permission modes (default, bypass, etc.)
- Working directory restrictions
- Allow/deny/ask rules by source

---

### 3. **Task.ts** (3.15 KB)
**Purpose:** Task management and background execution support.

**Task Types:**
- `local_bash`: Local shell execution
- `local_agent`: Local agent execution
- `remote_agent`: Remote agent execution
- `in_process_teammate`: In-process execution
- `local_workflow`: Workflow execution
- `monitor_mcp`: MCP monitoring
- `dream`: Dream tasks

**Task States:**
```
pending → running → {completed | failed | killed}
```

**Key Exports:**
- `TaskHandle`: Task reference with cleanup
- `generateTaskId()`: Create unique task IDs
- `createTaskStateBase()`: Initialize task state
- `isTerminalTaskStatus()`: Check if task is done

---

### 4. **commands.ts** (25.2 KB)
**Purpose:** Command registry and dispatcher for both built-in and dynamic commands.

**Command Types:**
- `local`: Execute locally, return text
- `local-jsx`: Execute locally, render Ink UI
- `prompt`: Expand to text for model

**Built-in Commands Include:**
- Session/context management: `/resume`, `/session`, `/memory`, `/context`
- Code operations: `/commit`, `/review`, `/diff`, `/branch`
- Configuration: `/config`, `/model`, `/theme`, `/keybindings`
- Advanced: `/plan`, `/compact`, `/thinkback`, `/agents`
- Utilities: `/help`, `/exit`, `/cost`, `/status`

**Key Functions:**
- `getCommands()`: Load all available commands (built-in + skills + plugins)
- `getSlashCommandToolSkills()`: Get model-invocable skills
- `findCommand()`: Lookup by name/alias
- `isBridgeSafeCommand()`: Check remote-safe commands
- `clearCommandsCache()`: Invalidate memoization

**Command Filtering:**
- Availability requirements (claude-ai, console API)
- Feature flags (KAIROS, BRIDGE_MODE, etc.)
- Auth-based visibility

---

### 5. **context.ts** (6.45 KB)
**Purpose:** Manages execution context and state configuration.

**Likely Responsibilities:**
- Context provider setup
- State initialization
- Permission context configuration

---

### 6. **cost-tracker.ts** (10.7 KB)
**Purpose:** Tracks API usage costs and model metrics.

**Key Metrics:**
- Token usage (input/output)
- Model-specific costs
- Total API duration
- Cost aggregation

**Used By:** QueryEngine for SDK results

---

### 7. **query.ts** (68.7 KB)
**Purpose:** Low-level query execution and message handling.

**Likely Responsibilities:**
- Message streaming and processing
- Tool use execution loop
- Error handling and retries
- Stop hooks and cleanup

**Integration with QueryEngine:**
- QueryEngine uses `query()` for the core execution loop
- Handles streaming events and message mutations

---

### 8. **history.ts** (14.1 KB)
**Purpose:** Message history management and persistence.

**Features:**
- Session transcript storage
- Message deduplication
- History compacting/snipping
- Resume functionality

---

## Advanced Features

### State Management (`state/AppState.js`)
- Tool permission context
- Fast mode state
- File history
- Attribution tracking
- MCP connections

### Plugin System (`plugins/`)
- Plugin discovery and loading
- Plugin command registration
- Built-in plugin support

### Skills System (`skills/`)
- Skill directory loading
- Bundled skills
- Dynamic skill discovery
- Plugin skills integration

### MCP (Model Context Protocol)
- Server connections
- Resource management
- Tool integration from external servers

### UI Components (`components/`, `screens/`)
- React/Ink-based terminal UI
- Message selector
- Spinner animations
- Dialog launchers

### Services (`services/`)
- **API**: Claude API integration, error handling
- **Compact**: Message compacting/snipping for history
- **MCP**: Model Context Protocol integration
- **Skill Search**: Skill discovery and indexing

---

## Key Data Flows

### 1. **User Query Execution**
```
User Input
    ↓
processUserInput() → Parse slash commands & attachments
    ↓
QueryEngine.submitMessage()
    ↓
query() → API streaming loop
    ↓
Tool Execution (if model calls tools)
    ↓
Message accumulation & cost tracking
    ↓
Result yielding (SDK format)
```

### 2. **Tool Execution**
```
Tool Use from Model
    ↓
validateInput() → Schema & custom validation
    ↓
checkPermissions() → User/auto approval
    ↓
canUseTool() → Permission context check
    ↓
tool.call() → Execution
    ↓
Progress updates (optional)
    ↓
Tool Result → Back to model
```

### 3. **Command Processing**
```
User Types: /command args
    ↓
getCommands() → Find matching command
    ↓
isCommandEnabled() → Feature & auth checks
    ↓
Execute (local, jsx, or prompt expansion)
    ↓
Render/send result
```

---

## Type System

### Message Types
- `UserMessage`: User input
- `AssistantMessage`: Model response
- `SystemMessage`: System signals (errors, boundaries, etc.)
- `ProgressMessage`: Tool execution progress
- `AttachmentMessage`: File/resource attachments
- `ToolUseMessage`: Tool call requests

### Permission Types
- `PermissionResult`: Allow/deny decisions
- `PermissionMode`: default | bypass | auto | etc.
- `ToolPermissionContext`: Permission rules and state

### SDK Types (`entrypoints/agentSdkTypes.ts`)
- `SDKMessage`: Union of message types for SDK consumers
- `SDKStatus`: Execution status
- `SDKPermissionDenial`: Tracked permission denials

---

## Configuration & Environment

### Key Environment Variables
- `CLAUDE_COWORK_MEMORY_PATH_OVERRIDE`: Memory directory override
- `CLAUDE_CODE_SYNC_PLUGIN_INSTALL`: Plugin installation sync
- `MAX_STRUCTURED_OUTPUT_RETRIES`: Retry limits
- `USER_TYPE`: User type (ant, regular, etc.)

### Feature Flags (via `bun:bundle`)
- `COORDINATOR_MODE`: Coordinator-specific behavior
- `HISTORY_SNIP`: Message snipping/compacting
- `KAIROS`: Advanced features
- `BRIDGE_MODE`: Remote bridge support
- `MCP_SKILLS`: MCP-provided skills

---

## Common Patterns

### 1. **Memoization**
```typescript
const getCommands = memoize(async (cwd: string): Promise<Command[]> => {
  // Load all commands (expensive)
})
```

### 2. **Tool Defaults**
```typescript
export const buildTool = (def: ToolDef) => ({
  ...TOOL_DEFAULTS,
  ...def
})
```

### 3. **Async Generators for Streaming**
```typescript
async *submitMessage(prompt: string) {
  yield systemInitMessage
  for await (const message of query(...)) {
    yield normalizedMessage
  }
  yield resultMessage
}
```

### 4. **Permission Wrapping**
```typescript
const wrappedCanUseTool = async (...) => {
  const result = await canUseTool(...)
  if (result.behavior !== 'allow') {
    this.permissionDenials.push(...)
  }
  return result
}
```

---

## Integration Points

### External Services
- **Anthropic Claude API**: Via `services/api/claude.js`
- **MCP Servers**: Via `services/mcp/`
- **Plugins**: Via plugin system

### Local Systems
- **Filesystem**: File read/write operations
- **Git**: Version control integration
- **Shell**: Command execution (Bash)
- **IDE**: IDE-specific features

---

## Performance Considerations

1. **Lazy Loading**: Heavy modules imported on-demand (e.g., insights.ts)
2. **Memoization**: Command/skill loading cached by working directory
3. **Fire-and-Forget**: Transcript writing non-blocking for most operations
4. **Message Snipping**: Long histories compressed to reduce memory
5. **File State Cache**: LRU cache for file operations

---

## Testing & Debugging

### Available Debug Commands
- `/debug-tool-call`: Debug tool execution
- `/ant-trace`: Tracing for Ant users
- `/perf-issue`: Performance issue reporting

### Utilities
- `getInMemoryErrors()`: Last 100 errors buffer
- `headlessProfilerCheckpoint()`: Performance tracking
- `logError()`, `logForDebugging()`: Logging

---

## Future Extensibility

The codebase is designed for extensibility:
1. **New Tools**: Implement `Tool` interface via `buildTool()`
2. **New Commands**: Add to `/commands/` directory
3. **New Skills**: Add to `/skills/` directory
4. **Plugins**: Load from plugin system
5. **MCP Integration**: Connect external servers
6. **Custom Output Styles**: Via output styling system

---

## Key Takeaways

- **QueryEngine** is the orchestration core
- **Tools** provide extensible capabilities
- **Commands** offer user-facing interactions
- **Services** abstract external integrations
- **Async generators** enable streaming responses
- **Permission system** ensures security
- **State management** preserves session data
- **Feature flags** enable gradual rollouts

This architecture supports a flexible AI assistant that can be extended with custom tools, commands, and integrations while maintaining security and performance.
```

I've created comprehensive documentation for the swift-conversion codebase. The documentation includes:

1. **Overview** - High-level project description and statistics
2. **Directory Structure** - Visual tree of all major directories
3. **Core Modules** - Detailed breakdown of the 8 main TypeScript files with:
   - Purpose and responsibilities
   - Key classes/functions
   - Data structures
   - Dependencies

4. **Advanced Features** - Sections on:
   - State management
   - Plugin and skills systems
   - MCP integration
   - UI components
   - Services architecture

5. **Key Data Flows** - Visual diagrams of:
   - Query execution pipeline
   - Tool execution lifecycle
   - Command processing flow

6. **Type System** - Documentation of important TypeScript types
7. **Configuration** - Environment variables and feature flags
8. **Common Patterns** - Recurring code patterns used throughout
9. **Integration Points** - External services and local systems
10. **Performance Considerations** - Optimization techniques
11. **Testing & Debugging** - Available debug tools
12. **Extensibility Guide** - How to add new features
