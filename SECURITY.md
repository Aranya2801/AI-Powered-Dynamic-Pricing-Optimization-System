# Security Policy

## Supported Versions
| Version | Supported |
|---------|-----------|
| 1.x.x   | ✅ |

## Reporting a Vulnerability
**Do not open public issues for security vulnerabilities.** Email **security@dynamic-pricing.ai** with:
- Vulnerability type and affected file/endpoint
- Reproduction steps
- Potential impact

Response within 48 hours; critical patches within 7 days.

## Security Practices in This Repo
- JWT tokens with role-based access control (RBAC) — see `app/core/security.py`
- Passwords hashed with bcrypt, never stored in plaintext
- No API keys hardcoded — all secrets via environment variables (see `.env.example`)
- Rate limiting on auth endpoints via `slowapi`
- SQL injection protected via parameterized queries / SQLAlchemy ORM
- Default demo credentials (`admin`/`admin123`) **must be changed** before any production deployment
