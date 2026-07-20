# AGENTS.md

## Cursor Cloud specific instructions

### What this project is
A single, pure-Python **AI Automation Storage Test Platform** library under `ai-test-system/src/`
(modules: `agent`, `core`, `flow`, `knowledge`, `nodes`, `scheduler`, `storage`, `tools`).
All backing services (PostgreSQL, Redis, embeddings/vector store) are **mocked/simulated in-process** —
there is no web server, no database, and no external service to start.

### Runtime / dependencies
- Requires **Python 3.12** (uses the standard library only — there are no third-party packages, no
  `requirements.txt`/`pyproject.toml`, and nothing to `pip install`).
- The update script creates the `ai_test_system` symlink (see next section). No other setup is needed.

### Import gotcha (important)
The package directory is `ai-test-system` (hyphen) but all code imports it as `ai_test_system`
(underscore), which is not a valid Python module name. A repo-root symlink `ai_test_system -> ai-test-system`
makes imports resolve. This symlink is committed and also (re)created idempotently by the startup
update script (`ln -sfn ai-test-system ai_test_system`). Run scripts from the repo root so the symlink
is on `sys.path`.

### How to run (there is no README)
Run the standalone demo/test scripts from the repo root, e.g. `python3 test_tool_layer.py`.

### Lint / tests / build
- **Build:** none (interpreted).
- **Lint:** no linter is configured. Syntax can be verified with `python3 -m compileall ai-test-system/src`.
- **Tests:** the `test_*.py` files at the repo root are standalone `asyncio.run(...)` demo scripts, not a
  pytest suite. Run them directly with `python3 <file>`.

### Known pre-existing code bugs (NOT environment issues — do not "fix" as part of setup)
These scripts run to completion: `test_tool_layer.py`, `test_knowledge_layer.py`, `test_execution_nodes.py`.
These fail due to bugs in the repo's own code, unrelated to environment setup:
- `test_agent_engine.py` — `ShortTermMemory.search` is called without `await` (`engine.py`).
- `test_flow_engine_corrected.py` / `test_state_store.py` — wrong relative imports (`..core.types`
  instead of `...core.types`) inside `flow/models` and `storage/models`.
- `test_flow_engine.py` — imports `from src.flow...` instead of `from ai_test_system.src.flow...`.
- `test_scheduler.py` — indexes the priority queue's internal heap tuples as if they were objects.
