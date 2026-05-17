# JS / TS Examples

Real-world JavaScript and TypeScript examples demonstrating the four principles. Each example shows what coding agents commonly do wrong and how to fix it.

---

## 1. Think Before Coding

### Example 1: Hidden Assumptions in a Utility

**User Request:** "Add a helper to format salary"

**❌ What Agents Do (Wrong Assumptions)**

```ts
export function formatSalary(amount: number, currency = 'USD') {
  return new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency,
    maximumFractionDigits: 0,
  }).format(amount);
}
```

**Problems:**
- Assumed all salaries should be rounded
- Assumed locale should be `en-US`
- Assumed input is always yearly salary
- Assumed currency formatting is the desired output instead of a compact display like `₹24 LPA`

**✅ What Should Happen (Surface Assumptions)**

```text
Before implementing, I need to clarify a few things:

1. Should salary be shown as full currency (`₹2,400,000`) or compact form (`24 LPA`)?
2. Is the input monthly, yearly, or mixed?
3. Should formatting depend on the viewer locale or stay fixed for all users?
4. Do we need to support salary ranges like `₹20L - ₹28L`?

Simplest version: a pure formatter for yearly numeric salary in INR.
If that's what you want, I can implement that first.
```

### Example 2: Multiple Interpretations in TypeScript

**User Request:** "Make job filtering smarter"

**❌ What Agents Do (Pick Silently)**

```ts
export function filterJobs(jobs: Job[], query: string) {
  return jobs.filter((job) => {
    const text = `${job.title} ${job.company} ${job.description}`.toLowerCase();
    return text.includes(query.toLowerCase());
  });
}
```

**Problems:**
- Assumed "smarter" means full-text substring matching
- Ignored company allowlist / denylist
- Ignored ranking or scoring
- Ignored whether filtering should be exact, fuzzy, or weighted

**✅ What Should Happen**

```text
"Make job filtering smarter" could mean a few different things:

1. Better matching quality
   - prioritize title and skills over description
   - estimated effort: low

2. Better ranking
   - add score = role_seniority + company_quality + tech_match
   - estimated effort: medium

3. Better UX
   - faster client-side filtering + highlighted matches
   - estimated effort: low

4. Better exclusion rules
   - hide consultancies / duplicates / irrelevant locations
   - estimated effort: low

Which one matters most right now?
```

---

## 2. Simplicity First

### Example 1: Overengineered Type Model

**User Request:** "Add a function to group jobs by company"

**❌ What Agents Do (Overengineered)**

```ts
interface GroupStrategy<T> {
  getKey(item: T): string;
}

class CompanyGroupStrategy implements GroupStrategy<Job> {
  getKey(item: Job) {
    return item.company.trim().toLowerCase();
  }
}

class Grouper<T> {
  constructor(private strategy: GroupStrategy<T>) {}

  group(items: T[]) {
    return items.reduce<Record<string, T[]>>((acc, item) => {
      const key = this.strategy.getKey(item);
      acc[key] ??= [];
      acc[key].push(item);
      return acc;
    }, {});
  }
}

export function groupJobsByCompany(jobs: Job[]) {
  const grouper = new Grouper(new CompanyGroupStrategy());
  return grouper.group(jobs);
}
```

**✅ What Should Happen (Simple)**

```ts
export function groupJobsByCompany(jobs: Job[]) {
  return jobs.reduce<Record<string, Job[]>>((acc, job) => {
    const key = job.company.trim().toLowerCase();
    acc[key] ??= [];
    acc[key].push(job);
    return acc;
  }, {});
}
```

**When to add complexity:** Only when grouping rules vary enough to justify strategies.

### Example 2: Speculative Type Safety

**User Request:** "Add a function to parse experience years"

**❌ What Agents Do (Too Much Too Early)**

```ts
type ExperienceUnit = 'years' | 'months';

type ExperienceValue = {
  min?: number;
  max?: number;
  unit: ExperienceUnit;
  source: 'title' | 'description' | 'manual';
  confidence: number;
};

class ExperienceParser {
  parse(text: string): ExperienceValue | null {
    // 80+ lines handling many speculative formats
    return null;
  }

  normalize(value: ExperienceValue) {
    // more abstraction
  }
}

export const experienceParser = new ExperienceParser();
```

**✅ What Should Happen (Just What Was Asked)**

```ts
export function parseExperienceYears(text: string): number | null {
  const match = text.match(/(\d+)\+?\s*(?:years|yrs)/i);
  return match ? Number(match[1]) : null;
}
```

---

## 3. Surgical Changes

### Example 1: Fixing a Small Bug Without Rewriting

**User Request:** "Fix the bug where empty search terms break filtering"

