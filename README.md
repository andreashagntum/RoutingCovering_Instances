# RoutingCovering_Instances
This folder contains several set of test instances for the Multi-Vehicle Covering Tour Problem (m-CTP and m-CTP-p), the Vehicle Routing Problem with Demand Allocation (VRDAP), and for a real-world application for
distributing (food) aid in Afghanistan during the May 2024 Floods in Baghlan and Takhar provinces.
Beside the latter, all test instances were obtained from public github repositories by Bruno Mattos:
- m-CTP(-p): [https://github.com/brunomattos1/VRP-with-lockers-instances](https://github.com/brunomattos1/m-ctp_instances)
- VRDAP: [https://github.com/brunomattos1/VRP-with-lockers-instances](https://github.com/brunomattos1/VRP-with-lockers-instances)

All instances were transformed into a standardized, easy to read format. 


**File format:**
.csv files are formatted with ; as separator and , as decimal.


**Data sources:**
Besides the latter instance set, all instances were obtained from the aforementioned git repositories, which are also referred to in Oliveira et al. (2024, DOI 10.1007/s10288-025-00584-0) and Oliveira et al. (2025, DOI 10.1007/s10732-025-09557-2).
Below are the papers in which the instances were first proposed:
- mandatory m-CTP-p (Oliveira) and non-mandatory m-CTP-p (Oliveira):
	- originally proposed in Oliveira, Pessoa, Robodero (2024): New cuts and a branch-cut-and-price model for the multi-vehicle covering tour problem, DOI 10.1007/s10288-025-00584-0
- m-CTP (Pham)
	- originally proposed in Pham, Hà, Nguyen (2017): Solving the multi-vehicle multi-covering tour problem, DOI 10.1016/j.cor.2017.07.009
- m-CTP-p (Glize)
	- originally proposed in Glize et al. (2020): Exact methods for mono-objective and Bi-Objective Multi-Vehicle Covering Tour Problems, DOI 10.1016/j.ejor.2019.11.045
- VRDAP (Ghoniem)
	- originally proposed in Reihaneh & Ghoniem (2018): A multi-start optimization-based heuristic for a food bank distribution problem DOI 10.1057/s41274-017-0220-9


**Upper and Lower Bounds:**
Sources (bounds of value 0 indicate that no information for the respective bound type was available at the time of writing):
- VRDAP:
	- no lower bound information available at the time of writing
    - lower bounds are self-computed by solving the LP relaxation of the model proposed in DOI 10.48550/arXiv.2411.17510
	- upper bound information obtained from Bruno Oliveira (bounds derived using the ILS in Oliveira (2025), DOI 10.1007/s10732-025-09557-2)
- mandatory m-CTP-p (Oliveira) and non-mandatory m-CTP-p (Oliveira):
	- upper and lower bounds obtained from the supplementary material of Oliveira (2024), DOI 10.1007/s10288-025-00584-0
- m-CTP (Pham):
	- upper and lower bounds obtained from the supplementary material of Oliveira (2024), DOI 10.1007/s10288-025-00584-0
	- Note: Oliveira (2024) does not underperform Pham (2017) on any m-CTP-instances
- m-CTP-p (Glize)
	- upper and lower bounds obtained from the supplementary material of Oliveira (2024), DOI 10.1007/s10288-025-00584-0
	- Note: Oliveira (2024) does not underperform Glize (2020) on any m-CTP-p-instances


**Further remarks:**
- the test instances used by Pham (2017) and Glize (2020) are, except for their value in q, identical
- the proposed Afghanistan Food Aid Instances have several additional pieces of information:
	- they distinguish between edge costs and edge distances. Only the latter is relevant for the satisfaction of the q constraints. For all other instances, q constraints refer to the costs of the used edges.
	- they also contain information on financial routing and covering costs (*_financial_costs.json and *_financial_covering_costs.json) and on covering distances (*covering_dists.json). While this information is not used by the algorithm, it can be useful to ex-post analyze the solution.
	- finally, they contain a map that visualizes the test instance

For further information on the latter test instances set, please refer to the README file in the corresponding directory.
