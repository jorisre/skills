# dev-skills

Personal library of Claude skills for JavaScript/TypeScript and React development — enforces consistent code style across projects. Installable via `npx skills add`.

## Installation

```bash
npx skills add jorisre/dev-skills
```

Or clone for local development:

```bash
git clone https://github.com/jorisre/dev-skills.git
cd dev-skills
```

## Skills

| Skill | Description |
|-------|-------------|
| `typescript-style` | TypeScript conventions — types, imports, file structure |
| `react-style` | React conventions — components, hooks, structure |

## Development

Each skill lives in its own folder at the repo root, containing:
- `SKILL.md` — skill definition (frontmatter + instructions)
- `scripts/` (optional) — helper scripts

The root `skills.json` manifest lists all available skills.

## License

MIT