**❌ What Agents Do (Too Much)**

```diff
-export function filterJobs(jobs, term) {
-  return jobs.filter((job) =>
-    job.title.toLowerCase().includes(term.toLowerCase())
-  );
-}
+export function filterJobs(jobs: Job[], term: string) {
+  const normalized = term.trim().toLowerCase();
+
+  return jobs
+    .filter((job) => Boolean(job.title && job.company))
+    .map((job) => ({
+      ...job,
+      score: getJobScore(job),
+    }))
+    .filter((job) =>
+      `${job.title} ${job.company} ${job.description}`
+        .toLowerCase()
+        .includes(normalized)
+    )
+    .sort((a, b) => b.score - a.score);
+}
```

**Problems:**
- Added TypeScript annotations nobody asked for
- Changed filtering scope
- Added scoring and sorting
- Introduced unrelated logic

**✅ What Should Happen (Surgical)**

```diff
 export function filterJobs(jobs, term) {
+  if (!term || !term.trim()) {
+    return jobs;
+  }
+
   return jobs.filter((job) =>
     job.title.toLowerCase().includes(term.toLowerCase())
   );
 }
```

### Example 2: Style Drift in TypeScript

**User Request:** "Add logging when scraper fetch fails"

**❌ What Agents Do (Reformat Everything)**

```diff
-export async function fetchHtml(url) {
+export async function fetchHtml(url: string): Promise<string> {
+  console.info(`Fetching ${url}`)
   try {
-    const res = await fetch(url)
-    return await res.text()
+    const response = await fetch(url)
+    if (!response.ok) {
+      console.error(`Request failed for ${url} with ${response.status}`)
+      throw new Error(`HTTP ${response.status}`)
+    }
+
+    return await response.text()
   } catch (error) {
-    throw error
+    console.error(`Unexpected scraper error for ${url}`, error)
+    throw error
   }
 }
```

**Problems:**
- Added types, renamed variables, changed formatting
- Changed behavior for non-OK responses
- Mixed logging task with API behavior change

**✅ What Should Happen (Match Existing Style)**

```diff
 export async function fetchHtml(url) {
   try {
     const res = await fetch(url)
     return await res.text()
   } catch (error) {
+    console.error('fetchHtml failed', { url, error })
     throw error
   }
 }
```

---

## 4. Goal-Driven Execution

### Example 1: Test-First Bug Fix

**User Request:** "Sorting breaks when two jobs have the same score"

**❌ What Agents Do (Fix Without Reproducing)**

```ts
export function sortJobs(jobs: Job[]) {
  return [...jobs].sort((a, b) => b.score - a.score || a.title.localeCompare(b.title));
}
```

**✅ What Should Happen (Reproduce First)**

```ts
// 1. First write a test that reproduces the issue
it('keeps ordering deterministic when scores are equal', () => {
  const jobs = [
    { id: '1', title: 'Frontend Engineer', score: 100 },
    { id: '2', title: 'Backend Engineer', score: 100 },
    { id: '3', title: 'SDE 2', score: 90 },
  ];

  const result = sortJobs(jobs);

  expect(result.map((job) => job.id)).toEqual(['2', '1', '3']);
});

// 2. Then make it pass
export function sortJobs(jobs: Job[]) {
  return [...jobs].sort(
    (a, b) => b.score - a.score || a.title.localeCompare(b.title)
  );
}
```

### Example 2: Multi-Step with Verification

**User Request:** "Add duplicate-job detection"

**❌ What Agents Do (Vague Plan)**

```text
I'll add duplicate detection by comparing job records and improving the pipeline.
```

**✅ What Should Happen (Verifiable Plan)**

```text
Plan:

1. Reproduce current duplicate behavior
   Verify:
   - same job from same company appears twice in fixture data

2. Add a small dedupe function using stable keys
   Verify:
   - duplicate fixture collapses to one result

3. Run existing scraper tests
   Verify:
   - no regression in non-duplicate cases

4. Check false positives
   Verify:
   - two different roles at same company still remain separate
```

---

## Anti-Patterns Summary

| Principle | Anti-Pattern | Fix |
|-----------|-------------|-----|
| Think Before Coding | Silently chooses one meaning of "smart filtering" | Present interpretations and ask which matters |
| Simplicity First | Builds reusable strategy objects for one grouping function | Use one reducer until real complexity appears |
| Surgical Changes | Fixes empty search by rewriting filtering, ranking, and typing | Only patch the failing condition |
| Goal-Driven | "I'll improve sorting" | Write failing test → fix → verify no regressions |

## Key Insight

Good JS/TS code is not code with the most abstractions. It is code that solves the current problem clearly, matches the existing style, and is easy to verify.
