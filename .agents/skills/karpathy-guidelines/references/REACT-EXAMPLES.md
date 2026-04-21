# React Examples

Real-world React examples demonstrating the four principles. These focus on components, hooks, UI state, and bug fixes in existing codebases. Use `EXAMPLES.md` for the more general cross-language patterns around ambiguity, small diffs, and verification.

---

## 1. Think Before Coding

### Example 1: Ambiguous Search Improvement

**User Request:** "Improve the search on the jobs page"

**❌ What Agents Do (Wrong Assumptions)**

```tsx
export function JobsSearch() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);

  useEffect(() => {
    const id = setTimeout(async () => {
      const res = await fetch(`/api/jobs/search?q=${query}`);
      setResults(await res.json());
    }, 300);

    return () => clearTimeout(id);
  }, [query]);

  return <SearchInput value={query} onChange={setQuery} />;
}
```

**Problems:**
- Assumed "improve" means live API search
- Assumed backend endpoint exists
- Assumed debouncing is the missing feature
- Ignored whether the real issue is relevance, speed, or UX

**✅ What Should Happen (Surface Interpretations)**

```text
"Improve the search" could mean:

1. Better UX
   - debounce typing, show loading, preserve input

2. Better relevance
   - search title/skills first, description second

3. Better performance
   - avoid unnecessary renders or requests

4. Better discoverability
   - add filters and highlighted matches

Which problem are users seeing today?
```

### Example 2: Hidden Assumptions in a Component Change

**User Request:** "Add company logos to job cards"

**❌ What Agents Do (Pick Silently)**

```tsx
<img src={`https://logo.clearbit.com/${job.company}.com`} alt={job.company} />
```

**Problems:**
- Assumed a third-party logo source is acceptable
- Assumed company name maps cleanly to domain
- Assumed hotlinking external images is okay
- Ignored broken-image behavior

**✅ What Should Happen**

```text
Before adding logos, I need to clarify:

1. Do we already have logo URLs in the data?
2. Are external logo services allowed?
3. Should missing logos fall back to initials?
4. Do we want this only on desktop or all screen sizes?

Simplest safe version: render `job.logoUrl` if present, otherwise show initials fallback.
```

---

## 2. Simplicity First

### Example 1: Overabstracted Hook

**User Request:** "Show a loading spinner while jobs are being fetched"

**❌ What Agents Do (Overengineered)**

```tsx
const JobsContext = createContext(null);

function JobsProvider({ children }) {
  const [state, dispatch] = useReducer(reducer, initialState);
  const services = useMemo(() => createServices(dispatch), [dispatch]);

  return (
    <JobsContext.Provider value={{ state, services }}>
      {children}
    </JobsContext.Provider>
  );
}

