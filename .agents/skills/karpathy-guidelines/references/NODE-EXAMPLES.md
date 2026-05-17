# Node Examples

Real-world Node.js examples demonstrating the four principles. These focus on Express routes, scraping jobs, background work, and server-side utilities.

---

## 1. Think Before Coding

### Example 1: Ambiguous Export Endpoint

**User Request:** "Add an endpoint to export applied jobs"

**❌ What Agents Do (Wrong Assumptions)**

```js
app.get('/api/applied-jobs/export', async (req, res) => {
  const jobs = await db.appliedJobs.findMany();
  res.setHeader('Content-Type', 'text/csv');
  res.send(convertToCsv(jobs));
});
```

**Problems:**
- Assumed CSV format
- Assumed exporting all jobs for all users
- Assumed synchronous download instead of queued export
- Ignored auth/privacy concerns

**✅ What Should Happen (Surface Assumptions)**

```text
Before implementing export, I need to clarify:

1. Should users export only their own applied jobs?
2. Do you want CSV, JSON, or downloadable Excel?
3. Is this a direct HTTP download or an async export job?
4. How large can the export get?

Simplest safe version: authenticated endpoint that exports only the current user's applied jobs as CSV.
If that's the intended scope, I can implement that first.
```

### Example 2: "Make Scraping Faster"

**User Request:** "Make the scraper faster"

**❌ What Agents Do (Pick Silently)**

```js
import PQueue from 'p-queue';
import Redis from 'ioredis';

const queue = new PQueue({ concurrency: 20 });
const redis = new Redis();

// 150 lines of concurrency, cache, and retry logic
```

**✅ What Should Happen**

```text
"Make the scraper faster" could mean:

1. Lower latency per page
   - reduce unnecessary waits / selectors

2. Higher throughput
   - increase concurrency carefully

3. Faster repeat runs
   - cache already-seen pages

4. Faster perceived workflow
   - start saving jobs as each page finishes

Which bottleneck are you seeing today: browser waits, network time, or total run duration?
```

---

## 2. Simplicity First

### Example 1: Overbuilt Scraper Service

**User Request:** "Save scraped jobs to the database"

**❌ What Agents Do (Overengineered)**

```js
class JobPersistenceStrategy {
  async persist(job) {
    throw new Error('Not implemented');
  }
}

class PrismaPersistenceStrategy extends JobPersistenceStrategy {
  constructor(prisma, logger, metrics) {
    super();
    this.prisma = prisma;
    this.logger = logger;
    this.metrics = metrics;
  }

  async persist(job) {
    this.logger.info('Persisting job', job.url);
    this.metrics.increment('job.persist.start');
    return this.prisma.job.upsert({
      where: { url: job.url },
      update: job,
      create: job,
    });
  }
}

class JobPersistenceManager {
  constructor(strategy) {
    this.strategy = strategy;
  }

  async saveAll(jobs) {
    for (const job of jobs) {
      await this.strategy.persist(job);
    }
  }
}
```

**✅ What Should Happen (Simple)**

```js
async function saveJobs(prisma, jobs) {
  for (const job of jobs) {
    await prisma.job.upsert({
      where: { url: job.url },
      update: job,
      create: job,
    });
  }
}
```

### Example 2: Speculative API Flexibility

**User Request:** "Add a route to mark a job as applied"

**❌ What Agents Do (Too Many Features)**

```js
app.patch('/api/jobs/:id/apply', async (req, res) => {
  const { id } = req.params;
  const { status = 'applied', notes, rating, appliedAt, source = 'manual' } = req.body;

  // 70 lines of optional audit logs, notifications, analytics,
  // external webhook dispatch, and config-driven behavior
});
```

**✅ What Should Happen (Just What Was Asked)**

```js
app.patch('/api/jobs/:id/apply', async (req, res) => {
  const job = await prisma.job.update({
    where: { id: req.params.id },
    data: { status: 'applied' },
  });

  res.json(job);
});
```

---

## 3. Surgical Changes

### Example 1: Fixing a Route Bug Without Refactoring the API

**User Request:** "Fix the crash when `req.query.page` is missing"

**❌ What Agents Do (Too Much)**

