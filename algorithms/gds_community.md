# Community Detection Algorithms

## Overview
Community Detection Algorithms identify tightly-connected groups of nodes within the overall network. Put anotherway, community detection algorithms identify natural clusters or communities within the graph. As with other families of GDS algoritims, the community detection algorithms approach this task in different ways and for different use cases. 

*This is an unofficial guide and does not include all algorithms in the GDS library. Consult the [official Neo4j Graph Data Science documentation](https://neo4j.com/docs/graph-data-science/current/) for the most up-to-date, authoritative content about the GDS library and individual algorithms.*

## GDS Community Detection Algorithms

**[Weakly Connected Components (WCC)](https://neo4j.com/docs/graph-data-science/current/algorithms/wcc/)**:
   - **Overview**: WCC identifies clusters in a graph where each node can reach each other node if relationship direction is ignored. Components are considered 'weakly connected' because nodes can reach each other if direction is ignored.
   - **Pros**: WCC is effective in understanding the broad structure of a graph and for identifying isolated communities within a network. It is also a fast algorithm, making it a good choice for large graphs.
   - **Cons**: WCC does not consider the direction of relationships, in contrast to the [Strongly Connected Components algorithm](https://neo4j.com/docs/graph-data-science/current/algorithms/strongly-connected-components/).
   - **When to use**: 
        * WCC is often a good first choice when attempting to undestand a graph's structure. At the start of an analysis, it can help identify natural groupings and clusterings. For example, a graph may have one large cluster and many very small clusters of one or two nodes. Or a graph may comprise many clusters that all have the same number of nodes. WCC can help identify these patterns quickly, even across large graphs. 
        * WCC clusters are often analyzed indiviually. For example, in a large graph WCC components may identify natural groupings of patients among health care data. These clusters can then be analyzed individually to understand the characteristics of each cluster. Having smaller clusters may also help when applying algorithms that would be too computationally expensive to run across the entire graph. 

**[Louvain](https://neo4j.com/docs/graph-data-science/current/algorithms/louvain/)**:
   - **Overview**: Louvain is a hierarchical community detection algorithm that optimizes the modularity (connection density) of a graph, seeking to maximize the density of edges inside communities and minimize the density edges between communities. 
   - **Pros**: Louvain provides a hierarchical approach to identifying more densely connected communities within a graph. The algorithm also scales well to large graphs.
   - **Cons**: The algorithm might yield different results if the order of the nodes is changed. Also, the algorithm may not perform well when the network has many smaller communities. This issue is somewhat resolved with the Leiden algorithm, below. 
   - **When to use**: Use Louvain when you need to identify communities in large-scale networks and are interested in understanding their hierarchical structure.

**[Leiden](https://neo4j.com/docs/graph-data-science/current/algorithms/leiden/)**:
   - **Overview**: The Leiden algorithm performs the same hierarchical community detection as the Louvain algorithm, but with improvements that address Louvain's challenges with detecting small communities. It does so by incorporating a 'refinement' phase into the algorithm, improving how it can detect smaller communities. 
   - **Pros**: Same as Louvain, but with improvements for detecting smaller communities.
   - **Cons**: While generally efficient, Leiden can be more computationally expensive than other community detection approaches. Its refinement over Louvain does require slightly more computation as well. The algorithm can also struggle with overlapping communities, similar to Louvain. 
   - **When to use**: Leiden is a likely the optimal choice when you want to use a hierarichal approach to community detection and when a slightly higher computatioal cost is an acceptable tradeoff. 

**[Label Propagation](https://neo4j.com/docs/graph-data-science/current/algorithms/label-propagation/)**:
   - **Overview**: The Label Propagation (LPA) algorithm assigns a unique label to every node and iteratively updates the labels based on the neighbors' labels until a consensus is reached.
   - **Pros**: Label Propagation is a fast community detection algorithm and scales well to larger graphs. The labels can also be pre-assigned, allowing Label Propagation to operate in a semi-supervised manner. 
   - **Cons**: This algorithm can produce fluctuating results due to its inherent randomness, though the GDS implementation does allow for seeding. Label Propagation can also struggle with identifying small communities in large networks.
   - **When to use**: Label Propagation is a good choice when you need a simple, fast, and highly-scalable algorithm for community detection. Because the algorithm only uses the graph structure and does not require any pre-defined information about the graph itself, it is a good choice if you do not know much about the graph you are analyzing. LPA also performs well when all nodes are connected as part of a single, large graph such as the output of a Node Similarity algorithm. 

**[K-Means Clustering](https://neo4j.com/docs/graph-data-science/current/algorithms/kmeans/)**:
   - **Overview**: The Neo4j GDS implmentation of K-Means Clustering is a property-based clustering algorithm. Therefore, it ignores relationships and uses numeric node properties. The algorithm requires that you pre-specifying the number of clusters (k), then assigns each node to the nearest centroid. It then iterates over the Euclidean distance updating the centroid until the cluster assignments stabilize.
   - **Pros**: K-Means is a well-established clustering approach and provides a property-based clustering option within the GDS library.
   - **Cons**: K-Means requires pre-defining the number of clusters, which can have a significant impact on the final cluster assignments. It also requires that the nodes all have the same properties and that these properties are numeric values. Calculating the silhouette score, as part of evaluating cluster quality, can also be computationally expensive. 
   - **When to use**: Use K-means when you want to cluster based upon node properties rather than relationships. This can help capture properties and provide a comparison to relationship-based methods. It can be an effective approach when you have captured graph structure as embeddings on node properties and want to use this information for clustering.
