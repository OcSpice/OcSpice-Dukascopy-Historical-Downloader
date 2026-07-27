# OcSpice-Dukascopy-Historical-Downloader

# AGENTS.md

## Project purpose
This repository builds **OcSpice Dukascopy Historical Downloader**, a local-first desktop app for downloading Dukascopy historical market data in large ranges, including 20+ years where available.

## Repo layout
This repository is expected to use a monorepo structure similar to:

- `apps/desktop` — frontend and desktop shell
- `apps/api` — local Node/TypeScript API
- `workers/java-fetch-worker` — Java worker that talks to Dukascopy
- `packages/shared-types` — shared TypeScript types
- `packages/shared-utils` — shared utility code
- `storage/` — local job output, manifests, temp files, and logs
- `docs/` — product and technical documentation
- `tests/` — integration, smoke, and regression tests

If the repository structure changes, keep this file aligned with the actual layout.

## Core operating rules
- Work in **small, reviewable increments**.
- Implement **one sprint or one ticket at a time**.
- Do **not** change unrelated files.
- Prefer the **simplest correct solution**.
- Keep changes **resumable, testable, and reversible**.
- Never silently overwrite user data or existing output files.
- If a requirement is unclear, **stop and ask** before making broad assumptions.

## Product constraints
- The app must support **chunked downloads** for long historical ranges.
- The app must be **local-first** and save data to the user’s chosen folder.
- The system must be able to **resume** interrupted jobs.
- Downloads should be streamed to disk rather than loaded fully into memory.
- The UI must remain responsive during long-running jobs.

## Preferred architecture
- **Frontend:** Next.js + TypeScript + Tailwind
- **API:** Node.js + TypeScript
- **Worker:** Java 17+ for Dukascopy fetch logic
- **Database:** SQLite
- **Export formats:** CSV first, Parquet where appropriate

## Expected module boundaries
### Frontend (`apps/desktop`)
- Keep UI components presentational where possible.
- Move job logic into feature modules and hooks.
- Do not place download orchestration directly in page components.

### API (`apps/api`)
- Keep request validation, job orchestration, and persistence here.
- Keep database access behind repository/service layers.
- Do not call Dukascopy directly from the frontend.

### Worker (`workers/java-fetch-worker`)
- Keep all Dukascopy fetching and file streaming here.
- Keep chunk execution isolated from UI concerns.
- Return structured success/failure results for each chunk.

### Shared packages
- Keep reusable types and pure helpers here.
- Do not add app-specific logic to shared packages.

## Repository workflow
Before coding:
1. Read this file.
2. Inspect the existing structure.
3. Identify the exact task scope.
4. Propose a short implementation plan.

While coding:
1. Make the smallest possible diff.
2. Keep changes within the requested scope.
3. Add or update tests alongside behavior changes.
4. Preserve backward compatibility unless the task explicitly changes it.

After coding:
1. Run the relevant tests.
2. Fix any failures introduced by the change.
3. Summarize what changed.
4. Mention any assumptions or follow-up work.

## Coding standards
- Use clear, explicit naming.
- Keep functions small and focused.
- Avoid duplication when it does not reduce clarity.
- Prefer typed interfaces and data models.
- Keep business logic out of UI components where possible.
- Add comments only where the code is not obvious.

## Data handling rules
- Treat downloaded market data as authoritative once saved.
- Do not delete or overwrite exported files unless the task explicitly requires it.
- Store metadata separately from raw downloads.
- Ensure every completed chunk is recorded in a manifest or job record.
- Preserve partial files with a clear temporary suffix until finalization.

## Error-handling rules
- Fail fast on invalid configuration.
- Retry transient fetch failures with bounded retries.
- Surface clear error messages for:
  - authentication failures
  - network interruptions
  - disk space issues
  - invalid date ranges
  - unsupported symbols or formats
- If a job fails midway, preserve completed output.

## Testing expectations
For any meaningful change:
- Add unit tests for pure logic.
- Add integration tests for persistence or API changes.
- Add worker tests for chunking or fetch behavior when applicable.
- Add regression tests for bugs you fix.

## Build and run commands
Use the repository’s actual commands if they already exist.

Typical commands for this project:
- Install: `pnpm install`
- Dev: `pnpm dev`
- Test: `pnpm test`
- Lint: `pnpm lint`
- Build: `pnpm build`
- Worker build: run the Java build command defined in the worker package

If the repo uses different scripts, update this file and keep the commands accurate.

## Task discipline
When asked to implement a feature:
- Stay inside the current sprint or ticket.
- Do not begin later sprint work early.
- Do not refactor unrelated areas.
- Do not introduce new libraries unless they are necessary and justified.

## Output discipline
When reporting progress:
- State what was changed.
- State what was tested.
- State what remains.
- Keep the summary concrete and brief.

## Human handoff
If progress depends on a decision, data source, credential, or environment detail that is missing, stop and ask for it rather than guessing.

