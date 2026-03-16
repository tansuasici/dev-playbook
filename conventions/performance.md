# Performance Standards

## Response Time Budgets

| Endpoint Type | Target (p95) | Max (p99) |
|---------------|-------------|-----------|
| Simple read (GET by ID) | < 100ms | < 200ms |
| List with pagination | < 200ms | < 500ms |
| Search with filters | < 300ms | < 800ms |
| Write (POST/PUT/PATCH) | < 200ms | < 500ms |
| File upload | < 2s | < 5s |
| Report generation | < 5s | < 10s (or async) |

If an operation exceeds 10 seconds, make it **asynchronous** — return `202 Accepted` and process in the background.

## Database Performance

### N+1 Query Prevention

The most common performance bug. Loading a list then querying each item individually.

```csharp
// BAD — N+1: 1 query for courses + N queries for instructors
var courses = await _context.Courses.ToListAsync();
foreach (var course in courses)
{
    course.Instructor = await _context.Users.FindAsync(course.InstructorId);
}

// GOOD — eager loading: 1 query with JOIN
var courses = await _context.Courses
    .Include(c => c.Instructor)
    .ToListAsync();
```

```typescript
// BAD — N+1
const courses = await prisma.course.findMany();
for (const course of courses) {
  course.instructor = await prisma.user.findUnique({ where: { id: course.instructorId } });
}

// GOOD — include relation
const courses = await prisma.course.findMany({
  include: { instructor: true },
});
```

```python
# BAD — N+1
courses = session.query(Course).all()
for course in courses:
    print(course.instructor.name)  # lazy load triggers N queries

# GOOD — eager load
courses = session.query(Course).options(joinedload(Course.instructor)).all()
```

### Query Rules

- **Always paginate** list endpoints — never return unbounded results
- **Select only needed columns** — don't `SELECT *` when you need 3 fields
- **Index WHERE, JOIN, ORDER BY columns** — see [database.md](../architecture/database.md)
- **Use EXPLAIN ANALYZE** on slow queries to understand the execution plan
- **Set query timeouts** — a runaway query shouldn't take down the database

```csharp
// Select only what you need
var courseSummaries = await _context.Courses
    .Where(c => c.TenantId == tenantId && !c.IsDeleted)
    .Select(c => new CourseSummaryDto
    {
        Id = c.Id,
        Title = c.Title,
        StudentCount = c.Enrollments.Count,
    })
    .ToListAsync();
```

## Caching Strategy

### Cache Layers

| Layer | What to Cache | TTL | Invalidation |
|-------|--------------|-----|-------------|
| **Browser** | Static assets (JS, CSS, images) | Long (1 year with hash) | New deployment |
| **CDN** | Public pages, images, API responses | Minutes to hours | Purge on update |
| **Application** | DB query results, computed values | Seconds to minutes | Write-through or event-based |
| **Database** | Query plans, prepared statements | Automatic | Database manages this |

### When to Cache

- Data that's read far more than written (course catalog, user profiles)
- Expensive computations (analytics, aggregations, reports)
- External API responses (with appropriate TTL)

### When NOT to Cache

- User-specific sensitive data (unless per-user cache with auth)
- Rapidly changing data (real-time feeds, live counters)
- Data that must be consistent (financial transactions, enrollment counts during registration)

### Implementation

```csharp
// .NET — IDistributedCache with Redis
public async Task<CourseDto?> GetCourseAsync(Guid courseId)
{
    var cacheKey = $"course:{courseId}";
    var cached = await _cache.GetStringAsync(cacheKey);

    if (cached != null)
        return JsonSerializer.Deserialize<CourseDto>(cached);

    var course = await _repository.GetByIdAsync(courseId);
    if (course == null) return null;

    var dto = course.ToDto();
    await _cache.SetStringAsync(cacheKey, JsonSerializer.Serialize(dto),
        new DistributedCacheEntryOptions { AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(5) });

    return dto;
}

// Invalidate on write
public async Task UpdateCourseAsync(Guid courseId, UpdateCourseRequest request)
{
    await _repository.UpdateAsync(courseId, request);
    await _cache.RemoveAsync($"course:{courseId}");
}
```

```typescript
// TypeScript — Redis cache helper
async function cached<T>(key: string, ttlSeconds: number, fn: () => Promise<T>): Promise<T> {
  const hit = await redis.get(key);
  if (hit) return JSON.parse(hit);

  const result = await fn();
  await redis.setex(key, ttlSeconds, JSON.stringify(result));
  return result;
}

// Usage
const course = await cached(`course:${id}`, 300, () => db.course.findUnique({ where: { id } }));
```

### Cache Key Convention

```
<entity>:<id>                    → course:abc-123
<entity>:list:<tenant>:<hash>    → course:list:tenant-1:page1-size20
<entity>:count:<tenant>          → course:count:tenant-1
```

## Frontend Performance

### Core Web Vitals Targets

| Metric | Target | What It Measures |
|--------|--------|-----------------|
| **LCP** (Largest Contentful Paint) | < 2.5s | Loading performance |
| **INP** (Interaction to Next Paint) | < 200ms | Interactivity |
| **CLS** (Cumulative Layout Shift) | < 0.1 | Visual stability |

### Rules

- **Lazy load** below-the-fold content and routes
- **Optimize images** — use WebP/AVIF, serve responsive sizes, always set width/height
- **Minimize JavaScript** — code split by route, tree-shake unused imports
- **Prefetch** likely navigation targets
- **Use SSR/SSG** for content pages, CSR for interactive dashboards

```typescript
// Next.js — Dynamic import for heavy components
import dynamic from 'next/dynamic';

const HeavyChart = dynamic(() => import('@/components/HeavyChart'), {
  loading: () => <ChartSkeleton />,
  ssr: false,
});

// Image optimization
import Image from 'next/image';

<Image
  src="/hero.jpg"
  alt="Course hero image"
  width={1200}
  height={630}
  priority  // LCP image — load immediately
/>
```

## Background Jobs

Move heavy operations out of the request path:

| Operation | Approach |
|-----------|----------|
| Sending emails | Queue → Background worker |
| Generating reports | Queue → Return job ID → Poll/webhook for result |
| Processing uploads | Queue → Worker → Notify on completion |
| Analytics aggregation | Scheduled job (cron) |
| Cache warming | Scheduled job or event-driven |

```csharp
// .NET — Return 202 and process in background
[HttpPost("reports/generate")]
public async Task<IActionResult> GenerateReport([FromBody] ReportRequest request)
{
    var jobId = await _backgroundJobs.EnqueueAsync<ReportGenerator>(
        x => x.GenerateAsync(request));

    return Accepted(new { jobId, statusUrl = $"/api/reports/jobs/{jobId}" });
}
```

## Anti-Patterns

- **No pagination** — Returning 10,000 rows because "it works in dev"
- **SELECT *** — Fetching 20 columns when you need 3
- **Cache everything** — Caching data that changes every second or is accessed once
- **Premature optimization** — Profile first, optimize the bottleneck, not what you guess
- **Synchronous heavy operations** — Generating PDFs or sending emails in the request handler
- **No query monitoring** — You can't fix what you can't see. Log slow queries (> 100ms)
