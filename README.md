# skills

Repository of Claude skill sets for code style, conventions, and development practices.

## Quick Start

### Install a Skill Set

```bash
npx skills add https://github.com/jorisre/skills --skill jorisre-guidelines
```

### Available Skill Sets

- **`jorisre-guidelines`** — My personal preferences for TypeScript, React, Tailwind, and code quality

For a complete list, see [SKILLS_INDEX.md](SKILLS_INDEX.md).

## What's Inside

### jorisre-guidelines

9 coding conventions covering:

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

## Repository Structure

```
skills/
└── jorisre-guidelines/
    ├── SKILL.md (index of all rules)
    └── rules/ (individual rule files)
```

To explore a skill set, read `/skills/{skill-set-name}/SKILL.md`.

## Creating Your Own Skill Set

See [SKILLS_INDEX.md](SKILLS_INDEX.md#creating-a-new-skill-set) for how to add a new skill set to this repository.

## Local Development

```bash
git clone https://github.com/jorisre/skills.git
cd skills
```

## License

MIT
