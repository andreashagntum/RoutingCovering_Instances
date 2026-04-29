# RoutingCovering_Instances

This repository contains test instances for a family of **Set Covering Routing Problems (SCRPs)**, including the Multi-Vehicle Covering Tour Problem (m-CTP and m-CTP-p), the Vehicle Routing Problem with Demand Allocation (VRDAP), and a real-world case study on helicopter-based food aid distribution in Afghanistan.

The instances were used to benchmark and evaluate the **Adaptive Variable Neighborhood Search (AVNS)** proposed in:

> Hagn, A., Krause, J., Moreno, L., Stargalla, M. (2026). *An Adaptive Variable Neighborhood Search for a Family of Set Covering Routing Problems with an Application in Disaster Relief Operations.*  arXiv Preprint: https://arxiv.org/abs/2411.17510

**If you use any of these instances in your own work, please cite the paper above**, as well as the original paper(s) in which the respective instance set was first proposed (see below).

---

## Repository Structure

```
RoutingCovering_Instances/
├── AFG/                             # Case study instances (Afghanistan food aid)
├── Lower and upper bounds/          # Known bounds for all instance sets
├── Solutions/                       # Solutions for all test instances, computed using the algorithm proposed in Hagn et al. (2026)
├── m-CTP-p (Glize)/                 # Benchmark instances from Glize et al. (2020)
├── m-CTP (Pham)/                    # Benchmark instances from Pham et al. (2017)
├── mandatory m-CTP-p (Oliveira)/    # Benchmark instances from Oliveira et al. (2024)
├── non-mandatory m-CTP-p (Oliveira)/# Benchmark instances from Oliveira et al. (2024)
├── Tuning instances/                # Instance names used for hyperparameter tuning
└── VRDAP (Ghoniem)/                 # Benchmark instances from Reihaneh & Ghoniem (2018)
```

---

## Benchmark Instances (from the Literature)

The following five instance sets are obtained from the literature. They were originally hosted in public repositories by Bruno Mattos Oliveira (alias Bruno Oliveira) and have been transformed into a standardized, easy-to-read format:

- **m-CTP(-p) instances**: https://github.com/brunomattos1/m-ctp_instances
- **VRDAP instances**: https://github.com/brunomattos1/VRP-with-lockers-instances

Each instance set, the paper in which it was first proposed, and the source of its bounds are listed below.

### m-CTP (Pham)
- **First proposed in:** Pham, Hà, Nguyen (2017): *Solving the multi-vehicle multi-covering tour problem.* DOI: [10.1016/j.cor.2017.07.009](https://doi.org/10.1016/j.cor.2017.07.009)
- **Bounds:** Upper and lower bounds obtained from the supplementary material of Oliveira et al. (2024), DOI: [10.1007/s10288-025-00584-0](https://doi.org/10.1007/s10288-025-00584-0). Note: Oliveira (2024) does not underperform Pham (2017) on any instance.
-  **Note:** The m-CTP assumes no customer demands, facility capacities, or vehicle capacities. All said values are set to dummy values.
- 
### m-CTP-p (Glize)
- **First proposed in:** Glize et al. (2020): *Exact methods for mono-objective and bi-objective multi-vehicle covering tour problems.* DOI: [10.1016/j.ejor.2019.11.045](https://doi.org/10.1016/j.ejor.2019.11.045)
- **Bounds:** Upper and lower bounds obtained from the supplementary material of Oliveira et al. (2024), DOI: [10.1007/s10288-025-00584-0](https://doi.org/10.1007/s10288-025-00584-0).
- **Note:** Oliveira (2024) does not underperform Glize (2020) on any instance. Note also that the instances used by Pham (2017) and Glize (2020) are identical except for their value of *q*.
-  **Note:** The m-CTP-p assumes no customer demands, facility capacities, or vehicle capacities. All said values are set to dummy values.

### mandatory m-CTP-p (Oliveira) and non-mandatory m-CTP-p (Oliveira)
- **First proposed in:** Oliveira, Pessoa, Roboredo (2024): *New cuts and a branch-cut-and-price model for the multi-vehicle covering tour problem.* DOI: [10.1007/s10288-025-00584-0](https://doi.org/10.1007/s10288-025-00584-0)
- **Bounds:** Upper and lower bounds obtained from the supplementary material of Oliveira et al. (2024), DOI: [10.1007/s10288-025-00584-0](https://doi.org/10.1007/s10288-025-00584-0).
-  **Note:** The m-CTP-p assumes no customer demands, facility capacities, or vehicle capacities. All said values are set to dummy values.

### VRDAP (Ghoniem)
- **First proposed in:** Reihaneh & Ghoniem (2018): *A multi-start optimization-based heuristic for a food bank distribution problem.* DOI: [10.1057/s41274-017-0220-9](https://doi.org/10.1057/s41274-017-0220-9)
- **Bounds:** No lower bounds were available in the literature at the time of writing; the values provided here are self-computed by solving the LP relaxation of the model proposed in DOI: [10.48550/arXiv.2411.17510](https://doi.org/10.48550/arXiv.2411.17510). Upper bounds were obtained from Bruno Oliveira (derived using the ILS in Oliveira (2025), DOI: [10.1007/s10732-025-09557-2](https://doi.org/10.1007/s10732-025-09557-2)).
-  **Note:** The VRDAP assumes no facility capacities, or route length restrictions. All said values are set to dummy values.

---

## Case Study Instances (AFG)

The `AFG/` directory contains instances for a real-world case study on helicopter-based food aid distribution in the Baghlan and Takhar provinces of Afghanistan, motivated by the May 2024 flash floods. These instances were constructed by the authors of the paper cited above and are not derived from any prior benchmark set.

Compared to the benchmark instances, the AFG instances have several additional features:

- They distinguish between **edge costs** and **edge distances**. Only the latter is relevant for satisfying the *q* (route length) constraints; routing costs are used in the objective function.
- They include a separate file containing **covering distances** (`*_covering_dists.json`). These are not used by the AVNS directly but can be useful for ex-post analysis of solutions.
- The underlying baseline instance (located in `AFG/baseline_instance`) contains a **map** visualizing the instance geography.

For full details on the instance construction methodology — including data sources, facility and depot selection criteria, 
demand computation, and cost definitions — please refer to the **README file inside the `AFG/` directory**, and
Section 6.1 and the **supplementary material** of the paper linked above.

---

## Tuning Instances

The `Tuning instances/` directory contains one JSON file per instance set. Each file lists the names of the instances used for hyperparameter tuning of the AVNS, as described in the paper.

---

## Lower and Upper Bounds

The `Lower and upper bounds/` directory contains one CSV file per instance set, reporting the best known lower and upper bounds for each instance.
A bound value of `0` (lower and upper bounds) or `1E+10` (upper bounds) indicates that no information for the respective bound type was available at the time of writing. Sources are as described in the per-instance-set sections above.

---

## File Format

All `.csv` files use `;` as the column separator and `,` as the decimal separator.
