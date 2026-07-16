# dev-skills

Personal Claude skills library for consistent JavaScript/TypeScript and React code style.

## Install

```bash
npx skills add jorisre/dev-skills
```

Then use skills like `@jorisre/typescript-style`, `@jorisre/react-style`, etc.

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

All rules are organized under `skills/`:
- `skills/SKILL.md` — Unified index with descriptions and links to all 9 rules
- `skills/rules/` — Individual markdown files for each rule

The `skills/SKILL.md` index serves as the source of truth for discovering available rules.

## Local Development

```bash
git clone https://github.com/jorisre/dev-skills.git
cd dev-skills
```

## License

MIT
