# Security policy

## Supported versions

| Version          | Supported |
| ---------------- | --------- |
| `0.1.x`          | Yes       |
| Earlier versions | No        |

## Reporting a vulnerability

Please do not open a public issue for a suspected vulnerability. Private vulnerability reporting is enabled for this repository; submit a report through [GitHub Security Advisories](https://github.com/gokcenciftci/audit-fence/security/advisories/new). If that path is temporarily unavailable, contact the repository owner through [their GitHub profile](https://github.com/gokcenciftci) without publishing exploit details.

Include a minimal reproduction, affected version, impact, and any mitigation you have identified. You should receive an acknowledgement within seven days.

## Input-handling boundaries

AuditFence processes local source code files offline. v0.1 limits scanning to text-based code files, ignores `node_modules` and binary targets, operates entirely offline, and does not send source code or metadata to external servers. These controls reduce risk; they are not a substitute for reviewing untrusted code before placing it in privileged environments.
