# Freight Transportation Cost Optimization

End-to-end logistics routing and cost minimization using Python, Excel Solver, Vogel’s Approximation Method (VAM), and the North-West Corner Method (NWCM) applied to inter-district road freight distribution across Haryana, India.

---

## Problem

Determine the most cost-efficient freight distribution schedule from multiple regional supply hubs to destination markets under strict supply and demand constraints, quantifying the cost penalty of traditional heuristics against exact mathematical optimization.

---

## Dataset

* **Source:** Synthesized inter-district road logistics network based on official National Highway (NH) routes in Haryana, India.
* **Network Scale:** 4 supply hubs (origins), 5 demand markets (destinations), plus 1 dummy absorption sink.
* **Volume:** 900 tonnes available supply across hubs; 850 tonnes realized market demand.

---

## Key Assumptions

| Parameter | Value / Policy |
| --- | --- |
| **Freight Rate** | Flat ₹18 / tonne-km across all routes |
| **Balance State** | Unbalanced ($Supply > Demand$). Handled via a Dummy Destination of 50 tonnes |
| **Dummy Cost** | ₹0 / tonne-km transport cost (pure structural balancing sink) |
| **Vehicle Fleet** | Homogeneous fleet with unconstrained route-level truck availability |
| **Route Distances** | Approximate road network distance via primary National Highways |

### Formulas & Heuristics

* **Unit Transportation Cost ($c_{ij}$):**

$$c_{ij} = \text{Distance}_{ij} \times 18$$


* **Total Cost Objective ($Z$):**

$$\min Z = \sum_{i=1}^{m} \sum_{j=1}^{n} c_{ij} x_{ij}$$


* **Supply Limit:**

$$\sum_{j=1}^{n} x_{ij} \le S_i \quad \forall i$$


* **Demand Target:**

$$\sum_{i=1}^{m} x_{ij} = D_j \quad \forall j$$


* **VAM Penalty:**

$$\text{Penalty} = \vert{}c_{i, \text{second lowest}} - c_{i, \text{lowest}}\vert{}$$



---

Pipeline

* **Data Engineering & Matrix Design:** Formulated the distance matrix (km) and unit shipping cost matrix ($₹/\text{tonne}$) for 4 origins and 5 destinations.
* **Matrix Balancing:** Introduced a 50-tonne dummy market with zero shipping cost to convert the unbalanced problem into an equality-constrained model.
* **Heuristic Baseline (NWCM):** Stepped through supply and demand allocations strictly from the top-left cell to establish an unoptimized baseline cost.
* **Advanced Heuristic (VAM):** Evaluated unit penalty differentials across active rows and columns to sequentially allocate freight to lowest-cost lanes.
* **Exact Optimization (LP / Solver):** Built and solved the Linear Programming model using Python (`scipy.optimize.linprog` / PuLP) and Excel Solver (Simplex LP) to determine the global optimum.
* **Comparative Benchmarking:** Computed variance matrices, cost differences, and surplus inventory positioning across all three approaches.

---

Benchmark Results

| Optimization Method | Total Logistics Cost | Variance vs. Optimum | Complexity / Efficiency |
| --- | --- | --- | --- |
| **North-West Corner Method (NWCM)** | ₹14,88,780 | +20.74% | $O(m + n)$ (naive positional) |
| **Vogel’s Approximation Method (VAM)** | ₹12,35,700 | +0.22% | $O((m+n)^2)$ (penalty heuristic) |
| **LP Solver (Simplex LP)** | **₹12,33,000** | **0.00% (Baseline)** | Exact global optimum |

---

Optimal Route Allocation (Tonnes)

| Origin \ Destination | Gurugram | Karnal | Ambala | Sirsa | Kurukshetra | Dummy (Surplus) | Total Dispatched |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **Faridabad** | 220 | 0 | 0 | 0 | 0 | 30 | **250** |
| **Panipat** | 0 | 140 | 160 | 0 | 0 | 0 | **300** |
| **Rohtak** | 0 | 40 | 0 | 0 | 110 | 0 | **150** |
| **Hisar** | 0 | 0 | 0 | 140 | 40 | 20 | **200** |
| **Delivered Demand** | **220** | **180** | **160** | **140** | **150** | **50** | **900** |

---

Key Insights

* **Heuristic Efficiency:** VAM eliminates **98.9% of the cost inefficiency** present in the NWCM solution, finishing within **0.22% (₹2,700)** of the exact LP solution without running an iterative solver.
* **Surplus Staging Strategy:** The LP solver leaves the 50-tonne system surplus stored at **Faridabad (30t)** and **Hisar (20t)**, avoiding unnecessary long-distance line hauls to central distribution hubs.
* **Strategic Hub Specialization:** Panipat exclusively fulfills northern industrial demand (140t to Karnal, 160t to Ambala), minimizing cross-district transit miles.
* **Cost Disparity:** Relying on naive top-down routing schedules (NWCM) inflates operating logistics expenses by **₹2,55,780** (+20.74%) over the optimal plan on a single 900-tonne distribution cycle.

---

Limitations

* **Linear Freight Simplification:** Applying a uniform ₹18/tonne-km rate ignores fixed terminal handling fees, loading/unloading charges, and economies of scale over longer distances.
* **Unconstrained Capacity:** Transit corridors and intermediate routes are assumed to have infinite vehicle throughput without highway congestion or toll differentiation.
* **Zero Holding Cost Assumption:** Surplus inventory assigned to the dummy destination incurs zero holding or warehouse penalty costs, treating retained stock at Faridabad and Hisar as cost-free.

---

## Repo Structure


data/
  ├── distance_matrix.csv        # Road distances between hubs (km)
  └── cost_matrix.csv            # Calculated shipping costs (₹/tonne)
python/
  ├── transportation_solver.py   # NWCM, VAM, and SciPy Simplex implementation
  └── benchmark_comparison.py    # Cost delta and variance calculation script
excel/
  └── transport_solver.xlsx      # Interactive matrix with Excel Solver add-in


Dashboard of the Project is 
<img width="1231" height="687" alt="image" src="https://github.com/user-attachments/assets/3553872c-c709-44cf-a7b0-0e586bb6bf34" />





## Tech Stack

Python (`numpy`, `pandas`, `scipy.optimize`) · Excel (Solver Add-in, Simplex LP) · Markdown
