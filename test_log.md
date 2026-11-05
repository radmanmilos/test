# AIR Test Log

## Environment
- OS: Windows 11
- Node: v24.15.0
- AIR version: 0.2.0
- Test project: E:\GitHub\test (C# console app)
- Adapters available: aider, goose, opencode, kilocode, codex, github-copilot, ollama
- Ollama models: qwen2.5-coder:7b, deepseek-r1:1.5b, qwen3.5:4b

## Test 1: air status
- Command: `air status`
- Result: 7 tools installed, 0 cooldowns, 11 successes, 1 failure
- Status: PASS

## Test 2: air models
- Command: `air models`
- Result: aider (openrouter, cost 0), goose (openai, cost 2), ollama (local, cost 0, preferred)
- Status: PASS

## Test 3: air run "change greeting"
- Command: `air run "change the greeting in Program.cs from Hello World2 to Hello AIR"`
- Model chosen: aider (openrouter/gemma-4-31b-it:free) — rate limited
- Result: Reported SUCCEEDED_WITH_CHANGES but NO actual file change (false positive)
- Root cause: aider created .aider* files (untracked) counted as changes
- Status: FAIL (false positive) — FIXED (excluded .aider* from delta)

## Test 4: air chat "describe this repo"
- Command: `air chat` then "describe this repo"
- Model chosen: ollama (qwen2.5-coder:7b, preferred local)
- Result: Job started, completed with "Done."
- Status: PASS

## Findings
1. Write tasks fail when aider is rate-limited and no paid provider allowed. Failover to goose requires --allow-provider openai and OPENAI_API_KEY.
2. Ollama only handles read-only tasks (no fileEditing capability). Write tasks cannot use Ollama.
3. False-positive success from aider artifacts — FIXED.
4. Chat data loss on close — FIXED.
5. Packaging/import resolution — FIXED (check:dist passes).
6. MCP stdout purity — FIXED.
7. Free-only filter now includes Ollama (cost -1).
8. Documentation updated with install instructions and Ollama support.

## Recommendations
- Set OPENROUTER_API_KEY with BYOK for reliable write tasks.
- Or install Ollama + a coding model for offline use (read-only tasks).
- For write tasks with Ollama, a file-editing executor is needed (future work).
filePath">
E:\GitHub\test\test_log.md