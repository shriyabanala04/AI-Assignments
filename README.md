## Artificial Intelligence - Assignment 2

This repository contains the programming implementations developed for the AI assignment.

### Assignment Topics

#### Part A

**Uninformed Search**

* Breadth-First Search
* Uniform-Cost Search
* Depth-First Search
* Depth-Limited Search
* Iterative Deepening Search
* Bidirectional Search

**Informed Search**

* Greedy Best-First Search
* Best-First Search
* Dijkstra's Algorithm
* A*
* Beam Search
* RBFS
* IDA*
* Weighted A*

**Heuristics**

* Satisficing Search
* Admissible Heuristic
* Heuristic Formulation
* Heuristics Generated from Subproblems

#### Programming Problems

* Dijkstra's Algorithm using Indian city road routes
* UGV navigation with static obstacles
* UGV navigation with dynamic obstacles
* Route finding using Indian cities
* Map coloring of Telangana districts

### Folder Structure

```text
01_uninformed_search/
02_informed_search/
03_heuristics/
04_dijkstra_indian_routes/
05_ugv_static/
06_ugv_dynamic/
07_indian_route_search/
08_ts_map_coloring/
```

Each assignment folder contains a primary Python program along with a brief README.

### Requirements

Python 3.10 or later is recommended.

The only external package required is **Matplotlib**, which is used for visualization.

Install the required package using:

```bash
pip install -r requirements.txt
```

### Running a Program

Navigate to the required assignment folder in a terminal and run:

```bash
python solution.py
```

The UGV navigation and Telangana map-coloring programs generate visual output using Matplotlib.

### Note on Data

The Indian route programs use a compact road graph based on open data to keep the implementations simple and easy to run. Additional road connections can be added to expand the graph.

The Telangana map-coloring program represents the districts as a constraint graph. Telangana currently consists of 33 districts.

### Purpose

The programs are designed to demonstrate the implementation and working of the algorithms directly, without relying on external AI or search libraries. This keeps the underlying logic clear and makes the implementations easier to understand and study.
The graph can be extended by adding more road edges.

The Telangana program models the districts as a constraint graph. The Telangana government currently lists 33 districts.
