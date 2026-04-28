# AFG — Afghanistan Food Aid Case Study Instances

This directory contains test instances for a real-world case study on helicopter-based food aid distribution in the **Baghlan province of Afghanistan**, motivated by the May 2024 flash floods. The instances were constructed by the authors of the following paper and are not derived from any prior benchmark set:

> Hagn, A., Krause, J., Moreno, L., Stargalla, M. (2026). *An Adaptive Variable Neighborhood Search for a Family of Set 
> Covering Routing Problems with an Application in Disaster Relief Operations.* arXiv Preprint: https://arxiv.org/abs/2411.17510

**If you use these instances in your own work, please cite the paper above.**

---

## Background

The May 2024 heavy rainfall caused extensive flooding across large parts of Afghanistan. Four districts in the province of Baghlan were particularly heavily affected, leaving large parts of the road network impassable or destroyed and making ground-based aid delivery infeasible. In response, the paper proposes a hybrid distribution concept in which **helicopters** (modeled on the Mil Mi-8 AMT, previously operated by the UN Humanitarian Air Service in Afghanistan) conduct circular flights from airports, delivering bulk aid consignments to local **landing sites**. Beneficiaries then travel on foot to their assigned landing site to collect their aid.

The instances model this distribution problem as a **Set Covering Routing Problem (SCRP)**, which combinates features
of the **m-CTP** and the **VRDAP**: given a set of beneficiary locations (customers), potential landing sites (facilities), 
and airports (depots), the goal is to select a subset of landing sites, assign each beneficiary group to one landing site, 
and construct helicopter routes — all while minimizing a weighted combination of routing costs (proxy for operational expenses) 
and covering costs (proxy for burden on beneficiaries).

---

## Instance Construction

Each instance is derived from a **baseline instance** (see `baseline_instance/` subdirectory) by adjusting the five 
parameters described below. The baseline is constructed as follows:

### Beneficiary Locations and Demands

Population density data (WorldPop) and historical records of affected individuals per district (OCHA) are combined to 
compute the number of flood-affected individuals per 1 km² grid cell. Each grid cell with at least one affected individual
becomes a **customer node**, whose demand corresponds to the weight of high-energy biscuits (HEBs) needed to cover its 
beneficiaries' minimum daily calorie intake (2,100 kcal, translating to approximately 0.467 kg of HEBs per person). 
To reduce problem size, all beneficiaries in the same 1 km² cell are aggregated into a single customer node.
The baseline instance covers **5,831 affected individuals** across **558 customer nodes**.
Depending on the service threshold and the affected individuals factor applied, this translates to **432 to 558** customer nodes
and **2819 to 8922** beneficiaries.

### Landing Sites (Facilities)

Candidate landing sites are identified across the affected region by applying a set of terrain and accessibility filters using satellite-derived elevation data (DLR TanDEM-X) and land cover data (ESA WorldCover):

