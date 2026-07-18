# AuditFence

[![CI](https://github.com/gokcenciftci/audit-fence/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/gokcenciftci/audit-fence/actions/workflows/ci.yml)
[![CodeQL](https://github.com/gokcenciftci/audit-fence/actions/workflows/codeql.yml/badge.svg?branch=main)](https://github.com/gokcenciftci/audit-fence/actions/workflows/codeql.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

AuditFence analyzes source code files for logic flaws, security vulnerabilities, unhandled async promises, debug leakage, and memory anti-patterns. It is a deterministic, local-first CI gate and CLI tool: it does not send your code to external cloud providers or remote third-party services.

> Part of the **Fence Security & Quality Suite** (`SchemaFence`, `ActionFence`, `EnvFence`, `AuditFence`).

> v0.1 is intentionally narrow. It favors predictable behavior and zero-dependency heuristic analysis over claiming complete AST coverage.

## Why AuditFence?

Code reviews are critical for catching subtle logic traps before code reaches production, but automated static analysis tools often require complex remote cloud pipelines or heavy external dependencies.

AuditFence provides an offline, local-first audit engine that runs in under 20ms. It uses a modular provider pattern featuring a zero-dependency AST heuristic analyzer and an optional integration for local Ollama LLM models (`qwen2.5-coder:1.5b`), ensuring complete code privacy and fast feedback in CI automation.

## Quick start

AuditFence is not published to npm yet. Run it from a clone:

```bash
git clone https://github.com/gokcenciftci/audit-fence.git
cd audit-fence
npm ci
npm run build

node dist/cli.js examples/insecure-code.ts
```

Example output:

```text
🔍 AuditFence Security & Quality Report
Provider: HEURISTIC
----------------------------------------
[ERROR] examples/insecure-code.ts:4
  Rule: debug-leak
  Message: Leftover "debugger" statement found.
  Snippet: debugger;
  Suggestion: Remove debugger statement before committing to production.

[WARNING] examples/insecure-code.ts:5
  Rule: debug-leak
  Message: Console debug statement detected in production code.
  Snippet: console.log("Processing input:", input);
  Suggestion: Remove console log or replace with structured logging.

[ERROR] examples/insecure-code.ts:10
  Rule: eval-injection
  Message: Critical security risk: "eval()" call detected.
  Snippet: const unsafeVal = eval(input);
  Suggestion: Refactor code to avoid eval(). Use static parsers or JSON.parse().
```

For CI-friendly machine output, add `--format json`.

## CI contract

```bash
node dist/cli.js src/ --format json
```

| Exit code | Meaning                                                                                                 |
| --------- | ------------------------------------------------------------------------------------------------------- |
| `0`       | Audit completed; no critical security errors found.                                                     |
| `2`       | Audit completed; one or more error-level vulnerabilities were detected, or invalid flags were supplied. |

JSON output is written to standard output; diagnostics are written to standard error. Finding order is deterministic: file path, line number, rule ID, then severity.

## Rules implemented in v0.1

AuditFence enforces static logic and security rules out-of-the-box via its built-in heuristic provider:

| Rule ID                   | Severity      | Description                                                                        |
| ------------------------- | ------------- | ---------------------------------------------------------------------------------- |
| `unhandled-async-promise` | Error         | Detects floating promises or missing `await`/`.catch()` in asynchronous execution. |
| `debug-leak`              | Warning/Error | Flags leftover `debugger`, `console.log`, `console.trace`, or `alert()` calls.     |
| `logic-null-deref`        | Warning       | Detects deep property access without optional chaining (`?.`).                     |
| `memory-leak-listener`    | Warning       | Identifies un-removed `addEventListener` or `setInterval` calls missing cleanup.   |
| `eval-injection`          | Error         | Detects dangerous dynamic code evaluation via `eval()` or `new Function()`.        |

## Provider engine architecture

1. **`HeuristicProvider`** (Default): Built-in zero-dependency AST/regex rule engine running in < 20ms with zero RAM overhead.
2. **`OllamaProvider`** (Optional): Connects to local Ollama API (`http://localhost:11434`) for lightweight local models (`qwen2.5-coder:1.5b`, `llama3.2:1b`), featuring automatic fallback to `HeuristicProvider` if Ollama is offline.
3. **`MockProvider`**: Deterministic test engine for unit testing and CI verification.

## Supported environment and limits

- Designed for Node.js 20 or newer.
- Scans `.ts`, `.tsx`, `.js`, `.jsx`, `.mjs`, `.cjs`, `.py`, `.go`, and `.rs` source files.
- Automatically skips `node_modules`, `dist`, `coverage`, `.git`, and dotfiles.
- Operates entirely offline; no external network requests or remote API calls are performed.

## Architecture

See [the architecture documentation](docs/architecture.md) for details on the provider pattern and AST analysis pipeline.

## License

[MIT](LICENSE) © 2026 Gökçen Çiftci
