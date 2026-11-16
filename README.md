# E-Learning Platform Course Path Optimizer
**SDG 4: Quality Education**

A tool for an online learning platform that suggests the most efficient order for completing courses, based on their prerequisites, so learners spend the least total time.

**▶ Live demo:** https://25a31a05ij.github.io/ADS_AA_PROJECT/

![Course Path Optimizer](screenshots/lava_1_graph_goal_plan.png)

## Design: Lava Stone Red + Midnight Black
The header is black stone with glowing lava-red veins, over a midnight-black stone background. The title is gold serif lettering above a gold divider. The marble textures are drawn in code (SVG fractal noise), so there are no image files and the app works offline.

| Fastest Finish (critical path) | Goal Planner |
|---|---|
| ![Fastest Finish](screenshots/lava_3_fastest_finish.png) | ![Goal Planner](screenshots/lava_5_goal_planner.png) |
| **Graph: critical path** | **Recommend (Min-Heap)** |
| ![Critical path](screenshots/lava_4_graph_critical_path.png) | ![Recommend](screenshots/lava_7_recommend.png) |

## Features
| Feature | Where in the app | Technique |
|---|---|---|
| Courses modelled as a dependency graph; loops are rejected | Course Catalogue, Graph | Directed Acyclic Graph (DAG) |
| Valid course order, best course first | Course Order | Topological Sort (Kahn) + Min-Heap |
| Minimum time to finish everything, critical path | Fastest Finish | Dynamic Programming (longest path) |
| Quickest route between two courses | Route Finder | Dynamic Programming (shortest path) |
| Only the courses needed for a target course | Goal Planner | Topological Sort on a subset |
| Courses to take next, by rating or popularity | Recommend | Min-Heap (top-k) |
| Progress tracking, add/edit/delete courses, JSON import/export | Catalogue, top bar | – |
| Built-in tests | How It Works → Run self-test | 10 tests |

## How to run
**Web app (recommended):** open `index.html` in any browser, or use the live demo link. Nothing to install.

**Python versions (optional),** in the `python/` folder. These need Python 3.8+ and no extra packages.
```
python python/course_path_optimizer_app.py       # desktop window (Tkinter)
python python/course_path_optimizer_cli.py       # command-line menu
python python/course_path_optimizer_cli.py test  # unit tests
```

## Project structure
```
index.html                         Web application (HTML + CSS + JavaScript, single file)
courses.json                       Sample course catalogue (13 courses)
screenshots/                       Sample images of every screen
python/course_path_optimizer_app.py   Desktop GUI version (Python + Tkinter)
python/course_path_optimizer_cli.py   Command-line version (Python)
```

## Algorithms
### 1. DAG + Topological Sort
- Each course is a node, and an edge `A → B` means A is a prerequisite of B.
- Before an edge is added, the app checks whether B can already reach A. If it can, the edge would create a cycle, so it is rejected.
- Kahn's algorithm repeatedly removes a course whose prerequisites are all done. **O((V+E) log V)** with the heap.

### 2. Dynamic Programming on the DAG: O(V+E)
Courses are processed in topological order, so every prerequisite is solved before the course itself.
- **Longest path / critical path:** `finish(v) = duration(v) + max(finish(p) for p in prereqs(v))`. This is the minimum time to complete everything when independent courses are studied in parallel. For the sample data it is **220 h**, against **423 h** taking courses one at a time.
- **Shortest path:** `dist(w) = min(dist(v) + duration(w))` over the edges `v → w`.

### 3. Min-Heap
- A binary min-heap written from scratch: push and pop are O(log n), and building it from a list is O(n).
- Kahn's algorithm uses it in place of a plain queue, so whenever several courses are available, the best one comes next.
- It also powers the top-k recommendations.
- Values where higher is better (rating, popularity) are negated, because it is a min-heap.

### Why this minimises total time
Taking courses one at a time, every valid order adds up to the same hours. Time is saved by:
- taking only the prerequisites a goal needs (Goal Planner);
- studying independent courses in parallel (Fastest Finish and the study levels).
