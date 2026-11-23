<div align="center">

<sub><b>DESIGN AND ANALYSIS OF ALGORITHMS</b></sub><br>
<sub>CAPSTONE PROJECT</sub>

# MINI-COURIER PLANNER

*An Integrated Parcel Selection and Delivery Route Planning System*

![Python](https://img.shields.io/badge/Python-3-3776AB?logo=python&logoColor=white)
![Dependencies](https://img.shields.io/badge/dependencies-none-success)
![Tests](https://img.shields.io/badge/unit%20tests-passing-brightgreen)

</div>

---

### PROJECT TEAM AND SUBMISSION DETAILS

| | |
|:--|:--|
| **Student Name** | Anish Kumar Jha |
| **Roll Number** | 2501010273 |
| **Programme / Section** | B.Tech CSE Core (Section D) |
| **University / College** | K.R. Mangalam University |
| **Faculty Name** | Ruchika Bhakhar |
| **Submission Date** | 20th November 2026 |

*Submitted in partial fulfilment of the requirements of the subject*

**DESIGN AND ANALYSIS OF ALGORITHMS**

---

## Table of Contents

- [Abstract](#abstract)
- [1. Introduction and Problem Definition](#1-introduction-and-problem-definition)
  - [1.1 Background](#11-background)
  - [1.2 Formal Problem Statement](#12-formal-problem-statement)
  - [1.3 Why These Algorithms](#13-why-these-algorithms)
- [2. Objectives and Scope](#2-objectives-and-scope)
  - [2.1 Assumptions](#21-assumptions)
  - [2.2 Scope Boundaries](#22-scope-boundaries)
- [3. System Design and Workflow](#3-system-design-and-workflow)
  - [3.1 Module Description](#31-module-description)
  - [3.2 Input Data](#32-input-data)
- [4. Algorithms and Complexity Analysis](#4-algorithms-and-complexity-analysis)
  - [4.1 Parcel Prioritization — Comparison Sorting](#41-parcel-prioritization--comparison-sorting)
  - [4.2 Package Selection — 0/1 Knapsack Dynamic Programming](#42-package-selection--01-knapsack-dynamic-programming)
  - [4.3 Graph Representation and Dijkstra's Algorithm](#43-graph-representation-and-dijkstras-algorithm)
  - [4.4 Delivery Tour — Nearest-Neighbor TSP Heuristic](#44-delivery-tour--nearest-neighbor-tsp-heuristic)
  - [4.5 Complexity Summary](#45-complexity-summary)
- [5. Implementation Details](#5-implementation-details)
  - [5.1 How to Execute](#51-how-to-execute)
- [6. Testing and Results](#6-testing-and-results)
  - [6.1 Unit Tests](#61-unit-tests)
  - [6.2 Sample Execution Output](#62-sample-execution-output)
  - [6.3 Interpretation of the Sample Run](#63-interpretation-of-the-sample-run)
- [7. Performance Evaluation](#7-performance-evaluation)
  - [7.1 Benchmark Results](#71-benchmark-results)
  - [7.2 Observations](#72-observations)
- [8. Limitations and Future Scope](#8-limitations-and-future-scope)
- [9. Conclusion](#9-conclusion)
- [References](#references)
- [Appendix A: Submission Checklist](#appendix-a-submission-checklist)

---

## Abstract

Mini-Courier Planner is a Python application that integrates sorting, dynamic programming, graph algorithms and approximation techniques to solve a simplified courier delivery-planning problem. The system receives parcel details, a road network and a vehicle capacity. It prioritizes parcels, selects a high-value feasible set using 0/1 Knapsack dynamic programming, calculates shortest paths using Dijkstra's algorithm and constructs an approximate delivery tour using the nearest-neighbor TSP heuristic.

A benchmark mode records runtime measurements, while unit tests check important algorithmic behavior. The project uses CSV input files and Python's standard library only, so it runs on any machine with a standard Python 3 installation and no additional dependencies.

**Results at a glance**

| <h3>12</h3> | <h3>6</h3> | <h3>445</h3> | <h3>28 km</h3> |
|:-:|:-:|:-:|:-:|
| parcels in sample dataset | parcels selected | maximum total value | approximate tour length |

**Keywords:** *sorting, 0/1 knapsack, dynamic programming, Dijkstra's algorithm, Traveling Salesperson Problem, nearest-neighbor heuristic, complexity analysis.*

---

## 1. Introduction and Problem Definition

### 1.1 Background

Courier planning involves selecting parcels that can fit into a vehicle and deciding the order in which delivery locations should be visited. In practice, a dispatcher must answer several questions before a vehicle leaves the depot: which parcels are most urgent, which combination of parcels makes the best use of limited load capacity, how far each destination is from the depot, and in what order the stops should be visited to keep the total distance low.

The problem therefore combines multiple algorithmic decisions: sorting urgent parcels, optimizing package selection under a capacity constraint, finding shortest paths in a road network and approximating a low-distance tour. Each of these maps to a classic topic in the Design and Analysis of Algorithms course, which makes the courier scenario a natural capstone for integrating them in a single working system.

The project implements a prototype using sample road distances. It is intended for academic demonstration and is not connected to live maps, GPS, traffic feeds or a commercial courier service.

### 1.2 Formal Problem Statement

Given a set of n parcels, a weighted undirected road graph G = (V, E) with a designated depot vertex and an integer vehicle capacity W, the planner must produce a delivery plan satisfying the following:

| Element | Description |
|:--|:--|
| **Input — parcels** | For each parcel: ID, destination, weight, value, priority and delivery deadline. |
| **Input — road network** | Locations (vertices) and roads (edges) with non-negative distances in kilometres. |
| **Input — parameters** | Depot location (default DEPOT) and vehicle capacity (default 25 kg). |
| **Output 1** | A prioritized parcel queue ordered by urgency. |
| **Output 2** | A subset of parcels whose total weight does not exceed W and whose total value is maximum. |
| **Output 3** | Shortest-path distances from the depot to each selected destination. |
| **Output 4** | An approximate visiting order and road path that starts and ends at the depot, with total distance. |

### 1.3 Why These Algorithms

Each stage of the planner uses the technique best suited to its sub-problem. The table below summarizes the design choice and the reason behind it.

| Stage | Technique | Rationale |
|:--|:--|:--|
| **Prioritization** | Comparison sorting | Produces a deterministic, urgency-ordered queue in O(n log n) time. |
| **Load selection** | 0/1 Knapsack (DP) | Each parcel is taken whole or not at all; DP guarantees the optimal value under the capacity limit, unlike a greedy approach. |
| **Distance computation** | Dijkstra's algorithm | Road distances are non-negative, so Dijkstra gives exact shortest paths efficiently. |
| **Route construction** | Nearest-neighbor TSP heuristic | Exact TSP is NP-hard; the heuristic gives a good tour quickly for small stop counts. |

---

## 2. Objectives and Scope

The project was designed around the following objectives:

- Prioritize parcels using priority score and delivery deadline.
- Select a maximum-value parcel subset without exceeding vehicle capacity.
- Represent delivery points and roads as a weighted adjacency-list graph.
- Calculate shortest paths from the depot using Dijkstra's algorithm.
- Create an approximate route visiting all selected destinations and returning to the depot.
- Measure runtime of sorting, knapsack DP and Dijkstra for sample input sizes.
- Validate important algorithmic operations using unit tests.

### 2.1 Assumptions

To keep the model well defined and the analysis tractable, the following assumptions are made:

- Parcel weights, values and priorities are positive integers.
- Higher priority values indicate greater urgency; earlier deadlines break priority ties.
- Each parcel is either fully selected or not selected.
- Roads are undirected and have non-negative distances.
- All selected delivery destinations are reachable from the depot.
- Multiple parcels at the same destination require one stop.
- The route returns to the depot after the final delivery.

### 2.2 Scope Boundaries

| In scope | Out of scope |
|:--|:--|
| Single vehicle with a fixed integer capacity | Multiple vehicles and fleet assignment |
| Static, illustrative road distances | Live maps, GPS and traffic feeds |
| Priority and deadline based ordering | Delivery time windows and service durations |
| Approximate tour via nearest-neighbor | Guaranteed optimal TSP tours |
| Command-line interface and CSV files | Graphical interface and route visualization |

---

## 3. System Design and Workflow

The program is divided into independent modules. Input files are read from the data folder, algorithms are applied in sequence, and the resulting plan is printed and saved under outputs. Keeping each concern in its own module makes the code easy to test and allows any algorithm to be replaced without affecting the rest of the pipeline.

The workflow has five steps: (1) load parcels and the road network; (2) sort parcels by priority and deadline; (3) run the knapsack DP to choose the load; (4) run Dijkstra from the depot to measure distances to the chosen destinations; and (5) build the delivery tour with the nearest-neighbor heuristic and write the plan to the output folder. Figure 1 shows this flow.

<p align="center">
  <img src="images/figure1.png" alt="Figure 1. Overall system workflow." width="420"><br>
  <em>Figure 1. Overall system workflow.</em>
</p>

### 3.1 Module Description

| Module | Responsibility |
|:--|:--|
| **`models.py`** | Defines the Parcel data model. |
| **`io_utils.py`** | Loads parcel and road CSV files and writes output text. |
| **`algorithms.py`** | Implements sorting, knapsack, Dijkstra and nearest-neighbor TSP. |
| **`planner.py`** | Combines algorithm outputs into a complete delivery plan. |
| **`benchmark.py`** | Measures runtime for selected algorithm components. |
| **`main.py`** | Provides the command-line interface and saves generated results. |

### 3.2 Input Data

The planner reads two CSV files from the data folder. The parcel file describes each parcel; the road file describes the weighted road network. The sample parcel dataset contains 12 parcels (P001 to P012) destined for locations A to L, with weights between 2 kg and 9 kg and priorities from 1 to 5. The sample road graph contains the illustrative locations together with a depot named DEPOT.

| Parcel attribute | Meaning |
|:--|:--|
| **ID** | Unique parcel identifier, used as the final tie-breaker when sorting. |
| **Destination** | Location where the parcel must be delivered. |
| **Weight (kg)** | Capacity consumed in the knapsack; a positive integer. |
| **Value** | Benefit of delivering the parcel; the knapsack maximizes the total. |
| **Priority** | Urgency score from 1 to 5; higher means more urgent. |
| **Deadline** | Latest delivery date and time; earlier deadlines win priority ties. |

---

## 4. Algorithms and Complexity Analysis

This section describes the four algorithmic components, gives their pseudocode, and analyzes their time and space complexity. Throughout, n is the number of parcels, W is the integer vehicle capacity, V and E are the numbers of vertices and edges in the road graph, and k is the number of unique selected destinations.

### 4.1 Parcel Prioritization — Comparison Sorting

Parcels are sorted by descending priority, ascending deadline and parcel ID. The tie-breaking rules make the output deterministic: the same input always produces the same queue. Python's built-in comparison sort has O(n log n) time complexity and O(n) auxiliary space for the returned sorted list.

```text
SORT-PARCELS(parcels):
    return sorted(parcels, key = (-priority, deadline, id))
```

In the sample data, parcels P004, P001 and P007 all have priority 5, so their deadlines (09:30, 10:00 and 11:00 on 10 November) decide their order. The queue is an ordering of urgency; it does not by itself decide what is loaded, which is the job of the next stage.

### 4.2 Package Selection — 0/1 Knapsack Dynamic Programming

Let `dp[i][c]` denote the maximum value obtainable using the first i parcels with capacity c. If parcel i has weight w and value v, then if w is greater than c the previous result is retained. Otherwise `dp[i][c]` is the maximum of excluding the parcel and including it plus the best result for the remaining capacity. The table is traced backwards to recover the selected parcel list.

> **Recurrence**
>
> ```text
> dp[i][c] = dp[i−1][c] if w > c
> dp[i][c] = max( dp[i−1][c], v + dp[i−1][c−w] ) otherwise
> ```

```text
KNAPSACK(parcels, W):
    dp[0..n][0..W] = 0
    for i = 1 to n:
        for c = 0 to W:
            dp[i][c] = dp[i-1][c]
            if weight[i] <= c:
                dp[i][c] = max(dp[i][c], value[i] + dp[i-1][c - weight[i]])
    # trace back from dp[n][W] to recover the chosen parcels
    c = W
    for i = n down to 1:
        if dp[i][c] != dp[i-1][c]:
            select parcel i; c = c - weight[i]
    return selected, dp[n][W]
```

**Time complexity:** O(nW). **Space complexity:** O(nW), where n is the number of parcels and W is the integer vehicle capacity. The algorithm is pseudo-polynomial: its running time depends on the numeric value of W, which is why capacity is expressed in integer units.

A greedy strategy that simply loads the highest-priority parcels first can fill the vehicle with a poor combination. The DP considers every combination implicitly and therefore guarantees the optimal total value, which is why it is preferred here.

<p align="center">
  <img src="images/figure2.png" alt="Figure 2. 0/1 Knapsack DP flowchart." width="380"><br>
  <em>Figure 2. 0/1 Knapsack DP flowchart.</em>
</p>

### 4.3 Graph Representation and Dijkstra's Algorithm

The road network is represented using an adjacency list. Each vertex stores its adjacent vertices and edge distances. This representation uses space proportional to the number of vertices and edges, and is well suited to sparse road networks. Dijkstra's algorithm initializes the source distance to zero and all other distances to infinity. It repeatedly extracts the closest vertex from a min-priority queue and relaxes its outgoing edges. Since all road distances are non-negative, Dijkstra's algorithm is applicable.

```text
DIJKSTRA(graph, source):
    dist[v] = infinity for all v; dist[source] = 0
    heap = [(0, source)]
    while heap is not empty:
        (d, u) = extract-min(heap)
        if d > dist[u]: continue
        for (v, w) in graph[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w; prev[v] = u
                push(heap, (dist[v], v))
    return dist, prev   # prev lets us rebuild each path
```

With a binary heap, the time complexity is O((V+E) log V), and the graph plus auxiliary structures require O(V+E) space. The predecessor map `prev` allows the actual road path, not just its length, to be reconstructed.

<p align="center">
  <img src="images/figure3.png" alt="Figure 3. Dijkstra shortest-path flowchart." width="380"><br>
  <em>Figure 3. Dijkstra shortest-path flowchart.</em>
</p>

### 4.4 Delivery Tour — Nearest-Neighbor TSP Heuristic

The route begins at the depot. At each step, the algorithm uses shortest-path distances to choose the nearest unvisited delivery destination. After all selected destinations have been visited, it adds a shortest path back to the depot. This is a heuristic for the Traveling Salesperson Problem and does not guarantee a globally optimal tour.

```text
NEAREST-NEIGHBOR-TOUR(graph, depot, stops):
    current = depot; unvisited = set(stops); tour = [depot]
    while unvisited is not empty:
        run DIJKSTRA from current
        next = argmin over s in unvisited of dist[s]
        append shortest path current -> next to tour
        unvisited.remove(next); current = next
    append shortest path current -> depot to tour
    return tour
```

For k unique selected destinations, the implementation runs Dijkstra once per selected leg, giving an approximate upper bound of O(k(V+E) log V) time. Exact TSP is NP-hard, so a heuristic is the practical choice; the trade-off is that the tour may be longer than the true optimum, because an early greedy choice can force a long return trip later.

<p align="center">
  <img src="images/figure4.png" alt="Figure 4. Nearest-neighbor route construction flowchart." width="380"><br>
  <em>Figure 4. Nearest-neighbor route construction flowchart.</em>
</p>

### 4.5 Complexity Summary

| Component | Time Complexity | Space Complexity |
|:--|:--|:--|
| **Sorting** | O(n log n) | O(n) |
| **0/1 Knapsack DP** | O(nW) | O(nW) |
| **Dijkstra (binary heap)** | O((V+E) log V) | O(V+E) |
| **Nearest-neighbor with Dijkstra legs** | O(k(V+E) log V) | O(V+E) |

---

## 5. Implementation Details

The application is implemented in Python and uses only the standard library: `heapq` provides the priority queue for Dijkstra, `csv` handles input and benchmark output, and `unittest` supports the test suite. Parcel and road information are stored in CSV files. The default depot is DEPOT and the default vehicle capacity is 25 kg. The application accepts command-line arguments to change the input file paths, depot and capacity.

### 5.1 How to Execute

Run commands from the extracted project root, the directory that contains the `courier_planner`, `data` and `tests` folders.

| Command | Purpose |
|:--|:--|
| `python -m courier_planner.main` | Generate the delivery plan with default settings. |
| `python -m courier_planner.main --benchmark` | Generate the plan and run the runtime benchmark. |
| `python -m courier_planner.main --capacity 30 --benchmark` | Change the vehicle capacity to 30 kg and benchmark. |
| `python -m unittest discover -s tests -v` | Run the complete unit-test suite verbosely. |

---

## 6. Testing and Results

### 6.1 Unit Tests

The unit test suite checks parcel ordering, knapsack capacity and value, Dijkstra distance and path reconstruction, and a depot-returning nearest-neighbor tour. The complete test suite was executed successfully during preparation of this report.

| Test case | Expected behavior | Result |
|:--|:--|:-:|
| **Priority sorting** | Highest priority appears first | PASS |
| **Knapsack DP** | Capacity respected; optimum value returned | PASS |
| **Dijkstra** | Correct shortest distance and path | PASS |
| **TSP heuristic** | Tour starts and ends at depot | PASS |

### 6.2 Sample Execution Output

The following evidence images were generated from an actual execution of the project command. The sample dataset contains 12 parcels and the road graph contains the illustrative sample locations.

<p align="center">
  <img src="images/figure5.png" alt="Figure 5. Delivery plan printed by the application." width="470"><br>
  <em>Figure 5. Delivery plan printed by the application.</em>
</p>

<p align="center">
  <img src="images/figure6.png" alt="Figure 6. Runtime benchmark printed by the application." width="560"><br>
  <em>Figure 6. Runtime benchmark printed by the application.</em>
</p>

### 6.3 Interpretation of the Sample Run

The sample run, with a 25 kg vehicle, produced the following plan:

| Item | Result |
|:--|:--|
| **Parcels selected** | 6 of 12 (P004, P007, P002, P006, P010, P009) |
| **Load** | 25 of 25 kg — the vehicle is filled exactly to capacity |
| **Maximum total value** | 445 |
| **Destinations** | D, G, B, F, J, I |
| **Visit order** | DEPOT → G → D → B → F → I → J → DEPOT |
| **Road path** | DEPOT → G → D → A → B → C → F → I → J → DEPOT |
| **Total tour distance** | 28 km, including the return to the depot |

**Capacity is fully used.** The selected weights (3 + 2 + 4 + 7 + 5 + 4) add up to exactly 25 kg, and their values (60 + 45 + 70 + 110 + 95 + 65) add up to 445.

**Value, not priority alone, drives selection.** Parcel P001 has the top priority (5) but is not loaded, while lower-priority parcels such as P009 are. The knapsack maximizes total value within the weight limit, so a high-priority parcel is not guaranteed a place when the capacity is better used by another combination.

**The tour follows the nearest-neighbor rule.** G is the closest selected destination to the depot (3 km), so it is visited first. Locations A and C appear on the road path only as intermediate junctions, since no parcels for them were selected.

| Destination | Distance from depot |
|:--|:--|
| **G** | 3 km |
| **D** | 5 km |
| **B** | 6 km |
| **J** | 6 km |
| **F** | 7 km |
| **I** | 8 km |

*Table: Dijkstra shortest-path distances from DEPOT to each selected destination.*

---

## 7. Performance Evaluation

Benchmark mode measures sorting and knapsack execution for 10, 25, 50, 100 and 160 parcels. The road graph remains the same sample graph for the Dijkstra measurements, so Dijkstra's values are not expected to scale with the parcel count in this experiment. Measurements vary with the machine and software environment.

### 7.1 Benchmark Results

| Parcels | Sorting (ms) | Knapsack (ms) | Dijkstra (ms) |
|:--|--:|--:|--:|
| **10** | 0.0767 | 0.1846 | 0.0171 |
| **25** | 0.1727 | 0.6297 | 0.0253 |
| **50** | 0.3044 | 0.7446 | 0.0164 |
| **100** | 0.4239 | 1.5811 | 0.0179 |
| **160** | 0.7035 | 2.0909 | 0.0179 |

*Table: Runtime measurements saved to `benchmark_results.csv`.*

<p align="center">
  <img src="images/figure7.png" alt="Figure 7. Runtime comparison generated from benchmark_results.csv." width="560"><br>
  <em>Figure 7. Runtime comparison generated from benchmark_results.csv.</em>
</p>

### 7.2 Observations

- **Knapsack is the most expensive stage.** It rises from about 0.18 ms to about 2.09 ms as the input grows from 10 to 160 parcels, consistent with its O(nW) cost at a fixed capacity.
- **Sorting grows gently.** It rises from about 0.08 ms to about 0.70 ms, remaining well below the knapsack time at every size.
- **Dijkstra is essentially flat.** It stays between about 0.016 and 0.025 ms because the same road graph is used for every run; its cost depends on V and E, not on the parcel count.
- **Everything is fast at this scale.** Even for 160 parcels the whole benchmark finishes in a few milliseconds, so the algorithms are comfortably practical for the intended prototype.

---

## 8. Limitations and Future Scope

The current prototype uses static illustrative distances and does not account for traffic, delivery time windows, driver working hours, fuel consumption, service duration or multiple vehicles. Knapsack uses integer capacity units. The nearest-neighbor route is approximate and may be longer than the optimal TSP route.

| Limitation | Planned improvement |
|:--|:--|
| **Static illustrative distances** | Integrate a real mapping service for road distances and traffic-aware routing. |
| **No time windows or driver limits** | Introduce delivery time windows, service durations and driver constraints. |
| **Single vehicle only** | Extend the model to multiple vehicles using vehicle-routing algorithms. |
| **Approximate nearest-neighbor tour** | Compare nearest-neighbor with 2-opt local search or other route heuristics. |
| **Command-line interface only** | Build a graphical interface and interactive route visualization. |

---

## 9. Conclusion

Mini-Courier Planner demonstrates the integration of sorting, dynamic programming, graph algorithms and approximation techniques in one Python application. It prioritizes parcels, selects a high-value feasible load, computes shortest paths and produces an approximate delivery tour. The modular code, sample datasets, unit tests and benchmark output make it suitable for an academic DAA capstone demonstration.

In the sample run, the planner loaded 25 kg of parcels worth 445 and produced a 28 km round trip. The project shows how an optimal method (knapsack DP, Dijkstra) and an approximate method (nearest-neighbor) can be combined: exact algorithms solve the sub-problems that are tractable, and a heuristic handles the one that is not.

Key learnings from the project:

- Choosing the right algorithm for each sub-problem matters more than optimizing any single one.
- Dynamic programming gives optimal selections where greedy rules can fail, at the cost of O(nW) time and space.
- Heuristics such as nearest-neighbor trade guaranteed optimality for speed on NP-hard problems.
- Unit tests and benchmarks make algorithmic claims verifiable rather than assumed.

---

## References

1. Cormen, T. H., Leiserson, C. E., Rivest, R. L. and Stein, C. Introduction to Algorithms. MIT Press.
2. Kleinberg, J. and Tardos, É. Algorithm Design. Pearson.
3. Python Software Foundation. Python 3 Documentation. https://docs.python.org/3/

---

## Appendix A: Submission Checklist

- Fill in student name, roll number, programme, university, faculty and submission date on the cover page.
- Run the project on your own computer and retain your own terminal screenshots.
- Check that all selected destinations are present in the road dataset.
- Include the ZIP source-code folder with the report.
- Be prepared to explain sorting, 0/1 Knapsack, Dijkstra and nearest-neighbor TSP.
