# WattWise AI — Home Energy Advisor & Smart Schedule Optimizer

> **Project status: Planning / proposal stage.** This repository currently contains the project documentation. The application and AI algorithms are **not implemented yet**.

**Course:** CSC472 — Artificial Intelligence  
**Project type:** University group project  
**Team:** Add group member names here

## 1. Project overview

WattWise AI is a proposed home energy management application that helps people understand their electricity use, estimate costs, and plan energy-saving actions.

Users will enter information about household appliances, such as power ratings and daily usage hours. WattWise will calculate estimated energy consumption, apply a **Rule-Based Expert System** to explain potential energy-saving opportunities, and use **A* Search** to suggest lower-cost schedules for appliances that can run at different times.

**Main goal:** Help households make better energy decisions with understandable AI recommendations and transparent calculations.

## 2. Problem statement

Many people know how much they pay for electricity, but may not know:

- Which appliances contribute most to their estimated usage.
- How changes in usage hours affect their estimated bill.
- Whether moving flexible appliance use to different hours could reduce costs when electricity rates vary by time.
- Why an energy-saving recommendation was made.

WattWise aims to make these decisions easier to understand.

## 3. Main objectives

1. Estimate daily and monthly electricity use in **kilowatt-hours (kWh)**.
2. Estimate electricity cost using a user-provided tariff.
3. Identify high-usage appliances with an explainable rule-based system.
4. Find a valid, lower-cost schedule for flexible appliances using A* Search.
5. Display recommendations and before/after comparisons in a simple dashboard.

## 4. Planned features (MVP)

| Feature | What it does | Status |
|---|---|---|
| Appliance input | Add name, power (W), and hours used | Planned |
| Energy calculator | Estimate kWh and monthly cost | Planned |
| Energy dashboard | Display appliance totals and charts | Planned |
| Rule-based advisor | Provide IF–THEN recommendations and reasons | Planned |
| A* schedule optimizer | Plan flexible appliance times within constraints | Planned |
| What-if comparison | Compare original vs. suggested usage | Planned |

**MVP = Minimum Viable Product:** the smallest working version that demonstrates the core ideas.

### Outside the first version

- Direct control of real appliances or smart plugs.
- Real-time electricity meter integration.
- Guaranteed electricity-bill savings.
- Automatic learning from real household data.
- A chatbot or external large language model (for example, Gemini). This may be considered later, but is **not required** for the core AI algorithms.

## 5. AI techniques

### Rule-Based Expert System

Stores simple rules that produce understandable recommendations from appliance facts.

Example (illustrative rule, **not** a universal energy standard):

```text
IF appliance_type = "air_conditioner"
AND usage_hours_per_day > 6
THEN suggest reviewing runtime and temperature settings.
```

The system should show which rule triggered and why. Rules will be tested and reviewed before use.

### A* Search Algorithm

A* explores possible schedules and prioritizes states with the lowest estimated total cost:

```text
f(n) = g(n) + h(n)
```

- `g(n)`: cost of appliances already scheduled.
- `h(n)`: lower-bound estimate of the cost to schedule remaining appliances.
- `f(n)`: estimated total cost through state `n`.

The algorithm must respect appliance constraints, such as available time windows, duration, and limits on simultaneous usage. For a minimum-cost guarantee, the heuristic must not overestimate the remaining cost.

**Important:** Moving appliances to another time saves money **only when the relevant electricity price differs by time** (or another modeled pricing constraint makes it cheaper). Under a flat per-kWh rate, changing the time alone does not lower the energy charge.

For a deeper explanation, read [AI Design](docs/AI_DESIGN.md).

## 6. Proposed technology stack

| Technology | Planned role |
|---|---|
| Python | Calculations and AI algorithms |
| Streamlit | Web interface and dashboard |
| Pandas | Organizing and analyzing appliance data (optional) |
| Plotly | Interactive charts (optional) |
| Pytest | Automated tests |
| Git + GitHub | Collaboration and version control |

No external AI API is needed for the MVP. If a conversational assistant is added in the future, its explanations must use validated results from the calculation/optimization modules.

## 7. How WattWise is planned to work

```text
User enters appliances, usage hours, and electricity rate
                       |
                       v
               Input validation
                       |
                       v
            Energy cost calculation
                       |
             +---------+----------+
             |                    |
             v                    v
       Rule-based AI         A* scheduling AI
      (recommendations)     (flexible appliances)
             |                    |
             +---------+----------+
                       |
                       v
        Dashboard + savings comparison
```

## 8. Electricity calculation example

```text
Energy (kWh) = Power (W) / 1000 × Usage hours
Cost = Energy (kWh) × Electricity price (per kWh)
```

For example, an appliance rated at **1,200 W**, used for **8 hours/day** for **30 days**:

- Estimated energy: `1.2 × 8 × 30 = 288 kWh`
- At a *hypothetical flat rate* of **฿4/kWh**: `288 × 4 = ฿1,152`

This is a simplified estimate, not a prediction of a real bill. Actual power consumption, tiered rates, taxes, fixed charges, and other fees may differ.

## 9. Proposed repository structure

```text
wattwise-ai/
├── README.md
├── .gitignore
├── docs/
│   ├── PROJECT_GUIDE.md
│   └── AI_DESIGN.md
├── src/                  # To be added during implementation
│   ├── calculator.py
│   ├── rule_engine.py
│   └── astar_optimizer.py
├── tests/                # To be added during implementation
└── app.py                # To be added during implementation
```

**Note:** `src/`, `tests/`, and `app.py` are *planned*, not present in the documentation starter pack.

## 10. Development roadmap

- [x] Select project idea and define overall objectives.
- [x] Prepare the initial proposal and project documentation.
- [ ] Agree on the MVP requirements and team responsibilities.
- [ ] Implement and test the energy calculator.
- [ ] Implement and test the rule-based recommendation engine.
- [ ] Implement A* scheduling with a small sample dataset.
- [ ] Build the Streamlit dashboard.
- [ ] Compare results against a simple baseline schedule.
- [ ] Prepare a final demo, evaluation, and report.

See [Project Guide](docs/PROJECT_GUIDE.md) for acceptance criteria and a suggested work plan.

## 11. Academic references

Initial conceptual sources from CSC472 course materials:

- *Lec02 — Knowledge Representation* (course-provided lecture file).
- *Rule-Based Expert Systems* (course-provided lecture file).
- *Lec04 — Heuristic Search* (course-provided lecture file).
- *D — Heuristic Search* (course-provided lecture file).

These are background references for the planned AI methods. The WattWise application and results still need to be developed and tested.

## 12. Team and contributions

Team member names and roles will be added after the group confirms them. Please use GitHub Issues to track tasks and pull requests to review code changes.

---

**Project note:** WattWise is a student prototype and educational planning tool, not a certified energy audit or a guarantee of savings.
