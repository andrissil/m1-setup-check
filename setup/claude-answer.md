make test runs a single command (Makefile:10-11):

test:
      pytest -q

That runs pytest in quiet mode (-q, which prints a dot per test and a short summary) from the repo root. The repo has no pytest.ini, pyproject.toml or setup.cfg, so pytest uses its defaults and collects every test_*.py file it finds. Right now the only one is tests/test_smoke.py, which has two tests:

1. test_openapi_document_can_be_loaded: parses docs/openapi.yaml with yaml.safe_load, then checks that the openapi version starts with 3. and that paths is not empty. These are basic structure checks, not full OpenAPI validation.
2. test_participant_files_are_present: checks that these files exist: .claude/settings.json, .devcontainer/devcontainer.json, CLAUDE.md, Makefile, tracker/CR-2.md and tracker/README.md. If any are missing, it fails with the message Trūkst faili: … ("Missing files: …").

How it relates to the other targets:
- make lint-contract is the stricter check on the API contract. It runs tools/lint_contract.py against docs/openapi.yaml. Per CLAUDE.md, run it whenever you change the contract.
- make verify-setup checks the environment: Python version, the required packages (with Pydantic v2), the Claude CLI version, and that setup/claude-answer.md exists. Then it runs only tests/test_smoke.py. This is the target CI runs (.github/workflows/ci.yml). CI does not run make test.

Because pytest finds tests automatically, any new test_*.py files you add will run under make test with no Makefile change.