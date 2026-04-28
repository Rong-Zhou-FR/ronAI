# Agentic AI coder ESSENTIAL: `AGENTS.md`

## start small: 300-500 golden tokens(tok)

```md
# [Project Name]

## Tech Stack
- Language: [primary language]
- Framework: [main framework]
- Database: [database]

## Key Rules
- [Most important rule]
- [Second most important rule]
- [Third most important rule]

## When Stuck
Ask before making architectural changes.
```

## grow organically

- observe AI over 1-2 weeks
  - repeated mistakes > add rules against
  - critical optimization need > add performance guidelines
- target: 1 500 > 2 000 tok for repo wide instructions

## organise sensibly

- large contexts dilute attention
- AI doesn't need to know about `encik.py` when it is working on `vorto.py`
- use nested `AGENTS.md`
```
repo/
├── AGENTS.md                    # Universal team standards
├── packages/
│   ├── frontend/
│   │   └── AGENTS.md           # Frontend-specific rules
│   ├── backend/
│   │   └── AGENTS.md           # Backend-specific rules
│   └── mobile/
│       └── AGENTS.md           # Mobile-specific rules
```

## review and reflect monthly

- remove stale
- combine like
- optimise organization
