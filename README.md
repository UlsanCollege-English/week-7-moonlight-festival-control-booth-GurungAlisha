# Week 7 Homework: Moonlight Festival Control Booth

## Summary

This homework implements a priority queue system for a festival control booth using Python's `heapq` module. Each alert has a numeric priority and a title; lower numbers mean higher urgency. The four functions cover ordered processing, stable tie-breaking, selecting the top-k most urgent alerts, and non-destructive peeking at the next alert. All solutions use `heapq` operations to stay efficient rather than sorting naively.

---

## Approach

### `order_festival_alerts`

* Copied the input list and called `heapq.heapify()` on it to rearrange it into a min-heap in O(n) time.
* Each heap item was already a `(priority, title)` tuple, so Python's tuple comparison naturally ordered by priority first.
* Built the result by calling `heapq.heappop()` n times, each time extracting the minimum-priority alert and appending its title to the output list.

### `order_festival_alerts_stable`

* When two alerts share the same priority, comparing `(priority, title)` tuples would fall back to comparing strings alphabetically — which is not the same as input order.
* Fixed this by inserting the original index `i` as a middle tiebreaker: each heap item became `(priority, i, title)`. Since `i` values are always unique, Python never needs to compare titles at all.
* This guarantees that equal-priority alerts come out in exactly the order they appeared in the input.

### `top_k_festival_alerts`

* Used `heapq.nsmallest(k, alerts)` which returns the k tuples with the smallest priority values directly, without fully sorting the list.
* Handled `k <= 0` with an early return of `[]` before touching the heap.
* If `k` exceeds the length of `alerts`, `nsmallest` simply returns all available alerts — no special case needed.

### `peek_next_festival_alert`

* Created a shallow copy of the input with `list(alerts)` so the original is never modified.
* Called `heapq.heapify()` on the copy, then read `heap[0]` — the root of a min-heap is always the minimum element, so no pop is needed.
* Returned `None` for an empty input before attempting any heap operations.

---

## Complexity

### `order_festival_alerts`

* **Time:** O(n log n)
* **Space:** O(n)
* **Why:** `heapify` is O(n), but each of the n `heappop` calls costs O(log n), giving O(n log n) overall. The output list holds all n titles, so space is O(n).

### `order_festival_alerts_stable`

* **Time:** O(n log n)
* **Space:** O(n)
* **Why:** Same as above. Building the extended `(priority, i, title)` list is O(n), and the heap operations dominate at O(n log n). The extended list uses O(n) extra space.

### `top_k_festival_alerts`

* **Time:** O(n log k)
* **Space:** O(k)
* **Why:** `heapq.nsmallest(k, n_items)` maintains a max-heap of size k while scanning all n items, costing O(n log k). The output holds at most k titles, so space is O(k).

### `peek_next_festival_alert`

* **Time:** O(n)
* **Space:** O(n)
* **Why:** `list(alerts)` copies n elements in O(n), and `heapify` runs in O(n). Reading `heap[0]` is O(1). The copy requires O(n) extra space.

---

## Edge-case checklist

### `order_festival_alerts`

* [x] empty input — `heapify([])` is fine, the list comprehension produces `[]`
* [x] one alert — heapify and one pop works correctly
* [x] multiple different priorities — natural tuple ordering handles this

### `order_festival_alerts_stable`

* [x] same-priority tie — index `i` breaks the tie in insertion order
* [x] all same priority — all ties resolved by index, full input order preserved
* [x] empty input — enumerate produces nothing, heap is empty, result is `[]`

### `top_k_festival_alerts`

* [x] `k = 0` — early return `[]` before any heap work
* [x] `k > len(alerts)` — `nsmallest` returns all available items without error
* [x] duplicate priorities — `nsmallest` handles ties stably internally
* [x] empty input — `nsmallest` on an empty list returns `[]`

### `peek_next_festival_alert`

* [x] empty input — explicit `if not alerts: return None` guard
* [x] normal case — copy, heapify, return `heap[0][1]`

---

## Test notes

* Tested stable ordering with two alerts both at priority 2; confirmed the one listed first in input came out first.
* Tested `top_k_festival_alerts` with `k=0`, `k=1`, `k=100` on a 3-item list; all returned the expected results.
* Confirmed `peek_next_festival_alert` does not mutate the original list by checking it before and after the call.

---

## Assistance & Sources

### AI used?

* [ ] No
* [x] Yes

If yes, what did it help with?

Used AI to help structure the stable sort tiebreaker approach and verify complexity analysis.

### Other sources

* Python docs: `heapq` module — https://docs.python.org/3/library/heapq.html

---

## Reflection

**What was hardest?**

* The stable ordering function — it wasn't obvious at first why `(priority, title)` tuple comparison would break stability, since Python would silently fall back to alphabetical string comparison for ties instead of raising an error.

**What do you understand better now?**

* How Python compares tuples element-by-element, and why inserting a unique tiebreaker index is the standard pattern for stable heap operations.