function useJobsController() {
  const context = useContext(JobsContext);
  if (!context) throw new Error('Missing JobsProvider');
  return context;
}
```

**✅ What Should Happen (Simple)**

```tsx
function JobsPage() {
  const [jobs, setJobs] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    let active = true;

    fetch('/api/jobs')
      .then((res) => res.json())
      .then((data) => {
        if (active) setJobs(data.jobs);
      })
      .finally(() => {
        if (active) setLoading(false);
      });

    return () => {
      active = false;
    };
  }, []);

  if (loading) return <Spinner />;
  return <JobsList jobs={jobs} />;
}
```

### Example 2: Speculative Component Flexibility

**User Request:** "Add a button to save a job"

**❌ What Agents Do (Too Many Features)**

```tsx
export function SaveButton({
  job,
  variant = 'primary',
  onBeforeSave,
  onAfterSave,
  analyticsLabel,
  optimistic = true,
  iconPosition = 'left',
  size = 'md',
}) {
  // 80+ lines of configurable button behavior
}
```

**✅ What Should Happen (Just What Was Asked)**

```tsx
export function SaveJobButton({ jobId, onSave }) {
  return <button onClick={() => onSave(jobId)}>Save job</button>;
}
```

---

## 3. Surgical Changes

### Example 1: Bug Fix Without Refactoring the Component Tree

**User Request:** "Fix the bug where clearing filters does not reset the input"

**❌ What Agents Do (Too Much)**

```diff
-export function JobsFilters({ filters, setFilters }) {
-  const [query, setQuery] = useState(filters.query || '');
+export function JobsFilters({ filters, setFilters, onAnalyticsEvent }) {
+  const [state, dispatch] = useReducer(filtersReducer, {
+    query: filters.query || '',
+    location: filters.location || '',
+    remoteOnly: Boolean(filters.remoteOnly),
+  });
+
+  useEffect(() => {
+    onAnalyticsEvent?.('filters_loaded');
+  }, [onAnalyticsEvent]);
```

**Problems:**
- Replaced local state with reducer
- Added analytics prop
- Expanded filter model unrelated to the bug

**✅ What Should Happen (Surgical)**

```diff
 export function JobsFilters({ filters, setFilters }) {
   const [query, setQuery] = useState(filters.query || '');
+
+  useEffect(() => {
+    setQuery(filters.query || '');
+  }, [filters.query]);
```

### Example 2: Style Drift While Adding UI State

**User Request:** "Disable the Apply button while the request is in progress"

**❌ What Agents Do (Reformat + Rewrite)**

```diff
-export function ApplyButton({ onApply }) {
-  return <button onClick={onApply}>Apply</button>;
-}
+export function ApplyButton({ onApply }: { onApply: () => Promise<void> }) {
+  const [loading, setLoading] = React.useState(false);
+
+  const handleClick = React.useCallback(async () => {
+    try {
+      setLoading(true);
+      await onApply();
+    } finally {
+      setLoading(false);
+    }
+  }, [onApply]);
+
+  return (
+    <button className="btn btn-primary" disabled={loading} onClick={handleClick}>
+      {loading ? 'Applying...' : 'Apply'}
+    </button>
+  );
+}
```

**Problems:**
- Added types, callback memoization, and new classes
- Changed label copy and styling
- More change than necessary

**✅ What Should Happen (Minimal Change)**

```diff
 export function ApplyButton({ onApply }) {
+  const [loading, setLoading] = useState(false)
+
+  const handleClick = async () => {
+    setLoading(true)
+    try {
+      await onApply()
+    } finally {
+      setLoading(false)
+    }
+  }
+
-  return <button onClick={onApply}>Apply</button>;
+  return <button onClick={handleClick} disabled={loading}>Apply</button>;
 }
```

---

## 4. Goal-Driven Execution

### Example 1: Reproduce a UI Bug First

**User Request:** "The saved jobs count doesn't update until refresh"

**❌ What Agents Do (Immediate Guess Fix)**

```tsx
useEffect(() => {
  refetchSavedJobs();
}, [savedJobs]);
```

**✅ What Should Happen (Test First)**

```tsx
it('updates the saved jobs count after saving a job', async () => {
  render(<JobsPage />);

  await user.click(screen.getByRole('button', { name: /save job/i }));

  expect(await screen.findByText('Saved jobs (1)')).toBeInTheDocument();
});
```

Then fix the actual missing state update or invalidation path.

### Example 2: Incremental UI Work

**User Request:** "Add optimistic UI for saving jobs"

**❌ What Agents Do (Large Unverifiable Rewrite)**

```text
I'll refactor saved jobs into a global store with optimistic updates and rollback support.
```

**✅ What Should Happen (Verifiable Plan)**

```text
Plan:

1. Add a test for current save flow
   Verify:
   - click save -> saved state appears after server response

2. Apply a local optimistic update to the clicked card
   Verify:
   - button/state changes immediately before response finishes

3. Handle rollback on failure
   Verify:
   - failed request restores previous saved state

4. Run existing component tests
   Verify:
   - no regression in non-save flows
```

---

## Anti-Patterns Summary

| Principle | Anti-Pattern | Fix |
|-----------|-------------|-----|
| Think Before Coding | Assumes "improve search" means live debounced API search | Clarify whether the issue is UX, relevance, or performance |
| Simplicity First | Introduces providers and reducers just to show loading | Add the smallest local loading state |
| Surgical Changes | Fixes one filter-reset bug by redesigning filter state | Sync only the field that is broken |
| Goal-Driven | Guesses at UI state bugs without a reproduction | Add a failing UI test first |

## Key Insight

Good React code is not the most abstract component architecture. It is the smallest understandable change that fixes the user-visible problem, preserves existing patterns, and can be verified through behavior.
