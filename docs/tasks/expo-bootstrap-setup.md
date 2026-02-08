# Expo Bootstrap Setup

## Scope
Implement Phase 1 bootstrap only:

- Expo SDK `54.0.33`
- TypeScript `5.9.3`
- Biome `2.3.14`
- ESLint + Prettier coexistence with Biome-primary workflow
- Expo Router minimal stack scaffold

## Checklist
- [x] Scaffold app and sync into repo root
- [x] Configure router entry and minimal routes
- [x] Configure lint/format toolchain
- [x] Add scripts and ignores
- [x] Run verification commands

## Review
### Implemented

- Scaffolded Expo TypeScript app from temp directory and synced into repo root while preserving `docs/`.
- Switched entry to Expo Router via `main: "expo-router/entry"`.
- Added minimal router files:
  - `app/_layout.tsx`
  - `app/index.tsx`
- Pinned required versions:
  - `expo: 54.0.33`
  - `typescript: 5.9.3`
  - `@biomejs/biome: 2.3.14`
- Added Biome, ESLint flat config, and Prettier docs-only setup.
- Added deterministic scripts for typecheck, lint, format, and format-check.

### Verification Log

1. `pnpm install` (with lockfile refresh): passed.
2. `pnpm run typecheck`: passed.
3. `pnpm run lint`: passed (`biome lint` + `eslint`).
4. `pnpm run format:check`: passed.
5. `pnpm exec expo config --json`: passed, includes `"plugins":["expo-router"]` and `"extra":{"router":{}}`.
6. Smoke test:
   - `CI=1 pnpm expo start --offline` starts and reports project startup.
   - Process was manually interrupted with `Ctrl+C` after startup log.
