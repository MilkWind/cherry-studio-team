# Package.json Scripts Explanation

This document explains the function of each script command in the package.json file, along with the components involved in these commands.

## Development Scripts

### `dev`
```bash
npm run generate:openapi && dotenv electron-vite dev
```
Starts the development server with OpenAPI spec generation. It first generates the OpenAPI specification and then starts the Electron-Vite development server with environment variables.

### `dev:watch`
```bash
dotenv electron-vite dev -- -w
```
Starts the development server in watch mode with environment variables, automatically restarting when files change.

### `debug`
```bash
electron-vite -- --inspect --sourcemap --remote-debugging-port=9222
```
Starts the application in debug mode with inspection enabled, sourcemaps generated, and remote debugging on port 9222.

## Build Scripts

### `build`
```bash
npm run generate:openapi && npm run typecheck && electron-vite build
```
Builds the production version of the application. It generates OpenAPI specs, performs type checking, and builds the Electron application.

### `build:check`
```bash
pnpm lint && pnpm openapi:check && pnpm test
```
Runs linting, OpenAPI specification validation, and tests to ensure code quality before building.

### `build:unpack`
```bash
dotenv npm run build && electron-builder --dir
```
Builds the application and creates an unpacked directory distribution.

### Platform-Specific Build Scripts

#### Windows Builds
- `build:win`: Builds for Windows x64 and arm64 architectures
- `build:win:x64`: Builds for Windows x64 architecture only
- `build:win:arm64`: Builds for Windows arm64 architecture only

#### macOS Builds
- `build:mac`: Builds for macOS x64 and arm64 architectures
- `build:mac:arm64`: Builds for macOS arm64 architecture only
- `build:mac:x64`: Builds for macOS x64 architecture only

#### Linux Builds
- `build:linux`: Builds for Linux x64 and arm64 architectures
- `build:linux:arm64`: Builds for Linux arm64 architecture only
- `build:linux:x64`: Builds for Linux x64 architecture only

## Type Checking Scripts

### `typecheck`
```bash
pnpm --filter @cherrystudio/ai-sdk-provider build && concurrently -n "node,web,aicore" -c "cyan,magenta,yellow" "npm run typecheck:node" "npm run typecheck:web" "pnpm --filter @cherrystudio/ai-core typecheck"
```
Runs type checking across multiple project parts (node, web, AI core) concurrently.

### `typecheck:node`
```bash
tsgo --noEmit -p tsconfig.node.json --composite false
```
Performs type checking for Node.js code using TypeScript without emitting files.

### `typecheck:web`
```bash
tsgo --noEmit -p tsconfig.web.json --composite false
```
Performs type checking for web code using TypeScript without emitting files.

## Testing Scripts

### Core Test Scripts
- `test`: Runs all tests silently
- `test:main`: Runs tests for the main process
- `test:renderer`: Runs tests for the renderer process
- `test:aicore`: Runs tests for the AI core module
- `test:shared`: Runs tests for shared modules

### Specialized Test Scripts
- `test:update`: Updates test snapshots
- `test:coverage`: Runs tests with coverage reporting
- `test:ui`: Opens the Vitest UI for interactive testing
- `test:watch`: Runs tests in watch mode
- `test:e2e`: Runs end-to-end tests with Playwright
- `test:lint`: Runs code linting with Oxlint and ESLint

## Linting & Formatting Scripts

### `lint`
```bash
oxlint --fix && eslint . --ext .js,.jsx,.cjs,.mjs,.ts,.tsx,.cts,.mts --fix --cache && pnpm typecheck && pnpm i18n:check && pnpm format
```
Runs multiple code quality checks including fixing issues with Oxlint, ESLint, type checking, internationalization checks, and formatting.

### `format`
```bash
biome format --write && biome lint --write
```
Formats code and fixes lint issues using Biome.

### `format:check`
```bash
biome format && biome lint
```
Checks code formatting and lint issues without making changes.

## Internationalization (i18n) Scripts

### `i18n:check`
```bash
dotenv -e .env -- tsx scripts/check-i18n.ts
```
Checks internationalization files for issues using environment variables.

### `i18n:hardcoded`
```bash
tsx scripts/check-hardcoded-strings.ts
```
Checks for hardcoded strings that should be internationalized.

### `i18n:hardcoded:strict`
```bash
I18N_STRICT=true tsx scripts/check-hardcoded-strings.ts
```
Performs strict checking for hardcoded strings.

### `i18n:sync`
```bash
dotenv -e .env -- tsx scripts/sync-i18n.ts
```
Synchronizes internationalization files using environment variables.

