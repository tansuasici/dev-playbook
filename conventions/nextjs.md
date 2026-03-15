# Next.js Conventions

## Architecture: App Router

**App Router only** — no `pages/` directory.

```
src/
├── app/
│   ├── [locale]/
│   │   ├── (portal-student)/
│   │   ├── (portal-instructor)/
│   │   ├── (portal-admin)/
│   │   ├── (auth)/
│   │   ├── layout.tsx
│   │   └── page.tsx
│   ├── api/            ← Route handlers (if needed)
│   └── globals.css
├── components/
│   ├── ui/             ← shadcn/ui primitives
│   ├── shared/         ← Reusable across portals
│   └── features/       ← Feature-specific components
├── lib/
│   ├── api/            ← API client functions
│   ├── hooks/          ← Custom React hooks
│   ├── stores/         ← Zustand stores
│   └── utils/          ← Pure utility functions
├── types/              ← TypeScript type definitions
└── messages/           ← i18n translation files (next-intl)
```

## Component Rules

1. **Server Components by default** — Only add `"use client"` when you need interactivity, browser APIs, or hooks
2. **Colocate** — Keep related files together (component + its types + its styles)
3. **One component per file** — Exception: small helper components used only by the parent
4. **Export named, not default** — `export function CourseCard()` not `export default function`

## State Management

| Type | Tool | When |
|------|------|------|
| Server state | TanStack Query | API data fetching, caching, mutations |
| Client state | Zustand | UI state (sidebar open, theme, filters) |
| Form state | React Hook Form + Zod | All forms, always validated |
| URL state | `useSearchParams` | Filters, pagination, sorting |

**Never** use React Context for state that changes frequently. Zustand or URL state instead.

## Naming

| Element | Convention | Example |
|---------|------------|---------|
| Files | kebab-case | `course-card.tsx` |
| Components | PascalCase | `CourseCard` |
| Hooks | camelCase with `use` prefix | `useCourseData` |
| Stores | camelCase with `use` + `Store` | `useSidebarStore` |
| Types/Interfaces | PascalCase | `CourseResponse` |
| Constants | SCREAMING_SNAKE | `MAX_FILE_SIZE` |
| CSS classes | Tailwind utilities | — |

## Styling

- **Tailwind CSS** for everything — no CSS modules, no styled-components
- **shadcn/ui** for base components — customize via Tailwind, don't fork
- **`cn()` helper** for conditional classes (from `lib/utils`)
- **No inline styles** — Use Tailwind classes

## i18n (Internationalization)

- **next-intl** for all user-facing text
- Never hardcode UI text — always use `t('key')`
- Translation files: `messages/tr.json`, `messages/en.json`
- Default locale: Turkish (`tr`)

## Data Fetching

```typescript
// Server Component — fetch directly
async function CoursePage({ params }: { params: { id: string } }) {
  const course = await getCourse(params.id);
  return <CourseDetail course={course} />;
}

// Client Component — TanStack Query
function CourseList() {
  const { data, isLoading } = useQuery({
    queryKey: ['courses'],
    queryFn: () => apiClient.getCourses(),
  });
}
```

## Forms

Always: React Hook Form + Zod schema

```typescript
const schema = z.object({
  title: z.string().min(1, 'Required').max(200),
  description: z.string().optional(),
});

type FormData = z.infer<typeof schema>;
```

## Testing

- **Vitest** for unit tests
- **Playwright** for E2E tests
- Test user interactions, not implementation details
- Mock API calls, not components
