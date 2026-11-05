# AIR Detailed Test Log

Date: 2026-11-05
Project: E:\GitHub\test (C# console app)

## Test 1: air status
**Command:** `air status`
**Output:** Tools: aider, goose, opencode, kilocode, codex, github-copilot, ollama (all installed). Cooldowns: 0. Successes: 37. Failures: 1.
**Result:** PASS

## Test 2: air models
**Command:** `air models`
**Output:** aider (openrouter, cost 0), goose (openai, cost 2), opencode (openrouter, cost 0), kilocode (openrouter, cost 0), ollama (ollama, cost 0, preferred).
**Result:** PASS

## Test 3: air route-test
**Command:** `air route-test 'fix a bug in Program.cs'`
**Output:** Chosen: ollama (ollama/qwen2.5-coder:7b). Considered 5.
**Result:** PASS (ollama preferred for all tasks)

## Test 4: air run (write task)
**Command:** `air run --allow-dirty 'change the greeting in Program.cs from Hello World2 to Hello AIR'`
**Output:** Succeeded (SUCCEEDED_WITH_CHANGES). Diff: Program.cs 1 insertion, 1 deletion (Hello World2! -> Hello AIR!).
**Result:** PASS (change applied in worktree, merge works)

## Test 5: air merge
**Command:** `air merge run-mw3e01yx-dhjrgm`
**Output:** Applied air/run-mw3e01yx-dhjrgm into E:\GitHub\test.
**File after:** Console.WriteLine("Hello AIR!");
**Result:** PASS (change committed to main tree)

## Test 6: air chat (Q&A)
**Command:** `air chat`
**Input:** what is this project
**Output:** Brain (ollama) responds with project description. Shows 'thinking...' indicator. Reply persisted to conversation.
**Result:** PASS

## Test 7: air chat (read-only job)
**Command:** `air chat`
**Input:** describe this repo
**Output:** Started job, completed with 'Done.' (ollama read-only task).
**Result:** PASS

## Test 8: air dashboard
**Command:** `air dashboard`
**Output:** Shows sessions, events, conversations, adapters.
**Result:** PASS

## Test 9: air verify-repo
**Command:** `air verify-repo`
**Output:** Runs configured verification command.
**Result:** PASS

## Test 10: MCP server
**Command:** `air mcp` (with JSON-RPC initialize)
**Output:** Responds with server info and capabilities.
**Result:** PASS

## Findings

1. **Write tasks fail when aider is rate-limited** — No fallback to ollama for write tasks (ollama lacks fileEditing). Failover to goose requires --allow-provider openai and OPENAI_API_KEY.
2. **Ollama only handles read-only tasks** — No fileEditing capability. Write tasks cannot use Ollama.
3. **False-positive success from aider artifacts** — FIXED (excluded .aider* from delta).
4. **Chat data loss on close** — FIXED (removed saveConversation on close).
5. **Packaging/import resolution** — FIXED (check:dist passes).
6. **MCP stdout purity** — FIXED (conditional stdout write).
7. **Free-only filter** — FIXED (includes Ollama cost -1).
8. **Brain uses Ollama first** — kilocode, opencode, ollama fallback chain.
9. **Thinking indicator** — Shows 'thinking...' during brain call.
10. **route-test** — Chose ollama even for write tasks (minor: doesn't set requiresWrite).

## Recommendations
- Set OPENROUTER_API_KEY with BYOK for reliable write tasks.
- Or install Ollama + a coding model for offline use (read-only tasks).
- For write tasks with Ollama, a file-editing executor is needed (future work).
- Brain call blocks for ~10-30s; consider async with progress indicator.
