# Century Building Solar Rooftop Optimization  
*A Pyomo-based notebook for sizing a photovoltaic (PV) system on a downtown Chicago high-rise*

---

## 1 Overview  
This repository contains a single Jupyter notebook, **`Solar_optimization_solver-2.ipynb`**, that formulates and solves a mixed‑integer linear program (MILP) to determine the optimal allocation of solar panels across eight rooftop segments of the “Century Building.”  
The model maximises **annual energy production (kWh)** while respecting site‑specific engineering and financial limits:

| Category | Key limits modelled |
|----------|--------------------|
| **Physical** | ✅ Usable roof area per segment (ft²) <br> ✅ Structural panel cap per segment |
| **Performance** | ✅ Shading factor (0‑1) by segment <br> ✅ Performance ratio (system losses) |
| **Business** | ✅ Capital budget (USD) <br> ✅ Overall nameplate cap (kW) |

The notebook solves the problem with the open‑source **CBC** solver (GLPK, Gurobi, or NEOS back‑ends can be substituted) and reports:

* Panel count per segment  
* Installed capacity (kW DC)  
* First‑year energy yield (kWh)  
* High‑level project economics (cost after federal ITC, $/kWh offset, REC value)

---

## 2 Project Structure  

```
.
└── Solar_optimization_solver-2.ipynb   # Main notebook (Pyomo model + demo run)
```

---

## 3 Quick‑start

> **Prerequisites**  
> • Python 3.9+ • Jupyter Lab/Notebook • Pyomo • A MILP solver (CBC or GLPK shown below)

```bash
# 1. Create & activate a clean environment (recommended)
python -m venv venv
source venv/bin/activate          # Windows: venv\Scriptsctivate

# 2. Install Python packages
pip install pyomo jupyter

# 3. Install a solver (Ubuntu/Debian example)
sudo apt-get update && sudo apt-get install -y coinor-cbc

# 4. Launch and explore
jupyter notebook Solar_optimization_solver-2.ipynb
```

Inside the notebook, simply run **all cells** (`Kernel → Restart & Run All`). The solve takes milliseconds on a laptop‑class CPU.

---

## 4 Model Anatomy  

| Element | Symbol | Description |
|---------|--------|-------------|
| **Decision vars** | \(x_i\) | integer # of panels on roof segment *i* |
| **Objective** | maximize \( \sum_i x_i \; P_{\text{panel}} \; \eta \; \text{sunh}_{\text{annual}} \; \text{shade}_i \) | annual kWh |
| **Constraints** | • Area: \(x_i \cdot A_{\text{panel}} \le A_i\) <br> • Budget: \(\sum_i x_i C_{\text{panel}} \le \$350{,}000\) <br> • Structural caps \(x_i \le \text{struct}_{i}\) <br> • Site capacity: \(\sum_i x_i P_{\text{panel}} \le 500{,}000\text{ W}\) |

Default engineering constants are set in the notebook header (17.5 ft² / 320 W per panel, 4.5 sun‑hours/day, etc.) and can be edited inline for *what‑if* studies.

---

## 5 Sensitivity & Scenario Analysis  

Two simple approaches:

1. **Inline sliders** – add *ipywidgets* sliders for **budget**, **capacity cap**, or **REC rate** and re‑solve on the fly.  
2. **Batch sweep** – loop over a Python `for` list of parameter values, store results in a Pandas DataFrame, and plot breakeven curves.

A stub function `solve_model(budget, capacity_limit, shading_factor=...)` is already structured in the notebook to make parameterisation straightforward.

---

## 6 Extending the Notebook  

| Task | Where to plug in |
|------|------------------|
| **Custom irradiance data** | Replace `peak_sun_hours` with PVGIS or NREL TMY‑3 data per segment |
| **Module/tilt selection** | Treat tilt angle as a discrete decision var and link to yield tables |
| **Financial pro‑forma** | Expand Section 6 to cash‑flow NPV with discounting, degradation, O&M |
| **Visualization** | Add Matplotlib heat‑maps of panel allocation or Pareto frontiers |

---

## 7 Results Snapshot (default inputs)

```
Solver Status   : ok
Termination Cond: optimal

Maximized Annual Energy (kWh): 726,000

Segment allocation
  1 → 20  panels   (100 % of usable area)
  2 → 100 panels   …
  ...
Installed capacity : 498.6 kW
Total cost         : $349,600
Net cost after ITC : $258,704
Year‑1 cashflow    : $101,600  (energy + SRECs)
Simple payback     : 2.6 years
```

*(Values vary if you change any of the defaults.)*

---

## 8 Troubleshooting  

| Symptom | Fix |
|---------|-----|
| `Executable cbc not found` | Install solver (`apt-get install coinor-cbc` or `brew install cbc`) |
| MemoryError on Colab | Switch to `neos-<solver>` to offload computing to NEOS server |
| Model infeasible | Check that budget and capacity limits are not mutually exclusive |

---

## 9 License & Attribution  

This notebook is released under the **MIT License**. Solar constants and incentive figures are illustrative; validate with current Illinois SREC and utility tariff schedules before making investment decisions.

---

## 10 Authors  

- **Aryaj Pandey** – Model design, coding, and documentation  
- Course: *Engineering Systems Analysis*, Illinois Tech (Spring 2025)

Contributions, improvements, and bug reports are welcome—please open an issue or pull request.

---

