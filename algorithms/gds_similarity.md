# GDS Node Similarity Algorithms
## Overview
The Neo4j Graph Data Science library contains two primary algorithms that identify how similar nodes are to each other. However, these two algorithms do so in significantly different ways:
* Node Similarity does so based upon graph structure
* K-Nearest Neighbors (KNN) does so based upon graph properties

Depending on the graph and requirements, both algorithms can be effective choices for calculating similarity. They can also effectively be used in combination to provide a more complete picture of similarity that includes both graph structure and node properties.

## Node Similarity Algorithms
### [Node Similarity](https://neo4j.com/docs/graph-data-science/current/algorithms/node-similarity/)
   - **Overview**: The Node Similarity algorithm calculates similarity based upon outgoing connections to common neighbors. Therefore, the more common neighbors two nodes have, the more similar they are. Similarity Scores are calculated using Jaccard or the Overlap Coefficient, and the output is new relationships weighte by the calculated similarity score. 
   - **Pros**: Node Similarity effectively captures the graph structure as a similarity score between nodes. This helps capture informaiton from the graph that may not be available from node properties or if the data was not in graph format. 
   - **Cons**: Because it compares all nodes to all other nodes, Node Similarity can be computationally expensive for large graphs. Identifying communities or filtering nodes can be effective ways to mitigate this. Additionally, Node Similarity also often requires pre-processing, such as converting a subgraph into a bi-partite graph. Because node similarity requires a bi-partite graph, it also only considers direct neighbors and not the entire graph structure. 
   - **When to use**: Node similarity is a highly effective way to quanitfy node similarities based upon graph structure. The resulting similarity relationships are frequently used as part of a larger workflow that may also incorporate community detection (to narrow the scope of similarity comparisons), centrality (to identify important node based on common similarity), and embeddings (to capture the similarity graph as a vector for further analysis). 

   *[Filtered Node Similarity](https://neo4j.com/docs/graph-data-science/current/algorithms/alpha/filtered-node-similarity/) is an extension of the Node Similarity algorithm that allows for filtering source nodes and target nodes. This can help speed up calculation and focus the similarity calculations.* 

### [K-Nearest neighbors](https://neo4j.com/docs/graph-data-science/current/algorithms/knn/)
   - **Overview**: K-Nearest Neighbors (KNN) calculates node similarity based upon node properties, rather than on relationships (existing relationships are ignored). Like the Node Similarity algorithm, KNN outputs new relationships between nodes, each of which is weighted by the calculated similarity score. 
   - **Pros**: KNN is simple and powerful in many contexts, particularly if nodes have meaningful properties.
   - **Cons**: KNN does not take into account the structure of the graph, only the node properties, though it can account for graph structure if captured in an embedding vector. KNN can be slow to calculate for large graphs or high dimensions. The algorithm also requires that node properties be numeric. 
   - **When to use**: Use KNN when you have meaningful node properties and are interested in finding nodes that are similar based on these properties. KNN can also be used as the final step in a similarity calculation workflow where Node Similarity and/or graph embeddings were used to capture graph structure and then saved as node properties. 

 *Note: The Neo4j GDS Library also supports a [Filtered K-Nearest Neighbors](https://neo4j.com/docs/graph-data-science/current/algorithms/alpha/filtered-knn/) that allows for filtering source nodes and target nodes to speed up computation and focus similarity comparisons.* 

 *This is an unofficial guide and does not include all algorithms in the GDS library. Consult the [official Neo4j Graph Data Science documentation](https://neo4j.com/docs/graph-data-science/current/) for the most up-to-date, authoritative content about the GDS library and individual algorithms.*
