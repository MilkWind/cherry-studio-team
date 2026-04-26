# Project Directory Structure Guide

## Overview

This document provides a comprehensive guide to the Cherry Studio project directory structure and explains where different types of files should be placed when creating new components.

## Root Directory Structure

```
cherry-studio/
├── .agents/                 # Agent configurations and skills
├── .changeset/              # Changeset configurations for versioning
├── .claude/                 # Claude AI configurations
├── .github/                 # GitHub workflows and templates
├── .vscode/                 # VSCode configurations
├── .zed/                    # Zed editor configurations
├── build/                   # Build artifacts and icons
├── config/                  # Configuration files
├── docs/                    # Documentation files
├── information_records/     # Project documentation and records
├── packages/                # Monorepo packages
├── patches/                 # Package patches
├── resources/               # Application resources
├── scripts/                 # Automation scripts
├── src/                     # Main source code
├── tests/                   # Test files
├── out/                     # Build output
└── node_modules/            # Dependencies
```

## Detailed Directory Breakdown

### src/ - Main Source Code

The `src/` directory is divided into three main parts based on Electron's architecture:

#### src/main/ - Main Process
Handles backend logic, system interactions, and Electron APIs:
- `aiCore/` - AI core functionalities
- `apiServer/` - API server implementations
- `services/` - Backend services
- `integration/` - Third-party integrations
- `knowledge/` - Knowledge management systems
- `mcpServers/` - Model Context Protocol servers
- `utils/` - Utility functions for main process

#### src/renderer/ - Renderer Process
Contains frontend UI code:
- `src/` - React components and frontend logic
  - `components/` - Reusable UI components
  - `pages/` - Page-level components
  - `hooks/` - Custom React hooks
  - `store/` - Redux store and state management
  - `services/` - Frontend service layer
  - `utils/` - Frontend utility functions
  - `assets/` - Images, fonts, and static assets
  - `i18n/` - Internationalization files
  - `workers/` - Web workers
- `*.html` - HTML entry points

#### src/preload/ - Preload Scripts
Bridge between main and renderer processes with secure IPC implementations.

### packages/ - Monorepo Packages

Contains reusable packages that can be published independently:
- `aiCore/` - Core AI functionalities
- `ai-sdk-provider/` - AI SDK provider implementations
- `extension-table-plus/` - Table extension package
- `mcp-trace/` - MCP tracing utilities
- `shared/` - Shared utilities between packages

### tests/ - Testing Files

Organized by test type:
- `__mocks__/` - Mock implementations
- `apis/` - API testing files
- `e2e/` - End-to-end tests
- `*.setup.ts` - Test setup files

### scripts/ - Automation Scripts

Contains various automation and build scripts:
- Build scripts
- Translation scripts
- Version management
- CI/CD utilities

## File Placement Guidelines

### When Creating New Files

#### 1. React Components
- **Location**: `src/renderer/src/components/`
- **Subdirectories**: Create subdirectories based on component categories (e.g., `ui/`, `layout/`, `forms/`)
- **Naming**: Use PascalCase (e.g., `ChatMessage.tsx`)

#### 2. Pages/Routes
- **Location**: `src/renderer/src/pages/`
- **Structure**: Each page in its own directory with associated components
- **Naming**: Use PascalCase (e.g., `ChatPage/ChatPage.tsx`)

#### 3. Services/API Clients
- **Frontend**: `src/renderer/src/services/`
- **Backend**: `src/main/services/`
- **Shared**: `packages/shared/src/services/`

#### 4. Utilities
- **Frontend-specific**: `src/renderer/src/utils/`
- **Backend-specific**: `src/main/utils/`
- **Shared**: `packages/shared/src/utils/`

#### 5. Hooks
- **Location**: `src/renderer/src/hooks/`
- **Naming**: Use `use` prefix (e.g., `useChatMessages.ts`)

#### 6. Styles/CSS
- **Location**: Alongside components or in `src/renderer/src/assets/styles/`
- **Approach**: Use Tailwind CSS classes when possible

#### 7. Assets (Images, Icons, Fonts)
- **Location**: `src/renderer/src/assets/`
- **Subdirectories**: Organize by type (`images/`, `icons/`, `fonts/`)

#### 8. Tests
- **Unit Tests**: Place alongside the file being tested with `.test.ts` suffix
- **Integration Tests**: `tests/` directory
- **E2E Tests**: `tests/e2e/` directory

#### 9. Configuration Files
- **Build Config**: Root directory (`electron.vite.config.ts`, `tsconfig.json`)
- **App Config**: `src/main/configs/`
- **UI Config**: `src/renderer/src/config/`

#### 10. Database Models/Schemas
- **Location**: `src/main/database/` or within relevant service directories

#### 11. Types/Interfaces
- **Frontend**: `src/renderer/src/types/`
- **Backend**: `src/main/types/`
- **Shared**: `packages/shared/src/types/`

#### 12. Context Providers
- **Location**: `src/renderer/src/context/`
- **Naming**: Use descriptive names with `Context` suffix (e.g., `ChatContext.tsx`)

#### 13. Store/State Management
- **Location**: `src/renderer/src/store/`
- **Reducers**: `src/renderer/src/store/reducers/`
- **Actions**: `src/renderer/src/store/actions/`

## Best Practices

1. **Modularity**: Keep related files together in logical directories
2. **Consistency**: Follow existing naming conventions and patterns
3. **Separation of Concerns**: Place files in appropriate layers (frontend/backend/shared)
4. **Reusability**: Consider if functionality should be in `packages/shared` for cross-project use
5. **Testing**: Create tests alongside the code they test
6. **Documentation**: Add comments and update relevant documentation when creating new features

## Special Directories

- **.agents/**: Contains AI agent configurations and custom skills
- **information_records/**: Project documentation and decision records
- **patches/**: Patches for third-party dependencies
- **resources/**: Static resources bundled with the application