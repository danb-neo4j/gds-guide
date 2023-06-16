# GDS Centrality Algorithms

## Overview
The [Neo4j Graph Data Science (GDS)](https://neo4j.com/docs/graph-data-science/current/) library contains several [centrality algorithms](https://neo4j.com/docs/graph-data-science/current/algorithms/centrality/) which calculate a node's importance based upon its relationships to other nodes in the graph. The different centrality algorithims accomplish this task in many different ways and for different use cases. This guide is designed to help you quickly undersand the differences between the centrality algorithms and when to use each one. 

*This is an unofficial guide and does not include all Centrality algorithms in the GDS library. Consult the [official Neo4j Graph Data Science documentation](https://neo4j.com/docs/graph-data-science/current/) for the most up-to-date, authoritative content about the GDS library and individual algorithms.*

## GDS Centrality Algorithms
**[Degree Centrality](https://neo4j.com/docs/graph-data-science/current/algorithms/degree-centrality/)**:
   - **Overview**: Degree Centrality calculates a node's importance by counting its relationships. By default, the Neo4j Degree Centrality algorithm counts outgoing nodes, but the [hyperparameters can be adjusted](https://neo4j.com/docs/graph-data-science/current/algorithms/degree-centrality/#algorithms-degree-centrality-orientation-example) to count incoming relationships or both. The GDS implementation also has hyperparameters for a [weighted degree centrality](https://neo4j.com/docs/graph-data-science/current/algorithms/degree-centrality/#algorithms-degree-centrality-weighted-example) calculation.
   - **Pros**: The algorithm is easy to compute and understand. It is also useful for understanding the immediate influence or risk of a node based upon the count of relationships.
   - **Cons**: Degree Centrality does not consider the importance of connected nodes or of a node's position in the overall network structure.
   - **When to use**: There are multiple situations where a raw count of relationships is beneficial. 
      * If the count of relationships is important to a question at hand, or the quality of the relationships is considered to be equivalent across the graph, Degree Centrality can easily provide counts for each node.
      * Analyzing relationship counts across the graph and for specific node or relationship types can be very useful when performing initial exploratory analysis on a graph. 

**[Page Rank](https://neo4j.com/docs/graph-data-science/current/algorithms/page-rank/)**: 
   - **Overview**: PageRank assigns a numerical weight to each node in a graph based on the quantity and quality (importance) of its relationships to other nodes. Importance can manifest as a large number of relationshisp to other nodes (that may or may not be important themselves) or a small number of relationthips to highly important nodes. The Neo4j GDS implementation of PageRank also has an option to incorporate [relationship weights](https://neo4j.com/docs/graph-data-science/current/algorithms/page-rank/#algorithms-page-rank-examples-weighted). You can also implement [Personalized Page Rank](https://neo4j.com/docs/graph-data-science/current/algorithms/page-rank/#algorithms-page-rank-examples-personalised) which is biased for source nodes and is often used with recommender systems. 
   - **Pros**: The algorithm considers the quality, not just quantity, of connections when calculating importance. As a variant of [Eigenvector Centrality](https://neo4j.com/docs/graph-data-science/current/algorithms/eigenvector-centrality/), PageRank also has a 'dampening factor' which represents the probability of 'jumping' to continue traversing the graph.
   - **Cons**: The algorithm can be sensitive to nodes with only incoming links. PageRank also assumes a homogenous graph. Although it will run on a heterogenous graph, the algorithm will not differentiate between nodes with different labels. 
   - **When to use**: 
      * PageRank is a good choice when you want to measure the relative importance of nodes in a graph. This can be especially effective when identifying the most important node of a specific subgraph, such as a community or cluster.
      * For example, PageRank can help identify the most important person in a social network, the most important page on a website, or the most important article in a corpus of documents.

**[Eigenvector Centrality](https://neo4j.com/docs/graph-data-science/current/algorithms/eigenvector-centrality/)**:
   - **Overview**: Eigenvector Centrality is a simpler version of PageRank that does not include the 'dampening' (jump) hyperparameter. Otherwise, the algorithms are effectively the same, both calculating the transitive importance of nodes based upon their connections to other important nodes. 
   - **Pros**: Eigenvector Centrality is another option to calculate them importance of a node based upon the quality, and not just the quantity, of its relationships.
   - **Cons**: Convergence issues can occur with cyclic graphs. Eigenvector may also favor nodes with high-degree neighbours.
   - **When to use**: When you need to identify influential nodes within a network, but may not want the dampening factor used by PageRank. Otherwise, PageRank is often the default choice for calculating transitive influence.

**[Betweenness Centrality](https://neo4j.com/docs/graph-data-science/current/algorithms/betweenness-centrality/)**:
   - **Overview**: Betweenness Centrality quantifies a nodes importance based upon how it acts as a bridge along the shortest path between two other nodes. A node's betweenness score represents how many times it is on the shortest path between two other nodes in the graph.
   - **Pros**: This algorithm is useful for finding nodes that serve as bridges in a network. 
   - **Cons**: Betweenness Centrality can be computationally expensive for large networks. 
   - **When to use**: Betweenness Centrality is helpful when you need to identify nodes that control information flow in a network, or if you are trying to identify vulnerabilities in a graph. 
      * For example, a router in a network might have lower degree centrality or PageRank scores based upon its connections, but at the same time might have a very high betweenness centrality score because it sits on the only path for information to travel into or out of a network.
      * A transit hub may serve a similar role in a network and have few connections of its own. However, when considered in the context of the entire network it may control transit into or out of a portion of the network resulting in a high betweenness centrality score. 

**[Article Rank](https://neo4j.com/docs/graph-data-science/current/algorithms/article-rank/)**:
   - **Overview**: ArticleRank is a variant of PageRank that resuces the score of nodes with large numbers of outbound links. This addresses the 'spamming' problem in PageRank where a page could artificially increase its rank by adding more links.
   - **Pros**: In situations where nodes may attempt to artificially increase their rank by adding more links, ArticleRank can help reduce the impact of those links.
   - **Cons**: ArticleRank could under-value nodes that legitimately have many outbound links.
   - **When to use**: Use ArticleRank in situations where you want to avoid nodes spamming their way to 'higher ranks'. This could include websites, social media accounts, or publications with excessive citations. 

**[Influence Maximization (i.e., Cost-Effective Lazy Forward Selection or CELF)](https://neo4j.com/docs/graph-data-science/current/algorithms/celf/)**:
   - **Overview**: CELF is a greedy algorithm used to identify influence maximization within a network. It does so by identifying 'influencers' in a network that would maximize the spread of information, a disease, or other material.
   - **Pros**: CELF's lazy-forward optimization makes it more efficient than a traditional greedy algorithmic approach.
   - **Cons**: Despite optimizaitons, CELF can still be computationally expensive to calculate on large graphs.
   - **When to use**: CELF is most helpful when you are attempting to identify a subset of nodes that will maximize the 'spread' in a network. Traditional use cases include disease detection, marketing campaigns, and IT network security. 

