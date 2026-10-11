# Northwind Support Desk

A support ticket management system: raise tickets, search and filter them, assign them to agents,
and move them through their lifecycle.

## Task 1 - Defect fix and server side filtering

### 1.1 - DEFECT-117: filters sometimes do not take effect

**Root Cause**

The useTicketList hook in  **apps/web/src/features/tickets/hooks/useTicketList.ts** used a React useEffect whose dependency array contained only filters.search, filters.page and filters.pageSize. Therefore hanges to other filters such as Status and Priority did not trigger the effect or initiate a new API request. This could also cause stale ticket results to remain visible until another watched value changed.

**Fix Implemeted**

The effect was updated to monitor the relevant filter values. A useRef is used to compare the previous filter values with the current values and distinguish Search only changes from other filter changes.

- Search only changes are debounced using the existing DEBOUNCE_MS value.
- Changes to other filters trigger an API request immediately
- When Search changes together with another filter, the non-search change triggers an immediate request using the current filter values.

**Verification**
A regression test was added to **apps/web/src/features/tickets/hooks/useTicketList.test.ts** to verify that changing Status triggers another API request and includes the selected status in the request parameters. The test initially failed against the original implementation and passed after the fix.

The existing Search test was also retained to verify that the search request is sent after the debounce period.

Run the frontend tests from the repository root:
`npm run test --workspace apps/web`

At the time of writing this, both frontend test files pass, with three tests passing in total.

**Design Decisions**

- The frontend fix preserves the existing ticket-list hook and API interface rather than introducing a separate fetching mechanism. This keeps the change focused on the cause of the defect.
- The existing Search debounce is retained to avoid making an API request for every keystroke. Other filter changes trigger immediate requests to match the assignment requirements.
- The fix was verified with an automated regression test, rather than relying only on manual browser testing. 

Avg Time: 3h as I was completely new to .net and things. so took time to go through the repo and observe and learn.

Task 1.2 server update, 2, 3 and 4 were couldn't complete and still trying them out.
