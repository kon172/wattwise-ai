# WattWise AI — Project Guide

**Purpose:** Give the CSC472 group a shared plan before writing code.  
**Status:** Draft plan; update this document when the team agrees on requirements.

## 1. Project definition

**Working title:** WattWise AI — Home Energy Advisor & Smart Schedule Optimizer  
**Users:** People living in houses, apartments, or condos who want to understand estimated energy use.  
**Main problem:** Energy costs are hard to explain at the appliance level, and users may not know which changes are practical.  
**Proposed solution:** A dashboard with calculations, explainable rules, and A* scheduling for flexible appliances.

## 2. Core user stories

| ID | As a user, I want to… | Acceptance criteria (planned) |
|---|---|---|
| US-01 | Enter the appliances I use | I can provide name, wattage, and usage hours; invalid inputs are rejected. |
| US-02 | Understand my estimated cost | The app shows per-appliance and total kWh, plus estimated cost in selected currency. |
| US-03 | Know which appliance uses the most energy | The dashboard ranks appliances by estimated energy use. |
| US-04 | Get understandable suggestions | Each recommendation includes an explanation of the triggering rule. |
| US-05 | Compare different usage patterns | The app shows before/after kWh and cost using the same assumptions. |
| US-06 | Schedule flexible appliances | The app returns a feasible lower-cost schedule, or explains if none exists. |

## 3. MVP requirements

### Functional requirements

- FR-01: Add, edit, and remove appliances.
- FR-02: Store wattage, daily duration, and optional flexible scheduling information.
- FR-03: Allow users to configure an electricity rate (flat or example time-of-use schedule).
- FR-04: Calculate consumption in kWh and estimated energy charges.
- FR-05: Evaluate a small, testable IF–THEN rule set.
- FR-06: Use A* to search valid schedules for a **small** number of flexible appliances.
- FR-07: Show an original-schedule vs. optimized-schedule cost comparison.

### Non-functional requirements

- NFR-01: Clear interface suitable for non-expert users.
- NFR-02: No negative wattages, durations, or prices accepted.
- NFR-03: Given the same input data, results are reproducible.
- NFR-04: Explanations must distinguish estimates from measured energy use.
- NFR-05: Code can be tested without an external AI API or internet connection.

## 4. Inputs and outputs

| Inputs | Example |
|---|---|
| Appliance | Washing machine |
| Rated power | 500 W |
| Duration | 1 hour |
| Flexible time window | 08:00–22:00 |
| Electricity prices | Simulated hourly rates |
| Schedule restrictions | At most one of selected appliances may run in a slot |

| Outputs | What the user sees |
|---|---|
| Daily/monthly energy | kWh by appliance and total |
| Cost estimate | Estimated price under user-provided tariff |
| Recommendations | Rules that triggered and suggested actions |
| Optimized schedule | Start time for each flexible appliance |
| Comparison | Before/after price and percentage difference |

## 5. Milestones and suggested tasks

### Phase 1 — Agree on requirements

- Decide which appliances are included in the demo.
- Agree on hourly time slots for the A* problem.
- Decide which electricity rates are simulated.
- Write sample inputs and expected outputs.

**Deliverable:** Approved feature list and example scenarios.

### Phase 2 — Build calculation and rule modules

- Implement `kWh = watts / 1000 × hours`.
- Calculate cost from a configurable rate.
- Implement 5–10 IF–THEN rules with explanations.
- Write tests for calculations and rule triggering.

**Deliverable:** Working Python functions and tests.

### Phase 3 — Build the A* optimizer

- Define state, possible actions, cost, and goal.
- Enforce time windows, durations, and selected capacity constraints.
- Implement a lower-bound heuristic for remaining work.
- Check small scenarios against exhaustive search to verify the optimal result.

**Deliverable:** Working scheduling algorithm, sample states/search path, and tests.

### Phase 4 — Build user interface

- Create Streamlit input forms.
- Display charts and recommendations.
- Show optimized schedule and before/after savings.
- Handle infeasible schedules clearly.

**Deliverable:** Interactive demonstration.

### Phase 5 — Evaluate and document

- Test flat-price vs. time-of-use cases.
- Check that optimized schedules obey restrictions.
- Record total cost, running time, and number of states explored.
- Prepare screen captures, demo script, and final report.

**Deliverable:** Results table, limitations, final presentation.

## 6. Definition of done

The MVP is complete when:

1. Sample appliance inputs produce correct estimated kWh and energy charges.
2. The rule engine fires the expected rules and explains the recommendation.
3. A* finds a valid lowest-cost schedule on tested small scenarios.
4. The UI displays estimates, recommendations, and a baseline comparison.
5. Tests pass, and the final README contains verified local setup instructions.

## 7. Team collaboration guidelines

- Assign one GitHub Issue per feature or bug.
- Use short branches such as `feature/energy-calculator` and `feature/astar`.
- Keep pull requests small and include a short summary and test evidence.
- Do not commit API keys or sensitive user data.
- When decisions change, update these documents in the same pull request.
- Add member names and responsibilities once agreed; avoid assuming team roles.

## 8. Suggested initial GitHub Issues

1. **Define appliance data model and validation rules**
2. **Implement energy and cost calculator with tests**
3. **Document and implement rule-based recommendations**
4. **Design A* search state, actions, and heuristic**
5. **Implement A* optimizer with a small verified test case**
6. **Create Streamlit dashboard wireframe**
7. **Prepare demo dataset and evaluation scenarios**

## 9. Main risks and limits

- **Electricity rate assumption:** Savings from rescheduling depend on the tariff used. Under flat rates, timing alone does not change energy charges.
- **Estimated energy:** Rated power and usage hours are estimates; real appliance energy use can vary.
- **A* complexity:** Large schedules can require substantial memory/time. Start with 2–4 flexible appliances and hourly slots.
- **Advice quality:** Rule-based advice needs review and clear assumptions; do not claim professional certification.
- **Gemini API:** Optional future enhancement only. The initial system does not depend on it.

## 10. What to do first

For the first group meeting, decide:

- The 3–5 appliances included in the demonstration.
- Whether to model both flat and time-of-use tariffs.
- Which appliance restrictions count as valid.
- Who takes ownership of calculation, AI algorithm, UI, and testing/documentation tasks.

**Suggested first coding task:** Implement the energy calculation function and write three simple tests before building the UI.
