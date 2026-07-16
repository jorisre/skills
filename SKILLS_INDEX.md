# Skills Index

This repository contains multiple skill sets for different development needs.

## Available Skill Sets

### 🎯 jorisre-kit
**Complete web development guidelines for TypeScript, React, and Tailwind**

My personal collection of coding preferences and conventions for:
- **TypeScript** — File naming, ternary patterns, function styles, type declarations
- **React** — Component patterns, import styles, render function guidelines
- **Tailwind** — Dynamic class management with `cn()` and CVA
- **Code Quality** — Readability first, simplicity over cleverness

**Install:**
```bash
npx skills add https://github.com/jorisre/skills --skill jorisre-kit
```

**Location in repo:** `/skills/jorisre-kit/`

---

## Structure

```
skills/
└── jorisre-kit/
    ├── SKILL.md (main index of all rules)
    └── rules/
        ├── typescript-style.md
        ├── typescript-ternary.md
        ├── typescript-function-style.md
        ├── typescript-interface-preference.md
        ├── react-style.md
        ├── react-component-wrapper.md
        ├── react-no-render-functions.md
        ├── tailwind-class-merging.md
        └── code-simplicity.md
```

---

## How to Use a Skill Set

1. **Add the repository as a source:**
   ```bash
   npx skills add https://github.com/jorisre/skills
   ```

2. **Install a specific skill set:**
   ```bash
   npx skills add https://github.com/jorisre/skills --skill jorisre-kit
   ```

3. **Use it in your projects:** Reference the skill set in your development workflow to enforce consistent coding patterns.

---

## Creating a New Skill Set

To add a new skill set to this repository:

1. Create a new directory under `/skills/YOUR_SKILL_SET_NAME/`
2. Add a `SKILL.md` file with:
   - Frontmatter with `name` and `description`
   - Main index of all rules with links to rule files
3. Create a `/rules/` subdirectory with individual markdown files for each rule
4. Update this `SKILLS_INDEX.md` with the new skill set

**Example structure:**
```
skills/
└── your-skill-set/
    ├── SKILL.md
    └── rules/
        ├── rule-1.md
        ├── rule-2.md
        └── rule-3.md
```

---

## License

MIT
