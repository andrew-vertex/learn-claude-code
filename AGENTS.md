# Repository Guidelines

## Project Structure & Module Organization

This is a teaching repository for Claude Code-style agent harnesses. Numbered lesson folders such as `s01_agent_loop/` through `s20_comprehensive/` contain each chapter's `code.py`, multilingual READMEs, and assets under `images/`. The `agents/` directory mirrors lesson implementations as Python scripts used by smoke tests. Long-form documentation lives in `docs/en`, `docs/zh`, and `docs/ja`. Reusable skill examples are in `skills/`. The Next.js site is in `web/`, with application code in `web/src`, generated content in `web/src/data/generated`, and extraction scripts in `web/scripts`.

## Build, Test, and Development Commands

- `pip install -r requirements.txt pytest`: install Python runtime and test dependencies.
- `python -m pytest tests -q`: run Python smoke and behavior tests.
- `python agents/s01_agent_loop.py`: run an individual lesson agent script; set required environment variables first, such as `MODEL_ID`.
- `cd web && npm ci`: install web dependencies exactly from the lockfile.
- `cd web && npm run dev`: extract lesson content, then start the local Next.js dev server.
- `cd web && npx tsc --noEmit`: type-check the web app.
- `cd web && npm run build`: extract content and build the production site.

## Coding Style & Naming Conventions

Keep lesson code deliberately minimal and readable. Python files use 4-space indentation, `snake_case` names, and focused functions that support the chapter's teaching point. Numbered lessons follow the `sNN_topic` pattern. TypeScript and React code live under `web/src`; use lowercase route folders, PascalCase React components, and existing Tailwind/Next.js patterns.

## Testing Guidelines

Python tests use `pytest` and live in `tests/` as `test_*.py`. Add or adjust tests when changing shared `agents/` behavior or fixing regressions. For chapter changes, verify the relevant `code.py` compiles and run `python -m pytest tests -q`. For web changes, run `npx tsc --noEmit` and `npm run build` from `web/`.

## Commit & Pull Request Guidelines

Recent history uses short, imperative commit subjects such as `Update provider model examples` and `Add CONTRIBUTING with scope and review policy`. Keep commits focused. PRs should link a specific issue, explain the teaching impact, and disclose AI assistance when used. When modifying a chapter's `code.py` or README, keep the chapter's English, Chinese, and Japanese READMEs synchronized, with matching code blocks.

## Security & Configuration Tips

Do not commit API keys, `.env` files, or provider tokens. Many lesson scripts read environment variables such as `MODEL_ID`, `ANTHROPIC_BASE_URL`, or provider credentials; document new variables near the lesson that requires them.
