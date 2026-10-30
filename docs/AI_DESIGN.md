# WattWise — AI Design Notes

**Status:** Proposed algorithm design, **not implemented**.  
**Scope:** Explain how the CSC472 methods can be applied and tested.

## 1. What counts as AI in WattWise?

WattWise has two classical-AI components:

- A **Rule-Based Expert System** that matches appliance facts to human-written rules and gives an explanation.
- An **A* Search optimizer** that explores valid appliance schedules to minimize modeled energy cost.

The cost calculator itself is ordinary arithmetic, not AI. A Gemini or ChatGPT integration is not required to make the classical-AI components work.

## 2. Rule-Based Expert System

### Knowledge representation

Store facts about each appliance, for example:

```text
appliance_type = air_conditioner
rated_power_w = 1200
usage_hours_per_day = 8
```

Define simple IF–THEN rules, for example:

```text
RULE R1:
IF appliance_type == "air_conditioner"
AND usage_hours_per_day > 6
THEN message = "Review AC runtime and temperature settings."
```

> The `6 hours` condition is an **illustrative project threshold**, not a scientifically verified universal limit. Choose and document rules carefully.

### Inference process

1. Receive validated appliance facts.
2. Check rule conditions.
3. Fire matching rules (forward chaining).
4. Return the recommendation and explanation, including the matched rule ID.

The system should avoid claiming it has measured actual electricity usage.

## 3. A* Scheduling Optimizer

### The scheduling problem

Some appliances, such as a washing machine, may be moved to different time slots. Given flexible appliance jobs, time windows, and a known price for each hour, find a **valid schedule with minimum modeled energy charge**.

To make the search meaningful, start with:

- A day divided into discrete hourly slots.
- 2–4 flexible appliances, each with a specified duration and power.
- A start/end time window for each appliance.
- A limit on simultaneous usage (for example, one flexible appliance per slot).
- Simulated hourly electricity prices supplied as input.

### Search-state definition

One possible state is:

```text
state = (
  index_of_next_appliance,
  occupied_hourly_slots,
  assignments_so_far
)
```

- **Initial state:** No flexible appliances scheduled.
- **Action:** Assign the next appliance to a permitted start time without violating any constraints.
- **Transition:** Reserve its occupied time slots.
- **Goal:** Every flexible appliance has a valid assigned start time.
- **Path cost:** Total electricity charge for all assigned appliance runs.

For appliance `i` (constant power assumption):

```text
run_cost(i, start) = sum over active hours [power_kw(i) × tariff(hour)]
```

### A* evaluation

```text
f(n) = g(n) + h(n)
```

- `g(n)` = exact electricity charge of assigned appliance runs.
- `h(n)` = optimistic lower bound for unassigned appliance runs.

**One admissible heuristic for nonnegative prices:** For each unassigned appliance, find its cheapest possible run within its own time window while **ignoring competition for occupied slots**, then sum these lower costs. Since ignoring capacity conflicts can only make the remaining schedule look cheaper (or equal), `h(n)` never exceeds the true remaining cost when a valid schedule exists.

If an appliance has no allowed start time even when conflicts are ignored, the problem is infeasible. If time windows and scheduling constraints are absent, the optimization may be trivial.

### Simple pseudocode

```text
OPEN <- priority queue containing the initial state
best_cost[initial] <- 0

WHILE OPEN not empty:
    node <- pop the node with smallest g(node) + h(node)
    IF node schedules all appliances:
        RETURN its complete schedule and total cost

    FOR each valid next assignment:
        next <- result of assigning that appliance
        new_g <- g(node) + assignment_cost
        IF new_g is better than the best known cost for next:
            save cost and parent
            push next into OPEN with priority new_g + h(next)

RETURN "No valid schedule found"
```

Implementation must handle repeated equivalent states and ties consistently. Test against a brute-force enumeration on small cases.

## 4. Small example for a demonstration

Assume two flexible 1-hour jobs, with no simultaneous running:

| Job | Power | Available hours |
|---|---:|---|
| Washer | 1 kW | 18:00, 20:00, 22:00 |
| Dishwasher | 1 kW | 18:00, 20:00, 22:00 |

**Hypothetical** hourly tariff:

| Time | Price |
|---|---:|
| 18:00 | ฿6/kWh |
| 20:00 | ฿3/kWh |
| 22:00 | ฿2/kWh |

One minimum-cost schedule is:

- Washer: 20:00 → ฿3
- Dishwasher: 22:00 → ฿2
- **Total modeled cost: ฿5**

Reversing the appliance assignments gives the same minimum cost. This is a teaching example, not an actual utility tariff.

### Baseline comparison

If the original schedule is washer at 18:00 and dishwasher at 20:00, its cost is **฿9**. Under these *hypothetical* prices, the modeled difference is **฿4**, about **44.4% lower** energy charge for these two runs. This does not imply a 44.4% reduction in an entire household bill.

## 5. Validation plan

| Test | Expected behavior |
|---|---|
| Correct kWh input | Calculations match hand-worked examples |
| Negative wattage | Reject input |
| Simple triggering rule | Show matched rule and explanation |
| No valid start times | Report that no feasible schedule exists |
| Flat tariff, identical total energy | No time-shifting energy-charge savings |
| Small time-of-use scenario | A* cost equals exhaustive-search minimum |
| Overlapping forbidden slots | No conflicting assignment in output |

Additional evaluation metrics: time to solve, nodes expanded, and estimated cost change versus a baseline.

## 6. Potential future extensions (not MVP)

- Natural-language explanations with an LLM such as Gemini, using verified structured outputs.
- More detailed appliance consumption profiles and smart meter data.
- Forecasting, if appropriate real datasets become available.
- More complex multi-day scheduling and load limits.

Do not describe these features as implemented until they are built and tested.
