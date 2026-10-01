# AT5 Skills — Frontend Stack for Claude Code

A collection of agent skills documenting the **AT5 frontend architecture** — a modern, opinionated stack built for production-grade React applications with accessibility, dark mode, and type safety as first-class concerns.

## Install

```bash
npx skills add allysontsoares/at5-skills
```

Or install individual skills:

```bash
npx skills add allysontsoares/at5-skills/at5-frontend-stack
npx skills add allysontsoares/at5-skills/at5-ui-components
npx skills add allysontsoares/at5-skills/at5-nicemodal
npx skills add allysontsoares/at5-skills/at5-toasts
npx skills add allysontsoares/at5-skills/at5-radix-colors-tailwind
npx skills add allysontsoares/at5-skills/at5-wetrakr-api
```

## Skills

### [`at5-frontend-stack`](./at5-frontend-stack/SKILL.md)
Master guide and orchestrator for the full AT5 stack. Start here. Covers Vite 8+, React 19, React Compiler, Tailwind CSS v4, Base UI, ofetch, NiceModal, monorepo structure, responsive layout, and accessibility standards.

### [`at5-ui-components`](./at5-ui-components/SKILL.md)
Complete design system component catalog with production-ready Button, Modal, Select, Input, Textarea, Card, Badge, Accordion, and FormField — all built with Base UI primitives and Tailwind CSS v4. Includes the `render` prop pattern (Base UI's alternative to `asChild`).

### [`at5-nicemodal`](./at5-nicemodal/SKILL.md)
Declarative modal and form dialog architecture using NiceModal + React Hook Form + Zod. Features `createFormDialog` (with sidebar/tab layout, dirty-form close guard) and `createAlertDialog` helpers.

### [`at5-toasts`](./at5-toasts/SKILL.md)
Imperative toast notification system built on Base UI primitives. Supports sync, async promise, and CSS Anchor-positioned toasts. Zero external dependencies beyond Base UI.

### [`at5-radix-colors-tailwind`](./at5-radix-colors-tailwind/SKILL.md)
Design token architecture integrating `@radix-ui/colors` with Tailwind CSS v4 `@theme`. Covers the 12-step color scale semantics, brand color customization, `.dark` class-based dark mode, and `tailwind.config.js` mapping.

### [`at5-wetrakr-api`](./at5-wetrakr-api/SKILL.md)
Reference for the WeTrakr public REST API at `api.wetrakr.com`: OAuth and PKCE, sync and journal, scrobble, tracking, lists, comments, catalog, and rate limits. Snapshot of the public docs on 2026-10-01, changelog 1.0.7. The same skill is also published on its own at [allysontsoares/wetrakr-api](https://github.com/allysontsoares/wetrakr-api). It documents that API, separate from the frontend stack above.

## Stack

| Tool | Role |
|------|------|
| Vite 8+ | Build tool |
| React 19 + React Compiler | UI framework |
| Tailwind CSS v4 | Styling |
| Base UI (`@base-ui/react`) | Headless components |
| Radix Colors | Color tokens |
| Zod | Schema validation |
| React Hook Form | Form management |
| ofetch | HTTP client |
| NiceModal | Declarative modals |

## License

MIT
