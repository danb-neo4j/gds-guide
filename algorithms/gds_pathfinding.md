# Neo4j GDS Pathfinding Algorithms
## Summary
Pathfinding algorithms identify paths between nodes. The algorithms in the Neo4j Graph Data Science support multiple approaches to this task including identifying the shortest path between two nodes, identifying all paths between given nodes, or evaluating the quality of paths between nodes (among other approaches). 

## Pathfinding Algorithms 
### [Random Walk](https://neo4j.com/docs/graph-data-science/current/algorithms/random-walk/)
   - **Overview**: The Random Walks algorithm starts at a given node an then selects a random neighbor to explore. The algorithm continues this process of random selection for a set number of steps or until it reaches a goal.
   - **Pros**: Random Walks is a straightforward, understandable approach to exploring a large graph space. 
   - **Cons**: The algorithm provides no guarantee that it will find the most efficient paths or that it will reach a provided target. The paths explored may also be redundant. 
   - **When to use**: Random Walks are an effective way to explore the global structure of a graph. Random Walks are also an effective way to sample a graph when generating node embeddings (as is used in Node2Vec and DeepWalk). 

### [Random Walks with Restarts](https://neo4j.com/docs/graph-data-science/current/management-ops/projections/rwr/)
   - **Overview**: Currently classified as an auxiliary procedure in alpha tier, Random Walks with Restarts (RWR) is a variant of the Random Walks algorithm that returns a smaller, structurally representative, subgraph by performing random walks starting from a set of nodes. 
   - **Pros**: RWR can preserve structural features while sampling a larger graph. The implementation can also handle weighted relationships and preserve node label distributions. 
   - **Cons**: The restart property can significantly impact the output. The algorithm can also have an element of randomness, affecting the output from run to run. 
   - **When to use**: RWR enables you to sample a large graph while preserving structural features. RWR can also more closely sample the neighborhood of a node (compared to the more global approach of Random Walks). These highly-represetative samples can then be used to develop workflows or generate representative embeddings. 

### [Breadth First Search](https://neo4j.com/docs/graph-data-science/current/algorithms/bfs/)
   - **Overview**: Breadth First Search (BFS) identifies a path between two provided nodes. The algorithm will traverses a graph in layers moving out from a source node, exploring all of the neighbor nodes at the present depth, and then moving to nodes at the next level until the target nodes is found. 
   - **Pros**: A primary use of BFS is to find the shortest path between nodes in an unweighted graph.
   - **Cons**: BFS can be computationally expensive on large graphs because the algorithm has to explore all nodes at a given level before moving on to the next level. 
   - **When to use**: Use BFS to find a shortest path between two nodes when the likelihood of reaching the target node will decrease as the algorithm searches further and further away from the source node. 

### [Depth First Search](https://neo4j.com/docs/graph-data-science/current/algorithms/dfs/)
   - **Overview**: Depth First Search (DFS) identifies a path between two given nodes. The algorithm does so by exploring as deep as possible along each branch before backtracking to explore another. This contrasts with the Breadth First Search approach which considers all nodes at a given level, starting with immediate neighbors, before moving to the next level.
   - **Pros**: DFS can use less memory than BFS and also be a more efficient choice if the target node is likely further away from the source node. DFS is also effective for identifying cycles (loops) in a graph.
   - **Cons**: DFS may not always return the shortest path between two nodes.
   - **When to use**: DFS is well suited to traverse a graph where the depth matters or to find cycles within the graph.