```diff
-app.get('/api/jobs', async (req, res) => {
-  const page = Number(req.query.page);
-  const jobs = await listJobs({ page });
-  res.json({ jobs });
-});
+app.get('/api/jobs', async (req, res) => {
+  const page = Math.max(1, Number.parseInt(req.query.page ?? '1', 10));
+  const limit = Math.max(1, Number.parseInt(req.query.limit ?? '20', 10));
+  const sortBy = req.query.sortBy ?? 'createdAt';
+  const order = req.query.order === 'asc' ? 'asc' : 'desc';
+
+  const jobs = await listJobs({ page, limit, sortBy, order });
+
+  res.status(200).json({
+    success: true,
+    jobs,
+    pagination: { page, limit },
+  });
+});
```

**Problems:**
- Added pagination fields and response shape changes
- Added new query params
- Changed existing API contract

**✅ What Should Happen (Surgical)**

```diff
 app.get('/api/jobs', async (req, res) => {
-  const page = Number(req.query.page);
+  const page = Number(req.query.page || 1);
   const jobs = await listJobs({ page });
   res.json({ jobs });
 });
```

### Example 2: Logging Without Behavioral Drift

**User Request:** "Log scraper failures"

**❌ What Agents Do (Behavior Change Hidden Inside Logging Task)**

```diff
 async function scrapePage(url) {
   try {
     const html = await fetchHtml(url);
     return parseJobs(html);
   } catch (error) {
+    await saveScrapeFailure({ url, error: String(error) });
+    if (process.env.NODE_ENV === 'production') {
+      return [];
+    }
     throw error;
   }
 }
```

**Problems:**
- Quietly swallows errors in production
- Adds persistence dependency not requested

**✅ What Should Happen**

```diff
 async function scrapePage(url) {
   try {
     const html = await fetchHtml(url);
     return parseJobs(html);
   } catch (error) {
+    console.error('scrapePage failed', { url, error });
     throw error;
   }
 }
```

---

## 4. Goal-Driven Execution

### Example 1: Reproduce First

**User Request:** "The apply endpoint sometimes creates duplicates"

**❌ What Agents Do (Immediate Fix Without Proof)**

```js
app.patch('/api/jobs/:id/apply', async (req, res) => {
  const job = await prisma.job.upsert({
    where: { id: req.params.id },
    update: { status: 'applied' },
    create: { id: req.params.id, status: 'applied' },
  });

  res.json(job);
});
```

**✅ What Should Happen (Test + Verify)**

```js
it('does not create a second applied record for the same job', async () => {
  await request(app).patch('/api/jobs/123/apply');
  await request(app).patch('/api/jobs/123/apply');

  const records = await prisma.job.findMany({ where: { id: '123' } });
  expect(records).toHaveLength(1);
  expect(records[0].status).toBe('applied');
});
```

Then fix the route based on the actual data model.

### Example 2: Incremental Verification

**User Request:** "Add rate limiting to the API"

**❌ What Agents Do (Big-Bang Change)**

```text
I'll add Redis-backed rate limiting middleware with per-route config and monitoring.
```

**✅ What Should Happen (Verifiable Plan)**

```text
Plan:

1. Add basic in-memory rate limit middleware to one route
   Verify:
   - 6th request within the window returns 429

2. Apply it to the routes that need protection
   Verify:
   - protected routes are limited, internal health route is not

3. Run existing API tests
   Verify:
   - no route contracts changed unexpectedly

4. Only if needed, replace the store with Redis
   Verify:
   - counters remain shared across processes
```

---

## Anti-Patterns Summary

| Principle | Anti-Pattern | Fix |
|-----------|-------------|-----|
| Think Before Coding | Exports all applied jobs without clarifying scope or auth | Clarify format, scope, and ownership first |
| Simplicity First | Builds manager/strategy classes for one save loop | Start with one small function |
| Surgical Changes | Fixes missing page query by redesigning the whole API response | Patch only the defaulting logic |
| Goal-Driven | Adds rate limiting as a large undifferentiated change | Implement in small verifiable steps |

## Key Insight

Good Node code is not the most configurable server code. It is the smallest safe change that preserves contracts, avoids hidden behavior changes, and is easy to test end to end.
