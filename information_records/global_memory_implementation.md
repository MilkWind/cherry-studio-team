# Global Memory Implementation in Cherry Studio

## Overview

The global memory feature in Cherry Studio enables persistent memory capabilities across conversations and assistants. When enabled, it allows the application to store, retrieve, and utilize conversation memories and facts to enhance AI interactions.

## Architecture

### Core Components

1. **Memory Store (Redux)**
   - Located in `src/renderer/src/store/memory.ts`
   - Manages the global memory state including configuration and enabled status
   - Provides selectors and actions for memory management

2. **Memory Service**
   - Renderer process: `src/renderer/src/services/MemoryService.ts`
   - Main process: `src/main/services/memory/MemoryService.ts`
   - Handles all memory operations through IPC communication

3. **Memory Processor**
   - Processes conversation memories and extracts facts
   - Manages vector embeddings for semantic search

## Implementation Details

### 1. Global Memory State Management

The global memory state is managed through Redux with the following key elements:

```typescript
interface MemoryState {
  memoryConfig: MemoryConfig
  currentUserId: string
  globalMemoryEnabled: boolean // The key flag controlling memory functionality
}
```

The `globalMemoryEnabled` flag acts as a master switch that controls whether memory features are active throughout the application.

### 2. Enabling Global Memory

Global memory can be toggled in the Memory Settings page:

1. User navigates to Settings → Memory
2. Toggles the "Global Memory" switch
3. The `setGlobalMemoryEnabled` action updates the Redux store
4. The setting is persisted in the application state

### 3. Memory Operations Control

The global memory flag affects several key areas:

#### Memory Search Tool
In `src/renderer/src/aiCore/tools/MemorySearchTool.ts`:
```typescript
const globalMemoryEnabled = selectGlobalMemoryEnabled(store.getState())
if (!globalMemoryEnabled) {
  return [] // Short-circuits if global memory is disabled
}
```

#### Search Orchestration Plugin
In `src/renderer/src/aiCore/plugins/searchOrchestrationPlugin.ts`:
```typescript
// During memory storage
const globalMemoryEnabled = selectGlobalMemoryEnabled(store.getState())
if (!globalMemoryEnabled || !assistant.enableMemory) {
  return // Skip memory processing entirely
}

// During tool configuration
const globalMemoryEnabled = selectGlobalMemoryEnabled(store.getState())
if (globalMemoryEnabled && assistant.enableMemory) {
  params.tools['builtin_memory_search'] = memorySearchTool(assistant.id)
}
```

#### Assistant Memory Settings
In `src/renderer/src/pages/settings/AssistantSettings/AssistantMemorySettings.tsx`:
```typescript
const isMemoryEnabled = globalMemoryEnabled && isMemoryConfigured
// Memory toggle is disabled if global memory is off
```

### 4. Data Flow

1. **Configuration**: Memory configuration is stored in Redux and persisted
2. **Activation**: When global memory is enabled, memory tools become available
3. **Collection**: Conversations are processed to extract facts when global memory is enabled
4. **Storage**: Memories are stored in a SQLite database in the main process
5. **Retrieval**: Relevant memories are retrieved during conversations using vector similarity search
6. **Integration**: Memories are injected into AI prompts to provide context

## Key Features Controlled by Global Memory

1. **Conversation Memory Storage**: Automatic storage of conversation snippets
2. **Fact Extraction**: Extraction of key facts from conversations
3. **Semantic Search**: Vector-based search through stored memories
4. **Context Enhancement**: Providing relevant historical context to AI responses
5. **User-Specific Memory**: Separate memory contexts for different users

## Dependencies

For global memory to function properly, the following must be configured:
- Embedding model (for vector representations)
- LLM model (for fact extraction)
- Proper database initialization in the main process

## Impact of Disabling Global Memory

When `globalMemoryEnabled` is false:
- All memory operations are skipped
- Memory search tools are not added to AI requests
- Conversation processing bypasses memory extraction
- All assistants behave as if memory is disabled regardless of individual settings
- UI elements related to memory show warning messages

This design ensures that memory functionality can be completely disabled application-wide with a single toggle, providing users with control over data persistence and processing.