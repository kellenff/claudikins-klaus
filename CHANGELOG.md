# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.2.0] - 2026-05-15

### Added

- Aron agent: argumentation-auditing counterpart to Klaus, focused on structured argument analysis and sparring
- `argument-audit` skill: methodology for auditing arguments via Argdown AST traversal, scheme matching, and Dung extensions
- `/aron` command: invoke Aron for an argument audit pass
- `/aron-spar` command: invoke Aron in dialectical sparring mode
- Four `argument-audit` references: `argdown-ast-fixture.md`, `audit-report-template.md`, `methodology-references.md`, `walton-schemes.md`
- Ten audit fixtures under `tests/fixtures/audit/` covering hidden-assumption, equivocation, unsupported-leap, corpus-contradiction, and a sparring trace (paired `.md` / `.expected.md` plus a multi-file corpus subdirectory)

### Changed

- `plugin.json` keywords expanded with `argument-audit` and `aron` (existing keywords preserved)

### Notes

- Aron quartet is additive; the Klaus trio (agent + skill + command) is untouched
- Aron's `argument-audit` skill depends on the `@casualtheorics/argdown-plugin` MCP server (parse / export_json / dung_extensions tools)
- No breaking changes; minor version bump per SemVer

## [1.0.1] - 2026-01-20

### Fixed

- Agent: Added missing required `name` field
- Skill: Fixed name from `Rigorous Debugging` to `debugging` (lowercase, matches directory)
- Skill: Description CSO-optimized (now starts with "Use when...")
- Standardized plugin.json metadata format

## [1.0.0] - 2026-01-14

### Added

- Initial release
- Klaus agent: Germanic debugging excellence with Pong commentary
- Rigorous 8-phase debugging methodology skill
- /klaus command for summoning when truly doomed
