# Karachi Emergency Route Optimizer

A C++ Data Structures and Algorithms project that models a road network
of Karachi and helps identify suitable emergency hospitals based on road
connectivity and travel conditions.

The application allows a user to select an incident location, find a
hospital using either **Dijkstra's Algorithm** or **Breadth-First Search
(BFS)**, inspect the recommended route, and simulate changes to road
conditions such as closures and traffic congestion.

------------------------------------------------------------------------

## Table of Contents

-   [Project Overview](#project-overview)
-   [Main Features](#main-features)
-   [Core Concepts](#core-concepts)
-   [Algorithms Used](#algorithms-used)
-   [Project Architecture](#project-architecture)
-   [How the Components Work
    Together](#how-the-components-work-together)
-   [Program Workflow](#program-workflow)
-   [Road Closures and Traffic
    Updates](#road-closures-and-traffic-updates)
-   [Important Variables and Terms](#important-variables-and-terms)
-   [Difference Between Path Length and Hop
    Count](#difference-between-path-length-and-hop-count)
-   [Technologies and Concepts](#technologies-and-concepts)
-   [Building and Running](#building-and-running)
-   [Possible Future Improvements](#possible-future-improvements)

------------------------------------------------------------------------

## Project Overview

The **Karachi Emergency Route Optimizer** represents locations in
Karachi as vertices (nodes) and roads connecting those locations as
edges in a graph.

When an emergency occurs, the user enters the incident location. The
application evaluates registered hospitals and recommends a hospital
according to the selected routing method:

1.  **Minimum travel time using Dijkstra's Algorithm**
2.  **Minimum number of road segments (hops) using BFS**

The project also simulates changing road conditions. A road can be
closed, reopened, or assigned a different traffic congestion multiplier.
These changes affect subsequent route calculations.

The main purpose is to demonstrate how graph data structures and graph
traversal/shortest-path algorithms can be applied to a practical
route-planning problem.

## Main Features

-   Display registered Karachi locations and hospitals.
-   Find a hospital with the minimum estimated travel time using
    Dijkstra's Algorithm.
-   Find a hospital using the fewest road segments with BFS.
-   Evaluate and rank routes to all registered hospitals.
-   Display a detailed route breakdown.
-   Close a road corridor and prevent routing algorithms from using it.
-   Reopen a previously closed road.
-   Update a road's traffic congestion multiplier.
-   Reset all road conditions.
-   Recalculate a cached route when road conditions change.

## Core Concepts

### 1. Graph

A graph is a data structure made up of:

-   **Vertices (nodes):** The entities or places represented in the
    graph.
-   **Edges:** The connections between vertices.

In this project:

-   Each Karachi location is represented by a vertex.
-   Each road connecting locations is represented by an edge.

A graph is suitable for this project because a road network consists of
places connected by roads, and routes can be found by traversing those
connections.

### 2. Adjacency List

An adjacency list stores the neighbouring vertices connected to each
vertex.

In this project, `RoadGraph` provides an adjacency list for a location.
The routing algorithms use it to examine the roads leaving the current
location.

The adjacency list is represented using the project's custom linked-list
data structure.

### 3. Queue

A queue follows the **FIFO (First In, First Out)** principle.

The first element inserted is the first element removed.

BFS uses a queue to process locations in the order in which they are
discovered. This is what allows BFS to explore the graph level by level.

### 4. Linked List

A linked list consists of nodes connected through pointers. The graph
uses custom linked lists to store the road edges in its adjacency lists.

### 5. Hop

A **hop** is one edge or road segment travelled between two connected
locations.

For example:

`A -> B -> C -> D`

This route contains:

-   A to B: 1 hop
-   B to C: 1 hop
-   C to D: 1 hop

Total: **3 hops**.

A hop measures the number of road segments, not the distance or travel
time. A short road and a long road both count as one hop.

### 6. Distance, Travel Time, and Hops

These are three different measurements:

  Measurement   Meaning
  ------------- ---------------------------------------------
  Hops          Number of road segments in a route
  Distance      Total road distance, measured in kilometres
  Travel time   Estimated time required to travel the route

BFS in this project focuses on hops. Dijkstra focuses on accumulated
travel time.

------------------------------------------------------------------------

## Algorithms Used

### Breadth-First Search (BFS)

BFS stands for **Breadth-First Search**.

It explores a graph level by level, visiting locations according to
their number of edges from the starting location.

In this project, BFS is used to find a route containing the minimum
number of road segments (hops).

The general process is:

1.  Validate the source and destination locations.
2.  Mark the source as visited.
3.  Insert the source into the queue.
4.  Remove the next location from the queue.
5.  Examine its adjacent road edges.
6.  Skip closed roads, invalid locations, and already visited locations.
7.  Mark newly discovered locations as visited.
8.  Store the previous location in the `parent` array.
9.  Insert newly discovered locations into the queue.
10. Reconstruct the route using the `parent` array.

The implementation uses a custom queue and the graph's adjacency lists.

**Important:** BFS minimizes the number of edges in an unweighted graph.
It does not necessarily find the route with the shortest distance or
minimum travel time.

The BFS implementation also includes a connectivity check that explores
the network from a starting location and reports which registered
locations are reachable.

### Dijkstra's Algorithm

Dijkstra's Algorithm finds a shortest path in a graph with non-negative
edge weights.

In this project, the relevant edge weight is the estimated travel time
for a road.

The algorithm considers the cumulative travel time along a route.
Therefore, it can prefer a route with more road segments if that route
takes less time overall.

The project also stores route information such as total travel time and
total distance in its Dijkstra result.

**Important:** Dijkstra is used here for minimum estimated travel time,
not minimum number of hops.

### BFS vs. Dijkstra

  -----------------------------------------------------------------------
  Feature                 BFS                     Dijkstra
  ----------------------- ----------------------- -----------------------
  Main objective          Minimum number of hops  Minimum total travel
                                                  time

  Uses a queue            Yes                     Uses the priority
                                                  mechanism implemented
                                                  by the Dijkstra module

  Considers road          No                      Yes
  travel-time weights                             

  Can avoid closed roads  Yes                     Yes

  Result                  Fewest-edge route       Minimum-time route
  -----------------------------------------------------------------------

#### Example

Suppose two possible routes connect an incident location to a hospital:

  Route       Hops   Distance   Travel time
  --------- ------ ---------- -------------
  Route A        3      12 km    20 minutes
  Route B        2      25 km    35 minutes

-   BFS prefers **Route B**, because it has fewer hops.
-   Dijkstra prefers **Route A**, because it has lower travel time.

This example demonstrates why the project provides two routing methods.

------------------------------------------------------------------------

## Project Architecture

The project separates responsibilities into folders so that graph
management, data structures, algorithms, hospital selection, traffic
simulation, and user interaction are handled by different components.

The following is the project structure identified from the project files
shown:

``` text
Karachi-Emergency-Route-Optimizer/
│
├── include/
│   ├── algorithms/
│   │   ├── BFS.hpp
│   │   └── Dijkstra.hpp
│   │
│   ├── core/
│   │   ├── CustomQueue.hpp
│   │   └── CustomLinkedList.hpp
│   │
│   ├── graph/
│   │   ├── KarachiMapData.hpp
│   │   ├── Location.hpp
│   │   ├── Road.hpp
│   │   ├── RoadEdge.hpp
│   │   └── RoadGraph.hpp
│   │
│   ├── routing/
│   │   └── HospitalSelector.hpp
│   │
│   └── simulation/
│       └── TrafficController.hpp
│
└── src/
    ├── algorithms/
    │   ├── BFS.cpp
    │   └── Dijkstra.cpp
    │
    ├── graph/
    │   ├── KarachiMapData.cpp
    │   └── RoadGraph.cpp
    │
    └── ConsoleApp.cpp
```

*This tree lists the files identified in the project structure shown;
additional files may exist in the repository.*

### `graph/` --- Road Network

#### `Location.hpp`

Represents a location in Karachi.

The uploaded application code uses location properties including:

-   `id`: Location identifier.
-   `name`: Location or landmark name.
-   `isHospital`: Indicates whether the location is a hospital.
-   `x` and `y`: Coordinates used by the application.

#### `Road.hpp`

Represents a road in the original road dataset. The console application
creates an array of `Road` objects and uses the road information when
displaying and initializing the network.

#### `RoadEdge.hpp`

Represents a connection from one graph location to another.

The routing code uses properties including:

-   `targetId`: ID of the connected destination location.
-   `distanceKm`: Road distance.
-   `isClosed`: Indicates whether the road is closed.
-   `trafficMultiplier`: Traffic congestion multiplier.
-   `getEffectiveTime()`: Provides the effective travel time for the
    road.

#### `RoadGraph.hpp` and `RoadGraph.cpp`

Manage the road network graph.

The uploaded code uses functions such as:

-   `getLocation(id)`: Retrieves a registered location.
-   `getAdjacencyList(id)`: Retrieves the road connections from a
    location.
-   `loadFromKarachiDataset(...)`: Loads the supplied location and road
    data into the graph.

The graph provides the network information required by BFS, Dijkstra,
and route display functions.

#### `KarachiMapData.hpp` and `KarachiMapData.cpp`

Provide the Karachi location and road dataset used to initialize the
application.

### `core/` --- Custom Data Structures

#### `CustomQueue.hpp`

Provides the queue used by BFS. Its FIFO behaviour ensures that BFS
processes discovered locations in the correct order.

#### `CustomLinkedList.hpp`

Provides the linked-list structure used to store adjacency-list road
edges. The routing code traverses its nodes to examine neighbouring
locations.

### `algorithms/` --- Route-Finding Algorithms

#### `BFS.hpp` and `BFS.cpp`

Implement Breadth-First Search.

The BFS module provides:

-   `BFSResult`: Stores route information, including the path, path
    length, reachability, and hop count.
-   `ConnectivityResult`: Stores the results of a network connectivity
    check.
-   `findShortestHopRoute(...)`: Finds a route with the fewest hops.
-   `checkConnectivity(...)`: Checks which registered locations are
    reachable from a starting location.

#### `Dijkstra.hpp` and `Dijkstra.cpp`

Implement Dijkstra's shortest-path algorithm.

The Dijkstra module is used to find routes with minimum accumulated
travel time. Its result contains route and cost information used by the
hospital-selection module.

### `routing/` --- Hospital Selection

#### `HospitalSelector.hpp` and `HospitalSelector.cpp`

This module uses the routing algorithms to evaluate registered hospitals
and select or rank them.

The uploaded implementation includes these main functions:

  -----------------------------------------------------------------------
  Function                            Responsibility
  ----------------------------------- -----------------------------------
  `findNearestHospital(...)`          Evaluates hospitals using Dijkstra
                                      and selects the best candidate by
                                      travel time, with tie-breaking
                                      rules

  `findFewestHopsHospital(...)`       Evaluates hospitals using BFS and
                                      selects the best candidate by hop
                                      count, with tie-breaking rules

  `findAllHospitalRoutes(...)`        Calculates routes to registered
                                      hospitals and ranks the results
  -----------------------------------------------------------------------

The module also includes helper functions to:

-   Find registered hospitals.
-   Compare candidate hospitals.
-   Apply deterministic tie-breaking rules.

`HospitalSelectionResult` stores the selected hospital information,
route, hop count, travel time, and distance.

**Difference between routing and hospital selection:**

-   BFS and Dijkstra calculate routes between locations.
-   HospitalSelector evaluates the hospitals using those route results
    and decides which hospital should be selected.

### `simulation/` --- Traffic and Road Conditions

#### `TrafficController.hpp` and `TrafficController.cpp`

Manage changes to road conditions.

The uploaded console application uses operations to:

-   Close a road.
-   Reopen a road.
-   Set a traffic multiplier.
-   Read the current multiplier.
-   Get the effective travel time.
-   Reset all road conditions.

These operations update road conditions used by subsequent route
calculations.

### `ConsoleApp.cpp` --- Main User Interface

`ConsoleApp.cpp` contains the `main()` function and the console menu.

It is responsible for:

-   Loading the Karachi dataset.
-   Initializing the graph.
-   Creating the traffic controller.
-   Displaying the menu.
-   Reading and validating user input.
-   Calling the relevant algorithms and modules.
-   Displaying route and hospital-selection results.
-   Keeping track of the most recent route query.
-   Recalculating a route when road conditions change.

The console application coordinates the other components rather than
implementing the graph algorithms itself.

------------------------------------------------------------------------

## How the Components Work Together

The main components communicate in the following way:

``` text
                    ConsoleApp.cpp
                   (Main Menu / Input)
                           |
                           v
                    HospitalSelector
                    /             \
                   v               v
                 BFS             Dijkstra
                   \               /
                    \             /
                     v           v
                       RoadGraph
                           |
                           v
                  Location / RoadEdge
                           |
                           v
                  CustomLinkedList
                  (Adjacency Lists)

                  CustomQueue
                  (Used by BFS)

                  TrafficController
                  (Updates road conditions)
```

### Example: User selects Option 3 (BFS)

1.  `ConsoleApp.cpp` asks the user for the incident location ID.
2.  It calls `HospitalSelector::findFewestHopsHospital(...)`.
3.  HospitalSelector identifies registered hospitals.
4.  It calls `BFS::findShortestHopRoute(...)` for the candidate
    hospitals.
5.  BFS uses `RoadGraph` to retrieve connected road edges.
6.  BFS uses `CustomQueue` to explore the graph and `parent[]` to
    reconstruct paths.
7.  HospitalSelector compares the reachable hospital candidates.
8.  It returns a `HospitalSelectionResult`.
9.  `ConsoleApp.cpp` displays the selected hospital and route.

### Example: User selects Option 2 (Dijkstra)

The flow is similar, but HospitalSelector calls Dijkstra to evaluate
hospitals based on estimated travel time.

### Example: User selects Option 4 (Ranked Hospital Evaluation)

1.  The user enters an incident location.
2.  `ConsoleApp.cpp` calls `findAllHospitalRoutes(...)`.
3.  HospitalSelector evaluates each registered hospital using Dijkstra.
4.  It stores the route results.
5.  It sorts the results according to the implemented ranking rules.
6.  The console displays the ranked hospital list.

------------------------------------------------------------------------

## Program Workflow

When the application starts, the following initialization takes place:

1.  Create the location array.
2.  Create the road array.
3.  Load Karachi locations using `loadKarachiLocations(...)`.
4.  Load Karachi roads using `loadKarachiRoads(...)`.
5.  Create a `RoadGraph` object.
6.  Load the location and road data into the graph using
    `loadFromKarachiDataset(...)`.
7.  Create a `TrafficController` for the graph.
8.  Display the main menu.

### Main Menu Options

    Option Operation
  -------- ------------------------------------------------------------
         1 View all Karachi locations and emergency hospitals
         2 Find nearest hospital using minimum travel time (Dijkstra)
         3 Find hospital using minimum road hops (BFS)
         4 View ranked routing evaluation to all hospitals
         5 Display detailed route breakdown for the current selection
         6 Close a road corridor
         7 Reopen a road corridor
         8 Update road traffic congestion multiplier
         9 Reset all road conditions
        10 Exit the application

------------------------------------------------------------------------

## Road Closures and Traffic Updates

Road conditions affect route availability and travel-time calculations.

### Closing a Road

When the user selects Option 6:

1.  The application displays the road information.
2.  The user enters a road corridor ID.
3.  `TrafficController::closeRoad(...)` is called.
4.  The road is marked closed.
5.  The cached route is marked invalid.

BFS and Dijkstra skip closed roads when searching for routes.

The BFS implementation explicitly skips an edge when `edge.isClosed` is
true.

### Reopening a Road

When the user selects Option 7:

1.  The user enters a road corridor ID.
2.  `TrafficController::reopenRoad(...)` is called.
3.  The road is reopened.
4.  The cached route is invalidated.

### Updating Traffic

When the user selects Option 8:

1.  The application displays the current traffic multiplier.
2.  The user enters a new multiplier.
3.  `TrafficController::setTrafficMultiplier(...)` updates the road.
4.  The cached route is invalidated.

The effective travel time of a road is used by the travel-time routing
logic. BFS still focuses on the number of hops rather than
traffic-weighted travel time.

### Resetting Road Conditions

Option 9 calls `TrafficController::resetAll()` to restore the road
conditions to their normal state and invalidates the cached route.

### Route Cache

The console application stores the most recent route result and the
algorithm used to calculate it.

When road conditions change, the cached route is marked invalid. When
the detailed route is requested again, the application recalculates the
route using the previously selected routing algorithm.

------------------------------------------------------------------------

## Important Variables and Terms

  -----------------------------------------------------------------------
  Variable / Term                     Meaning
  ----------------------------------- -----------------------------------
  `sourceId`                          Starting location for a route
                                      search

  `destId`                            Destination location for a route
                                      search

  `originId`                          Incident location from which a
                                      hospital is needed

  `targetId`                          Destination location of a road edge

  `visited[]`                         Tracks locations that have already
                                      been visited

  `parent[]`                          Stores the previous location used
                                      to reach a location during BFS

  `path[]`                            Stores the final route as a
                                      sequence of location IDs

  `pathLength`                        Number of locations stored in the
                                      route

  `hopCount`                          Number of road segments in the
                                      route

  `reachable`                         Indicates whether a route exists

  `isClosed`                          Indicates whether a road is closed

  `distanceKm`                        Distance associated with a road or
                                      complete route

  `travelTimeMin`                     Estimated travel time

  `trafficMultiplier`                 Factor used to represent traffic
                                      congestion
  -----------------------------------------------------------------------

### `visited[]`

Prevents the search algorithm from repeatedly processing the same
location.

### `parent[]`

In BFS, stores the location from which a new location was discovered. It
is used to reconstruct the final route after the destination is reached.

### `path[]`

Stores the sequence of location IDs in the resulting route.

------------------------------------------------------------------------

## Difference Between Path Length and Hop Count

These two values are related but are not the same.

Consider this route:

`2 -> 5 -> 8 -> 10`

There are four locations and three road segments.

-   `pathLength = 4`
-   `hopCount = 3`

In general, for a valid route containing at least one location:

`hopCount = pathLength - 1`

This is because travelling between four locations requires three
connecting road segments.

If the source and destination are the same location:

-   `pathLength = 1`
-   `hopCount = 0`

------------------------------------------------------------------------

## Technologies and Concepts

The project demonstrates the use of:

-   C++
-   Object-oriented programming
-   Classes and structures
-   Arrays
-   Pointers
-   Custom linked lists
-   Custom queues
-   Graphs
-   Adjacency lists
-   Breadth-First Search
-   Dijkstra's Algorithm
-   Path reconstruction
-   Sorting and tie-breaking
-   Input validation
-   Simulation of dynamic road conditions

------------------------------------------------------------------------

## Building and Running

The project is written in C++. To build it, compile the source files
together and make sure the compiler can find the `include` directory.

A typical command-line compilation pattern is:

``` bash
g++ -std=c++17 -Iinclude src/ConsoleApp.cpp src/algorithms/BFS.cpp src/algorithms/Dijkstra.cpp src/graph/KarachiMapData.cpp src/graph/RoadGraph.cpp -o KarachiRouteOptimizer
```

Run on Windows PowerShell:

``` powershell
.\KarachiRouteOptimizer.exe
```

Run on Linux or macOS:

``` bash
./KarachiRouteOptimizer
```

The command above assumes these are the complete required `.cpp` source
files and that the project uses C++17. If your repository contains
additional source files, include them in the build command. If function
implementations are defined inline in headers, avoid keeping duplicate
out-of-class definitions in the corresponding `.cpp` files.

------------------------------------------------------------------------

## Possible Future Improvements

-   Add a graphical map interface to visualize locations and selected
    routes.
-   Display route alternatives rather than only the selected route.
-   Add more detailed road and traffic data.
-   Improve route visualization with turn-by-turn map directions.
-   Add automated tests for graph operations and routing algorithms.
-   Add file-based or database-based storage for road and location data.
-   Add more realistic travel-time estimation.

------------------------------------------------------------------------

## Learning Outcomes

This project demonstrates how fundamental Data Structures and Algorithms
concepts can be combined to solve a practical problem.

The key learning outcomes include:

1.  Representing a real-world road network using a graph.
2.  Implementing and using custom data structures.
3.  Applying BFS to find minimum-hop routes.
4.  Applying Dijkstra to find minimum-travel-time routes.
5.  Reconstructing paths using parent information.
6.  Separating route calculation from hospital selection.
7.  Simulating dynamic changes to a graph.
8.  Organizing a C++ project into modular components.

------------------------------------------------------------------------

**Project:** Karachi Emergency Route Optimizer\
**Language:** C++\
**Subject Area:** Data Structures and Algorithms
