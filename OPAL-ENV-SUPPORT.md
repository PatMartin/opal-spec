Add consistent python-dotenv support across the OPAL orchestrator and all OPAL services in scope.

Inspect the repositories, existing configuration systems, entry points, dependency management, tests, and documentation. Then implement, test, and document the complete change in one pass. Make routine implementation decisions without stopping for approval.

Requirements

1. Dependency
   Add python-dotenv as a required runtime dependency using each project’s existing dependency-management conventions.
   If an existing settings library already provides dotenv integration, use one coordinated loading mechanism rather than competing loaders.

2. Configuration precedence
   Enforce this order, highest to lowest:
   - Explicit CLI arguments
   - Existing environment variables
   - .env entries
   - Existing configuration-file values
   - Built-in defaults

   Existing environment variables must ALWAYS override .env entries, including when an existing environment variable is an empty string.
   When calling python-dotenv directly, use override=False.
   Unspecified CLI options must not override configured values merely because the CLI framework supplies defaults.

3. Automatic loading
   Load .env automatically at application startup, before constructing settings.
   Default to exactly .env in the current working directory.
   Do not implicitly search parent directories.
   Avoid file access, file creation, and os.environ mutation during module import.

4. CLI controls
   Provide consistent:
   --env-file PATH
   --no-env-file

   Make these mutually exclusive.
   Resolve relative explicit paths against the current working directory.
   --no-env-file must disable both dotenv loading and automatic .env creation.
   An explicitly requested missing or unreadable file must produce a clear error; do not silently create it.

5. Missing default .env
   If the default .env does not exist, initialize it from supported existing configuration-file values and built-in defaults so that ordinary startup preserves existing behavior.

   Generate these baseline values BEFORE applying environment-variable or CLI overrides. Do not persist those overrides.

   - Never overwrite an existing .env.
   - Do not write secrets, credentials, tokens, or passwords into the generated file. Omit sensitive entries or show them as commented placeholders.
   - Omit settings that cannot be represented faithfully, and document any material limitations.
   - Properly quote and escape values so loading the generated file preserves their meaning.
   - Account for dotenv interpolation when preserving literal values.
   - Use safe creation that cannot overwrite a file created concurrently, with restrictive permissions where supported.
   - If the working directory is read-only or creation otherwise fails, issue a concise warning without sensitive values and continue using the existing configuration sources.
   - Do not regenerate or synchronize an existing .env automatically.

   Document that the generated file is a snapshot: its active entries take precedence over later configuration-file changes.

6. OPAL execution modes
   Support standalone services and orchestrator-managed execution consistently.

   - Standalone service entry points own dotenv loading and initialization.
   - In embedded mode, the top-level opal application loads or initializes .env once, before constructing service settings.
   - Embedded service factories accept configuration without independently discovering, creating, or loading .env files.
   - In subprocess mode, define how the orchestrator propagates its dotenv policy so child services do not accidentally reload files or re-enable disabled loading.
   - Preserve explicit service CLI overrides and avoid introducing cross-service setting-name collisions.
   - Preserve direct Python/library use without automatic dotenv side effects.

7. Examples and documentation
   Add or update .env.example using supported settings, safe placeholders, and brief explanations.
   Update .gitignore to exclude local dotenv files while allowing .env.example.
   Do not modify existing user .env files.

   Document:
   - Configuration precedence
   - Automatic loading and missing-file initialization
   - Current-working-directory behavior
   - --env-file and --no-env-file
   - Standalone, embedded, and subprocess behavior
   - Snapshot behavior of generated settings
   - Minimal usage examples

8. Verification
   Add focused tests covering:
   - Loading values from .env
   - Existing nonempty and empty environment variables winning over .env
   - Explicit CLI arguments winning over environment variables
   - Unspecified CLI options preserving configured values
   - .env winning over configuration-file values and defaults
   - Missing default .env initialization preserving baseline settings
   - Exclusion of secrets and CLI/environment overrides from generated files
   - Existing .env files remaining untouched
   - Explicit missing or unreadable file errors
   - --no-env-file preventing loading and creation
   - Safe startup when default-file creation fails
   - No parent-directory discovery
   - Embedded and subprocess configuration ownership

   Use temporary directories and isolated environments. Do not read or alter developer .env files.
   Run appropriate existing checks and the new tests.

Keep the implementation small and consistent with each repository’s conventions. Avoid unrelated refactoring and do not introduce a new configuration framework or shared package unless the existing architecture already provides an appropriate home.

Final report
- Repositories changed and what changed in each
- Exact usage commands
- Confirmed precedence and execution-mode behavior
- How missing .env initialization works
- Tests run, pass/fail counts, and checks not run
- Any remaining limitations or compatibility concerns