### [Yen’s Shortest Path](https://neo4j.com/docs/graph-data-science/current/algorithms/yens/)
   - **Overview**: Yen's algorithm finds the K-shortest paths from a source to a target node, where K is the number of shortest paths to compute. The algorithm is designed to work on weighted graphs, incorporating positive relationship weights when identifying the shortest paths.
   - **Pros**: Yen's Shortest Path will return multiple paths between two nodes and can incorporate relationship weights as part of the path discovery process. Additionally, the Neo4j implementation is parallelized, which can significantly improve performance on large graphs. 
   - **Cons**: Identifying multiple paths with weights makes Yen's more computationally expensive than algorithms that only find a single path.
   - **When to use**: Yen's Shortest path is a good choice when you need to identify multiple paths between two nodes and the weights of the relationships are important to the pathfinding process. For example, the algorith could be used for logistics planning between two locations where the distance represents the cost.

### [A* Shortest Path](https://neo4j.com/docs/graph-data-science/current/algorithms/astar/)
   - **Overview**: A* is an informed search algorithm. In addition to known distance calculations, the algorithm uses a heuristic function that provides an estimate of the cost from a node to the goal to guide its search. The Neo4j implementation of A* uses Dijkstra's Shortest Path combined with haversine distance (two points on a sphere). 
   - **Pros**: A* is often faster than Dijkstra's algorithms due to the heuristic. If properly implemented, the heuristic will also dramatically increase the liklihood of finding the shortest path between two points. 
   - **Cons**: If the heuristic is not properly implemented it can significantly degrade the quality of results. Relationship weights on the graph should represent distances, ideally in nautical miles though kilometers or miles can also work. 
   - **When to use**: A* is likley a good choice when calculating spatial distances between points and when the actual distances are represented on relationship weights. 

### [Dijkstra's Single-Source Shortest Path](https://neo4j.com/docs/graph-data-science/current/algorithms/delta-single-source/)
   - **Overview**: Dijkstra Single-Source Shortest Path (SSSP) finds the shortest path from a source node to all other reachable nodes in the graph.
   - **Pros**: Dijkstra's SSSP is a well-known, well-established pathfinding graph algorithm that will provide a comprehensive view of the shortest paths from a source node to all other nodes in the graph. With positive weights, it is also guaranteed to find the shortest paths between nodes. 
   - **Cons**: The algorithm requires non-negative weights and can be computationally expensive on large graphs.
   - **When to use**: Dijkstra's SSSP can be a good choice when you need to find the shortest path from a specific source to all other nodes, such as in network routing or logistics planning. 

### [Dijkstra's Source-Target Shortest Path](https://neo4j.com/docs/graph-data-science/current/algorithms/dijkstra-source-target/)
   - **Overview**: This version of Dijkstra's algorithm finds the shortest path between a single source node and a single target node. It will stop once it finds the shortest path between the two nodes. 
   - **Pros**: Dijkstra's Source-Target Shortest Path is a more efficient version of Dijkstra's SSSP because it can stop once it finds the shortest path. If all relationships have positive weights, it is also guaranteed to find the shortest path between the two nodes.
   - **Cons**: The algorithm can only use positive weights on edges. 
   - **When to use**: Use this when you need the shortest path between a specific pair of nodes and your graph does have negative weights.

### [Delta-Stepping Single-Source Shortest Path](https://neo4j.com/docs/graph-data-science/current/algorithms/delta-single-source/)
   - **Overview**: Delta-Stepping is a parallelized version of the SSSP algorithm. It has a distance correcting feature that enables it to traverse the graph in parallel. 
   - **Pros**: If multiple cores or processors are avaialble, Delta-Stepping will likely perform faster than the original SSSP algorithm.
   - **Cons**: Like SSSP, Delta-Stepping also requires non-negative weights. Additionally, if multiple shortest paths exist between two nodes, Delta-Stepping may return different paths on different runs. 
   - **When to use**: For similar use cases to Dijkstra's SSSP, Delta-Stepping can be a good choice when you have multiple cores or processors available and your graph has non-negative weights.


*This is an unofficial guide and does not include all algorithms in the GDS library. Consult the [official Neo4j Graph Data Science documentation](https://neo4j.com/docs/graph-data-science/current/) for the most up-to-date, authoritative content about the GDS library and individual algorithms.*




