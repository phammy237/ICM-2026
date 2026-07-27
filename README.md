# ICM 2026 — Problem D (WNBA Franchise Model)

Modeling submission for MCM/ICM 2026 Problem D, using the Indiana Fever as the
target franchise. The model simulates a WNBA team's on-court and financial
performance week over week — roster construction, ticket pricing/attendance,
brand and quality dynamics, cash/debt management, and expansion effects — and
runs Monte Carlo trials to compare outcomes across scenarios.

## Repository layout

```
ICM-2026/
├── ICM_ProbD.pdf              # Problem statement
├── data/
│   └── icm_2026_wnba_data.xlsx  # Source workbook (players, stats, league, teams, expansion)
└── code/
    ├── wnba_data_setup.ipynb  # Data loader & prep
    └── modeling.ipynb         # Simulation, optimization, and Monte Carlo analysis
```

## Notebooks

### `code/wnba_data_setup.ipynb`
Loads the Excel workbook and builds the modeling table:
- Reads sheets: `players`, `player raw stats`, `league`, `teams`, `expansion`
- Cleans numeric columns (salary commas, blanks)
- Computes `perf_score` (performance index) and `injury_risk` when missing
- Outputs `players_model`, ready for optimization/simulation

### `code/modeling.ipynb`
Runs the core model, built around a `Params` dataclass of scenario-tuned
constants:
- **Roster selection** — `solve_roster_milp` picks a roster under the salary
  cap and roster-size bounds, respecting draft picks and trade budget
  (`load_candidates_and_assets`, `ensure_mechanism_sheets`)
- **Attendance & pricing** — `attendance` models demand as a function of
  brand, quality, and price; `choose_price` sweeps price to maximize revenue
- **Team dynamics** — brand (`B`) and quality (`Q`) evolution, cash/debt via
  `leverage_policy`, and expansion effects via `apply_expansion`
  (haversine distance to the expansion market)
- **Scenario simulation** — full season runs (`run_season_simulation`) across
  `baseline`, `macro_stress`, `injury`, and `expansion` scenarios, summarized
  and plotted (cash, debt, profit over time)
- **Monte Carlo** — `run_monte_carlo` repeats each scenario (default N=200)
  to estimate profit distributions and bankruptcy risk

## Requirements

Python 3 with `numpy`, `pandas`, `matplotlib`, and `openpyxl` (for reading
`.xlsx`).

## Usage

Run `code/wnba_data_setup.ipynb` first to produce `players_model` from the
Excel workbook, then run `code/modeling.ipynb` to build rosters, simulate
scenarios, and generate the Monte Carlo summaries.
