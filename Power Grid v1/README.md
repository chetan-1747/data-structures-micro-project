# Intelligent Power Grid Restoration Decision-Support System

A console program in C++ that helps plan the repair of a storm-damaged power grid. It shows which places have power, finds the cheapest repairs, and suggests the order to repair lines so that hospitals, water treatment and telecom come back before ordinary homes.

Built as a Data Structures micro-project.

## What it does

- Models the grid as a weighted graph of places and power lines.
- Shows who has power, using only repaired lines.
- Finds the cheapest repairs needed to power one place.
- Finds the cheapest repairs needed to reconnect the whole grid.
- Suggests a repair order and can carry out one repair at a time.
- Lets you repair lines or report new damage and see the effect straight away.

## Data structures and algorithms

| Need | What is used | File |
| --- | --- | --- |
| Store the grid | Weighted undirected graph (adjacency list) | `Graph` |
| Who has power | Breadth-First Search from all substations | `Connectivity` |
| Cheapest repairs to one place | Dijkstra's algorithm | `Dijkstra` |
| Cheapest repairs for the whole grid | Kruskal's algorithm | `Kruskal` |
| Avoid cycles in Kruskal's | Union-Find (path compression, union by rank) | `UnionFind` |
| Repair order | Priority queue (array based), gain divided by effort | `Scheduler` |

## Project structure

| File | Purpose |
| --- | --- |
| `Graph.h`, `Graph.cpp` | Nodes, edges, and their DAMAGED or REPAIRED status |
| `UnionFind.h`, `UnionFind.cpp` | Disjoint sets |
| `Connectivity.h`, `Connectivity.cpp` | BFS power check |
| `Dijkstra.h`, `Dijkstra.cpp` | Cheapest repair cost and path to a node |
| `Kruskal.h`, `Kruskal.cpp` | Minimum repair network |
| `Scheduler.h`, `Scheduler.cpp` | Repair priority and schedule |
| `main.cpp` | Interactive menu |
| `tests.cpp` | 55 automatic checks, built as a separate program |

Each `.h` file lists what a module offers, and the matching `.cpp` file holds the code.

## Build and run

Requires `g++` with C++17 support.

```
g++ -std=c++17 main.cpp Graph.cpp UnionFind.cpp Connectivity.cpp Dijkstra.cpp Kruskal.cpp Scheduler.cpp -o grid
```

Run it:

- Windows PowerShell: `.\grid.exe`
- Linux or macOS: `./grid`

To run the tests:

```
g++ -std=c++17 tests.cpp Graph.cpp UnionFind.cpp Connectivity.cpp Dijkstra.cpp Kruskal.cpp Scheduler.cpp -o tests
```

The last line of the output should read `Passed: 55  Failed: 0`.

## Sample grid

The program starts with this grid after a storm: all 7 lines are damaged, and only the two substations have power.

![Sample grid](grid-diagram.png)

| Edge id | Connects | Repair effort |
| --- | --- | --- |
| 0 | Substation A and Substation B | 5 |
| 1 | Substation A and City Hospital | 3 |
| 2 | Substation B and Water Plant | 4 |
| 3 | Substation B and Telecom Tower | 2 |
| 4 | City Hospital and Residential 1 | 6 |
| 5 | Water Plant and Residential 2 | 7 |
| 6 | Residential 1 and Residential 2 | 8 |

Node ids are 0 to 6: Substation A, Substation B, City Hospital, Water Plant, Telecom Tower, Residential 1, Residential 2.

## Menu

| Option | Action |
| --- | --- |
| 1 | Show the grid |
| 2 | Show power status |
| 3 | Repair an edge (enter its edge id) |
| 4 | Report new damage on an edge |
| 5 | Cheapest repair plan for a node (enter its node id) |
| 6 | Minimum repair network for the whole grid |
| 7 | Suggested repair schedule |
| 8 | Carry out the next scheduled repair |
| 9 | Exit |

## Example run

1. Type `1` to see the grid, then `2` to see that only the substations have power.
2. Type `7` to see the suggested order: hospital line, telecom line, water line, then the homes.
3. Press `8` repeatedly. Each press carries out the next repair, and the count of critical facilities without power drops from 3 to 0.
4. Type `4`, then `3` to damage the telecom line. The telecom tower loses power.
5. Type `8` to repair it again, then `9` to exit.

## How it works

- **Repairing a line** changes its status from DAMAGED to REPAIRED. The BFS then runs again, and every place reachable from a substation through repaired lines is marked POWERED.
- **Effort** is the cost of repairing a line. Dijkstra's and Kruskal's use it to find the cheapest repairs.
- **Gain** is the value of the places a repair newly powers. Hospital 10, water plant 8, telecom tower 6, home 1.
- **Scheduling** repeatedly picks the repair with the highest gain divided by effort.

## Limitations and future work

- Repairs are instant. There are no crews, time or budget.
- The sample grid is hardcoded, and the node values are chosen by the team.
- The scheduler is greedy, so it is not guaranteed to find the best possible order.
- Possible next steps: crew limits and repair time, a heap-based priority queue, and loading grids from a file.
