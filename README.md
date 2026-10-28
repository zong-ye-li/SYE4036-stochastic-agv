# Belief-Based Velocity Control in a Stochastic AGV Warehouse

A simulation study of how velocity efficiency objectives and charging thresholds affect an automated guided vehicle under uncertain traffic conditions.  
Reproduction guidelines are provided.

**Author:** LI Zong-Ye · **Course:** SYE4036 System Modelling and Optimization, City University of Hong Kong

[Technical report and code](report.ipynb) · [Interactive simulation replay](replay.html)

## Versions

Oct 2026: The archieves presented in this repo were revised using GenAI, which removed several bugs and expression errors.  
Apr 2026: The original deliveries are archived under `prerevised_project_submissions/`.

## Research question

An AGV performs pickup, delivery and return subtasks in a grid warehouse. Traffic is hidden and changes over time. A three-state hidden Markov model estimates traffic from noisy velocity observations; the resulting belief informs the controlled velocity. A routing heuristic decides whether to continue a subtask or visit a charger.

The study compares two distinct efficiency objectives—expected statewise displacement-to-energy ratio and expected displacement divided by expected energy—against a linear velocity baseline. It then examines how charging thresholds change throughput and energy debt. Movement success depends only on the controlled velocity, through probability `v/8`; traffic affects observations and energy cost.

## Main results

The comparison contains 540 trials: 30 paired seeds for each combination of three policies, three threshold pairs and two energy models. Each trial has a 720-step budget.

Under objective A and the soft energy model, raising the low/medium thresholds from 10/30 to 30/50 reduces mean peak energy debt from 14.91 to 3.85 units, while mean completed subtasks fall from 60.87 to 58.07. At thresholds 20/40, objective A completes 59.70 subtasks on average, compared with 54.43 for the linear baseline, but consumes more energy. The report includes the paired comparison and the corresponding energy and debt results.

Both efficiency objectives attain a maximum on the admissible velocity interval; objective B has a closed-form maximizing velocity. These are velocity-efficiency results. The charging heuristic and full-task schedule are evaluated empirically.

The environment is synthetic and uncalibrated. The principal soft model permits negative energy balance so that threshold settings can be compared over a common horizon; a terminating hard model is included separately.

## Repository contents

| File | Purpose |
|---|---|
| `report.ipynb` | Technical report, complete simulation and analysis code, and saved figures and tables |
| `index.html` | Technical report in html format |
| `replay.html` | Self-contained 1,000-step replay; opens directly in a browser |
| `prerevised_project_submissions/codes.html` | Prerevised working notebook, submitted as the project delivery |
| `prerevised_project_submissions/presentation.pdf` | Presentation slides, submitted as the project delivery |
| `data/threshold_comparison.csv` | Per-trial results for all 540 comparisons |
| `data/condition_statistics.csv` | Group means and variances for the recorded comparisons |
| `data/diagnostics.json`, `data/diagnostic_trace.csv` | Summary and complete trace of the 10,000-step HMM diagnostic |
| `data/replay.json` | Replay configuration and the numerical records embedded in the HTML |
| `data/metadata.json` | Experimental configuration, package versions and recorded-data checksums |
| `requirements.txt` | Numerical, plotting and notebook dependencies |

Figures are embedded in the notebook. Reruns write their files into `outputs/`, which is excluded from Git.

## Reproduce the report

Use Python 3.12. Run these commands from the repository root:

```sh
python -m venv .venv
```

Activate the environment with `.venv\Scripts\Activate.ps1` in Windows PowerShell, or `source .venv/bin/activate` on macOS/Linux. Then run:

```sh
python -m pip install -r requirements.txt
jupyter lab
```

Open `report.ipynb` and run all cells in order. Each execution creates `outputs/<UTC timestamp>/` containing the figures, supplementary statistics, diagnostic trace, and regenerated replay with its JSON records. The supplied data and published replay remain unchanged.

By default, the notebook reads the recorded 540-trial comparison and reruns the 10,000-step diagnostic and the 1,000-step replay. The diagnostic uses seed 61000. The replay uses seed 50006, objective A, thresholds 20/40 and the soft model; it completes all 1,000 steps, with energy debt permitted. The replay traffic indicator shows simulator ground truth, while the controller uses its HMM belief.

To regenerate all 540 comparison trials, set `RERUN_EXPERIMENTS = True` in the final code cell. The new CSV and configuration are saved in the fresh output directory; the recorded inputs are preserved. Earlier analysis cells still use the recorded CSV. To analyse the new comparison, point `DATA` in the setup cell to that completed output directory, reset `RERUN_EXPERIMENTS = False`, and rerun the notebook.

Section 12 of the report describes the same workflow. Fixed seeds specify the stochastic inputs; floating-point results can vary across numerical-library versions or platforms.
