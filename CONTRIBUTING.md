# Contributing

Thanks for contributing to AuditFence. The project prioritizes deterministic behavior, zero cloud dependencies, and fast heuristic code auditing.

## Prerequisites

- Node.js 20 or newer
- npm 10 or newer

## Local workflow

```bash
npm ci
npm run validate
```

`validate` runs ESLint, Prettier verification, strict TypeScript checks, Vitest with enforced coverage thresholds, a production build, and built-CLI smoke checks.

## Adding an audit rule

1. Keep rule analysis pure and place it in the `src/rules/` layer.
2. Add a focused regression test in `test/rules.test.ts` that asserts the rule ID, line number, and suggestion.
3. Update [the rule reference](docs/audit-rules.md) and README scope.
4. Preserve deterministic ordering; do not add timestamps, random IDs, or non-reproducible paths to issues.
5. Run `npm run validate` before opening a pull request.

## Pull requests

Use a clear, imperative commit message such as `feat: detect unhandled promise rejections` or `fix: support tsx file extensions`. Keep a pull request focused, explain security impact, and include the validation output in its description.

Do not add credentials, production secrets, or sensitive customer data to tests, issues, or screenshots. See [SECURITY.md](SECURITY.md) for vulnerability reporting.
