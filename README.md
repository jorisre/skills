# dev-skills

Personal Claude skills library for consistent JavaScript/TypeScript and React code style.

## Install

```bash
npx skills add jorisre/dev-skills
```

## Skills (9 total)

**TypeScript (4)**
- `typescript-style` — Kebab-case files/dirs, framework conventions
- `typescript-ternary` — Explicit ternaries, no short-circuit `&&`
- `typescript-function-style` — Function declarations vs arrows
- `typescript-interface-preference` — Prefer `interface` over `type`

**React (3)**
- `react-style` — React imports via `React.XXX`
- `react-component-wrapper` — Props extending, `cn()` for className
- `react-no-render-functions` — Extract components, no render functions

**Styling (1)**
- `tailwind-class-merging` — Dynamic classes with `cn()`, CVA for variants

**General (1)**
- `code-simplicity` — Readability first, simple and clear code

## Structure

Each skill folder contains:
- `SKILL.md` — Rules and examples
- Optional: `scripts/` for helper utilities

All skills in `skills.json` manifest.

## Local Development

```bash
git clone https://github.com/jorisre/dev-skills.git
cd dev-skills
```

## License

MIT