### `i18n:translate`
```bash
dotenv -e .env -- tsx scripts/auto-translate-i18n.ts
```
Automatically translates internationalization files using environment variables.

### `i18n:all`
```bash
pnpm i18n:sync && pnpm i18n:translate
```
Runs both synchronization and translation of internationalization files.

## Database Scripts (Agents)

These scripts manage the agents database using Drizzle ORM:

- `agents:generate`: Generates database migrations
- `agents:push`: Pushes schema changes to the database
- `agents:studio`: Opens the Drizzle Studio database interface
- `agents:drop`: Drops the database

## OpenAPI Scripts

### `generate:openapi`
```bash
tsx scripts/generate-openapi-spec.ts && biome format --write src/main/apiServer/generated/openapi-spec.json
```
Generates the OpenAPI specification file and formats it with Biome.

### `openapi:check`
```bash
tsx scripts/generate-openapi-spec.ts --check
```
Validates the OpenAPI specification without generating a new file.

## Release & Publishing Scripts

### `release`
```bash
node scripts/version.js
```
Handles version management for releases.

### `publish`
```bash
pnpm build:check && pnpm release patch push
```
Runs build checks and releases a patch version.

### `pulish:artifacts`
```bash
cd packages/artifacts && npm publish && cd -
```
Publishes artifacts package to npm (note: typo in command name).

### `changeset:*` Scripts
- `changeset`: Runs Changesets CLI
- `changeset:status`: Checks changeset status
- `changeset:version`: Versions packages with Changesets
- `changeset:publish`: Publishes packages with Changesets

## Package Management Scripts

### `packages:build`
```bash
pnpm --filter @cherrystudio/ai-sdk-provider build && pnpm --filter @cherrystudio/ai-core build && pnpm --filter @cherrystudio/extension-table-plus build
```
Builds internal packages in the correct order.

### `packages:release`
```bash
pnpm packages:build && changeset publish
```
Builds and publishes internal packages.

## Analysis Scripts

### `analyze:renderer`
```bash
VISUALIZER_RENDERER=true pnpm build
```
Builds the application with renderer bundle visualization enabled.

### `analyze:main`
```bash
VISUALIZER_MAIN=true pnpm build
```
Builds the application with main process bundle visualization enabled.

## CI/CD Scripts

### `ci:basic-check`
```bash
pnpm test:lint && pnpm format:check && pnpm typecheck && pnpm i18n:check && pnpm i18n:hardcoded:strict && pnpm openapi:check && pnpm skills:check
```
Runs basic continuous integration checks.

### `ci:test-check`
```bash
pnpm test:main && pnpm test:renderer && pnpm test:aicore && pnpm test:shared && pnpm test:scripts
```
Runs comprehensive test suite for continuous integration.

### `ci`
```bash
pnpm ci:basic-check && pnpm ci:test-check
```
Runs all CI checks including basic checks and test checks.

## Utility Scripts

### `prepare`
```bash
git config blame.ignoreRevsFile .git-blame-ignore-revs && prek install
```
Prepares the development environment by configuring Git blame ignore and installing pre-commit hooks.

### `start`
```bash
electron-vite preview
```
Starts the built Electron application in preview mode.

### `claude`
```bash
dotenv -e .env -- claude
```
Runs Claude AI integration with environment variables.

### `skills:sync`
```bash
tsx scripts/skills-sync.ts
```
Synchronizes AI skills configuration.

### `skills:check`
```bash
tsx scripts/skills-check.ts
```
Validates AI skills configuration.

### `update:languages`
```bash
tsx scripts/update-languages.ts
```
Updates language configurations.

### `update:upgrade-config`
```bash
tsx scripts/update-app-upgrade-config.ts
```
Updates application upgrade configurations.

## Benchmarking Scripts

### Core Benchmarks
- `bench`: Runs all benchmarks
- `bench:main`: Runs benchmarks for the main process
- `bench:renderer`: Runs benchmarks for the renderer process
- `bench:aicore`: Runs benchmarks for the AI core module
- `bench:shared`: Runs benchmarks for shared modules

Each script serves a specific purpose in the development, testing, building, and deployment workflow of the CherryStudio application. The scripts utilize various tools and frameworks including:
- Electron for cross-platform desktop application framework
- Vite for fast development and building
- TypeScript for type checking
- Biome and ESLint for code formatting and linting
- Vitest for testing
- Playwright for end-to-end testing
- Drizzle ORM for database management
- Changesets for version management and publishing