- Minimum area: roughly 100 m × 100 m (i.e. 3 arc seconds x 3 arc seconds)
- Terrain type: grasslands or shrublands (no dense vegetation)
- Minimum distance to waterbodies: 150 m; to other hazardous terrain (such as mountains and cliffs): 70 m
- Maximum local slope: 7% (relaxed if the area's internal slope exceeds 30%, to account for satellite elevation artefacts)
- Local curvature above the 10th percentile of the regional distribution (to exclude flood-prone valleys)
- Maximum elevation: 5,400 m (operational ceiling of the Mil Mi-8 AMT at maximum payload)

Each candidate is also required to lie within 1,000 m of the nearest road node, with no impassable terrain (waterbodies, mountains, dense vegetation) in between. Road distances are sourced from the HOT OpenStreetMap export for Afghanistan and post-processed to connect isolated road segments. After filtering, the 20,430+ raw candidates are reduced to up to **500 representative landing sites** using k-means clustering. Landing sites within a 12-minute walking radius of each other are then further removed. The capacity of each landing site is capped at **200 kg** (to limit on-site workload and avoid single points of failure).

### Depots

All major, regional, or local airports and heliports within 150 km of at least one beneficiary are considered as depot candidates. Because depots are assumed to be uncapacitated and cost-free, only those that are the closest depot to at least one landing site are retained. This yields **2 depots: Taloqan Airport and Bagram Airfield**.

### Fleet and Route Parameters

- **Helicopter type:** Mil Mi-8 AMT
- **Vehicle capacity:** 3,200 kg (4,000 kg maximum payload reduced by 20% for high-elevation performance degradation)
- **Maximum route length:** *q* = 540 km (helicopter range)
- **Maximum stops per route:** *p* = 28 (derived from an 8-hour operating window, 170 km/h average speed, and 10 minutes per stop for landing, unloading, and departure)

### Ground Travel

Last-mile distribution from landing sites to beneficiaries is assumed to be on foot at 4 km/h. Road distances are increased by a factor of 1.2 to account for partial inaccessibility and required detours. One person per household (assumed size: 6 individuals) collects the aid. A beneficiary can be assigned to a landing site only if their ground travel time does not exceed the **maximum covering duration** *CD*<sup>max</sup>.

### Routing and Covering Costs

Two distinct cost components are used in the objective function:

**Routing costs** are designed so that (a) splitting a route into two always increases total cost (i.e., fewer routes are always cheaper), and (b) inter-facility costs are proportional to travel distance. A fixed term is added to depot-adjacent arcs to enforce property (a). Routing costs are then rescaled by the **route cost scaling factor** *ρ*.

**Covering costs** are proportional to the ground travel distance and the number of beneficiaries represented by a customer node (household-weighted distance), serving as a proxy for the burden imposed on beneficiaries.

By adjusting *ρ*, the operator can tune the trade-off between minimizing operational expenses (high *ρ*) and minimizing ground travel burden on beneficiaries (low *ρ*).

---

## Instance Parameters and Naming Scheme

All instances follow the naming format:

```
Baghlan_ai{X}_vCnt{Y}_cD{Z}_sT{W}_rcS{V}
```

| Code | Parameter | Values used |
|---|---|---|
| `ai` | Affected individuals factor (*N*<sup>ben</sup>) — scales the number of beneficiaries relative to the baseline | 0.5, 0.75, 1.0, 1.25, 1.5 |
| `vCnt` | Maximum number of available helicopters (*M*) | 1, 2, 3, 4 |
| `cD` | Maximum covering duration in hours (*CD*<sup>max</sup>) — maximum ground travel time from a beneficiary to their assigned landing site | 1, 2, 3, 4 |
| `sT` | Service threshold in hours (*ST*<sup>max</sup>) — beneficiaries whose closest landing site exceeds this travel time are excluded from the instance | 1, 2, 3, 4 |
| `rcS` | Route cost scaling factor (*ρ*) — controls the weight of routing vs. covering costs in the objective | 0.1, 1, 3, 5, 7 |

Note that instances where *ST*<sup>max</sup> > *CD*<sup>max</sup> are discarded as infeasible by construction (at least one beneficiary would be unreachable). The full instance set (excluding these) contains **1,000 instances**.

**Example:** `Baghlan_ai0.5_vCnt1_cD1_sT1_rcS0.1` is an instance with half the baseline number of beneficiaries, 1 helicopter, a maximum covering duration of 1 hour, a service threshold of 1 hour, and a route cost scaling factor of 0.1.

---

## Additional Files per Instance

Beyond the standard instance data (node locations, demands, arc costs and distances, covering sets), the AFG instances include:

| File suffix                           | Contents                                                                                |
|---------------------------------------|-----------------------------------------------------------------------------------------|
| `*_covering_dists.json`               | Ground travel distances between beneficiary nodes and landing sites                     |
| `*_distances.json`                    | Air travel distances between landing sites and depots/landing sites                     |
| Map file [only for baseline instance] | A map visualizing the instance geography (beneficiary locations, landing sites, depots) |

Note that `*_covering_dists.json` and `*_distances.json` are **not used by the AVNS** during optimization, 
but are provided for ex-post analysis and evaluation of solutions.

**Important:** Unlike all other instance sets in this repository, AFG instances distinguish between **edge costs** (used in the objective function) and **edge distances** (used only to evaluate the *q* route length constraint). For all other instance sets, the *q* constraint refers to routing costs.

---

## Baseline Instance

The `baseline_instance/` subdirectory contains the baseline instance from which all other instances are derived. It corresponds to the parameter setting *N*<sup>ben</sup> = 1, *M* = 4, *CD*<sup>max</sup> = 4, *ST*<sup>max</sup> = 4, *ρ* = 1. Individual instances are then constructed by reading the baseline and adjusting the relevant components (beneficiary counts and demands, covering sets, and cost parameters) according to the instance's parameter values.

---

## Instance Summary

The file `instance_summary.csv` provides a tabular overview of all instances and their key characteristics, including the number of customer nodes, facilities, and beneficiaries for each parameter combination. It also contains empty columns intended for recording solution quality indicators, such as:

- Total routing distance
- Total covering distance
- Number of used helicopters/vehicles
- Objective value

These columns can be filled in to facilitate systematic comparison of results across instances.

---
## Instance Construction and Solution Algorithm
An implementation of the test instance constructor used for the proposed instance, as well as an implementation of the
AVNS used in Hagn et al. (2026) to solve the instances, can be found in the following repository:
https://github.com/andreashagntum/RoutingCovering_AVNS.


## Data Sources

| Data | Source |
|---|---|
| Affected individuals per district | OCHA Afghanistan Natural Disaster Incidents dataset |
| Population density (1 km² grid) | WorldPop Afghanistan 2015–2030 |
| Elevation data | DLR TanDEM-X Global DEM (González et al., 2020, DOI: 10.3390/rs12233961) |
| Land cover / terrain types | ESA WorldCover 10m 2021 v200 (Zanaga et al., 2022, DOI: 10.5281/zenodo.7254221) |
| Road network | HOT OpenStreetMap Afghanistan Roads export |
| Household size | NSIA Estimated Population of Afghanistan 2025–26 |
| Helicopter specifications | BlueSkyRotor Mil Mi-8 AMT data sheet |
| Calorie intake / HEB energy density | Afghanistan Food Security Cluster guidelines; WFP HEB product information |

Sources for the aforementioned data are provided in the paper cited at the beginning of this readme.

Furthermore, test instances can also be constructed from scratch using the data provided in the subdirectory `input_data/`.
