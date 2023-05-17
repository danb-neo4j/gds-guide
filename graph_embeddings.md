# Graph Embeddings

## Graph Data Science Embeddings Algorithms
As of May 2023 Neo4j Graph Data Science currently supports four embedding algorithms:
* FastRP
* GraphSAGE
* Node2Vec
* HashGNN

Documentation summaries and usage considerations are provided below to help data scientists understand the pros and cons of each algorithm and make an informed choice when selecting one for a project. As always, consult the [official Neo4j Graph Data Science documentation](https://neo4j.com/docs/graph-data-science/current/machine-learning/node-embeddings/) for the most up-to-date and version-specific guidance for these and all algorithms. 

### Fast Random Projection (FastRP)
#### Documentation Summary
[Link to Documentation](https://neo4j.com/docs/graph-data-science/current/machine-learning/node-embeddings/fastrp/)
Fast Random Projection (FastRP) is a graph embedding algorithm that leverages random projection techniques to perform dimensionality reduction while preserving most of the distance information between nodes. It operates on graphs by assigning initial random vectors to nodes using a technique called very sparse random projection. The algorithm then iterates, averaging over node neighborhoods to construct intermediate embeddings, which are combined to produce the final node embeddings. This approach exploits higher-order relationships in the graph while maintaining scalability.

The FastRP implementation in the Neo4j Graph Data Science (GDS) library supports weighted graphs and directed or undirected graphs, although the original algorithm designed for undirected graphs. To use weighted graphs, set the relationshipWeightProperty parameter to an existing relationship property. For directed graphs, only outgoing neighbors are considered when computing the intermediate embeddings, but it is recommended to start with undirected graphs, as this is what the original algorithm was evaluated on.

FastRP in the GDS library extends the original algorithm to take node properties into account, making the resulting embeddings more accurate representations of the graph. This is configured via the featureProperties and propertyRatio parameters. Embeddings generated with FastRP can be used in machine learning pipelines for tasks like link prediction or node property prediction. However, be mindful that the algorithm is random and inductive only, so ensure embeddings during training and prediction are of a similar distribution for the model to make useful predictions.

Hyperparameter tuning is important to optimize FastRP embeddings for your specific use case and graph. Key parameters include: embedding dimension, normalization strength, iteration weights, node self influence, and orientation. To find the optimal parameters, perform hyperparameter tuning using a downstream machine learning task and a test set excluded from the parameter tuning process.

#### Use Cases
FastRP can be applied to a variety of data science tasks, including:
1. **Node classification**: The embeddings can be used to train a machine learning model that classifies nodes based on their features.
2. **Link prediction**: By considering the similarity between the embeddings of two nodes, one can predict the likelihood of a link existing between them.
3. **Community detection**: Nodes that are embedded closely together in the reduced space are likely to be part of the same community.
4. **Graph visualization**: Reduced dimensional embeddings can be used to visualize the graph in 2D or 3D space.
5. **Graph comparison**: Embeddings can be used to measure similarity between different graphs.

#### Factors to Consider 
When considering whether to use FastRP for a particular project or graph, several factors should be evaluated:
1. **Graph size**: FastRP is designed to be efficient and scalable, making it suitable for large graphs. If the graph in question is quite large, FastRP can be a good choice.
2. **Node attribute data***: FastRP can include node features in the embedding process, making it a good choice if you have rich node attribute data that you want to incorporate into your analysis.
3. **Graph structure**: FastRP is well-suited to graphs that have a meaningful structural or topological component, as it uses personalized PageRank, which considers the structure of the graph in generating embeddings.
4. **Downstream task**: FastRP can be used in many machine learning tasks, including node classification, link prediction, and community detection. If your project involves these tasks, FastRP might be a suitable choice.
5. **Speed and efficiency requirements**: FastRP is designed to be computationally efficient, making it a good choice if you need to generate embeddings quickly or if you're working with limited computational resources.
6. **Dimensionality**: FastRP allows you to specify the dimensionality of the embedding space. If you need control over this aspect, FastRP could be a suitable choice.
7. **Interpretability**: FastRP, like many embedding techniques, may not provide easily interpretable embeddings. If interpretability is a crucial factor for your project, you might want to consider other methods.

#### Pros and Cons
**Pros**
1. **Efficiency**: FastRP is called "Fast" for a reason. It can generate embeddings in a time-efficient manner, even for larger graphs.
2. **Scalability**: This algorithm can handle high-dimensional data and large graph sizes.
3. **Versatility**: The generated embeddings can be used in various downstream tasks such as the ones mentioned above.

**Cons**
1. **Loss of Information**: As with any dimensionality reduction technique, there is a potential loss of information. Some details and nuances may be lost during the projection.
2. **Hyperparameter tuning**: The dimensions of the target space are a hyperparameter that needs to be tuned, and the optimal value may vary depending on the specific task and data.
3. **Randomness**: Because it's a stochastic algorithm, the output may vary between runs. This can make results less reproducible. However, in Neo4j's implementation there is the option to set a random seed. 


### GraphSAGE
#### Documentation Summary
[Link to Documentation](https://neo4j.com/docs/graph-data-science/current/machine-learning/node-embeddings/fastrp/)

GraphSAGE is an inductive algorithm implemented in Neo4j used for computing node embeddings in graph databases, which allows it to generate embeddings for unseen nodes or graphs based on the node's local neighborhood features. This method differs from traditional approaches that train individual embeddings for each node. However, this algorithm is only defined for undirected graphs. It's important to note that for isolated nodes or nodes with all properties as zero, GraphSAGE may result in all-zero vectors, which could impact downstream tasks such as nearest neighbor or other similarity algorithms.

In terms of usage, GraphSAGE can be utilized in machine learning pipelines as a node property step, but the model must be trained outside the pipeline first. The model can then be incorporated into a pipeline using certain commands. Moreover, due to the potential time and memory consumption when training GraphSAGE on large graphs, it might be beneficial to sample a smaller, structurally representative subgraph for training. The trained model can still be applied to predict embeddings on the full graph.

Tuning GraphSAGE involves adjusting several parameters dependent on the specific dataset. These parameters include the embedding dimension, which balances information capture against memory and computational requirements, and the aggregator, which dictates how a node's embedding and the sampled neighbor embeddings from the previous layer are combined. Other important parameters include the activation function, sample sizes, batch size, epochs, max iterations, batch sampling ratio, search depth, negative-sample weight, penalty L2, learning rate, tolerance, and projected feature dimension.

#### Use Cases
GraphSAGE is primarily used for representation learning on large graphs, particularly in the context of graphs that are constantly evolving and expanding. The following are among the most common use cases for the GraphSAGE algorithm:
1. Node Classification: GraphSAGE is often used to assign labels to nodes based on their role in the overall graph structure and their relationships with other nodes. This can be especially useful in social networks, citation networks, and other similar applications.
2. Link Prediction: By learning representations of node pairs, GraphSAGE can predict the likelihood of a relationship between two nodes. This is useful in recommendation systems, predicting social connections, or predicting potential links in a citation network.
3. Graph Classification: GraphSAGE can also be adapted to generate an embedding of an entire graph, which can be used for graph classification tasks.

#### Factors to Consider
Several factors should be considered when evaluating GraphSAGE for your project including:
1. Inductive vs. Transductive: GraphSAGE is an inductive method, meaning it's designed to generate embeddings for unseen nodes based on their features and their relation to other nodes. If you expect your graph to evolve over time (new nodes and edges), GraphSAGE might be an excellent choice.
2. Node feature information: GraphSAGE makes use of node attribute information to generate embeddings. If your nodes have rich feature information that you'd like to leverage, GraphSAGE can be a good option.
3. Scalability: GraphSAGE uses a neighborhood sampling strategy to scale to large graphs. If you have a very large graph, GraphSAGE might be suitable.
4. Downstream tasks: GraphSAGE can be used for various downstream tasks like node classification, link prediction, and community detection. Consider your project goals and whether GraphSAGE aligns with these.
5. Computational resources: GraphSAGE involves sampling and training on a certain number of "neighborhoods" in each batch, which can be computationally expensive. Consider your available resources and the efficiency of the algorithm.
6. Graph structure: GraphSAGE works well on graphs where the local neighborhood of a node (its immediate connections) can provide useful information about that node. If your graph's structure is such that a node's local neighborhood is informative, GraphSAGE might be a good choice.
7. Flexibility: GraphSAGE provides flexibility in terms of the aggregation function used (mean, LSTM, pooling), which gives the user some control over the algorithm's behavior.

#### Pros and Cons
The pros and cons of using GraphSAGE include:

**Pros**:
1. Scalability: GraphSAGE is designed to be scalable and can handle very large graphs efficiently.
2. Inductive Learning: Unlike many graph algorithms that are transductive and can only make predictions for seen nodes during training, GraphSAGE can generate embeddings for unseen nodes or entirely new graphs.
3. Incorporation of Node Features: GraphSAGE utilizes not only the graph structure but also node feature information when generating embeddings.

**Cons**:
1. Isolated Nodes: GraphSAGE may generate less meaningful embeddings for isolated nodes or nodes with all properties as zero. These embeddings may not be useful for downstream tasks.
2. Undirected Graphs Only: GraphSAGE is only defined for undirected graphs, which limits its applicability.
3. Computationally Intensive: Training the GraphSAGE model can be computationally intensive, especially for large graphs and complex aggregators.

### Node2Vec
#### Documentation Summary
[Link to Documentation]

Node2Vec is an algorithm used to create vector representations, or embeddings, of nodes within a graph. These embeddings are calculated based on random walks in the graph, essentially simulating random traversal of the graph. The algorithm leverages a single hidden layer neural network, which is trained to predict the likelihood of a node occurring in a walk based on the presence of another node.

The Node2Vec algorithm uses second order random walks, where the transition probability is determined by the currently visited node, the previously visited node, and the target node of a candidate relationship. The random walks are influenced by two parameters: the returnFactor (used when the walk returns to the previously visited node), and the inOutFactor (used when the walk traverses further away from the node). The traversal probabilities can also be adjusted using a relationshipWeightProperty, and the number and length of random walks per node can be controlled with the walkPerNode and walkLength parameters.

Using Node2Vec in a machine learning pipeline for tasks such as link prediction and node property prediction is currently not well-supported, primarily due to the non-deterministic nature of the algorithm. Because Node2Vec relies on the randomness of generating initial node embedding vectors and the random walks, it produces non-deterministic results, even when a randomSeed parameter is set. This makes the embeddings vary between runs, which can be problematic for machine learning models that require feature consistency between training and prediction phases.

Despite this limitation, Node2Vec embeddings can still be valuable if they are generated outside the machine learning pipeline and then used as features within it. However, caution should be taken due to the potential data leakage risks of not using the dataset split in the pipeline. It's important to note that these considerations are currently specific to Neo4j's implementation of Node2Vec, and may not apply to other implementations of the algorithm.

#### Factors to Consider
When deciding whether to use Node2Vec versus an alternative algorithm, the following factors should be considered:
* **Graph Type**: Node2Vec can handle various types of graphs, but some algorithms might be better suited to specific types of graphs (e.g., directed, weighted).
* **Task**: Node2Vec is great for tasks like link prediction, community detection, and recommendation systems. However, for tasks such as graph classification, other methods (like Graph Convolutional Networks) might be more suitable.
* **Data Size**: Node2Vec can be computationally expensive on larger graphs. If computational resources are limited, other, more efficient algorithms might be preferable.
* **Necessity for Deterministic Results**: If the task requires deterministic results, Node2Vec might not be the best choice due to its non-deterministic nature. Algorithms such as GraphSAGE or Graph Convolutional Networks, which provide deterministic results, might be better suited.

#### Use Cases
Some of the most common use cases for Node2Vec include:
1. **Link Prediction**: Node2Vec can be used to predict potential relationships between nodes by calculating the similarity of the node embeddings.
2. **Community Detection**: Node2Vec can help identify communities or clusters within a graph as nodes in the same community will have similar vector representations.
3. **Recommendation Systems**: Given the similarity measures derived from the embeddings, Node2Vec can be used to build recommendation systems, for instance, suggesting similar items, or friends in a social network.
4. **Node Classification**: Node2Vec can be used in semi-supervised settings where labels for some nodes are known, and the task is to predict labels for unlabeled nodes.

#### Pros and Cons
The pros of using Node2Vec include:
* **Flexibility**: Node2Vec can be used on different types of graphs (directed, undirected, weighted, unweighted).
* **Preserving Network Neighborhoods**: Node2Vec effectively captures both local and global structural information, making it good at preserving network neighborhoods.
* **Unsupervised Learning**: Node2Vec is an unsupervised algorithm, making it useful when labeled data is scarce.

However, Node2Vec also has some limitations:
* **Non-Deterministic Outputs**: As discussed earlier, Node2Vec can produce non-deterministic results due to the randomness involved in initializing node embedding vectors and the random walks, which can be problematic in certain scenarios.
* **Computational Requirements**: Node2Vec can be computationally expensive on larger graphs due to the need to generate and process random walks.
* **Sparse High-Dimensional Vectors**: The embeddings generated by Node2Vec can be high-dimensional and sparse, which may not be ideal for all applications.


### HashGNN
#### Documentation Summary
[Link to Documentation]

HashGNN is a node embedding algorithm, similar to Graph Neural Networks (GNN), but unlike GNNs, it doesn't require a model or training. It replaces the neural networks of GNNs with random hash functions, using the concept of min-hash locality sensitive hashing. Neo4j's Graph Data Science (GDS) implementation of HashGNN extends the original algorithm to support heterogeneous graphs and allows configuration of how much embeddings are updated using features from neighboring nodes versus features from the same node via the neighborInfluence parameter. The runtime of HashGNN is significantly lower than that of GNNs, and it doesn't require GPUs for execution. The implementation also performs well across many CPU cores.

The core of the HashGNN algorithm involves iterative computation of new binary embeddings for each node, using the embeddings from the previous iteration. The algorithm works only on binary features, necessitating an optional first step to transform non-binary input features into binary features. During each iteration, the new embedding vector for a node is constructed by taking random samples, considering both the features of the node itself and its neighbors. The number of samples taken is determined by the parameter 'embeddingDensity' in the algorithm's configuration.

The HashGNN algorithm assumes binary features as input and produces binary embeddings as output. To make the algorithm compatible with real-world graphs, Neo4j's implementation provides options to binarize node properties or generate binary features from scratch. The algorithm also allows you to control the influence of neighbor features over node features using the 'neighborInfluence' parameter. Moreover, it supports heterogeneous graphs, allowing it to distinguish between different relationship types, which is a useful feature when dealing with complex, multi-relational graphs. 

HashGNN can be an important step in machine learning pipelines, such as link prediction and node property prediction. However, it's crucial to ensure that the features produced during prediction are similar in distribution to the features produced during training. HashGNN's random and inductive nature necessitates the use of a consistent randomSeed configuration parameter for training and prediction. Moreover, the GDS implementation of HashGNN provides several tunable parameters to improve embedding quality, such as iterations, embeddingDensity, feature generation, feature binarization, neighbor influence, heterogeneous mode, and random seed. These parameters require careful tuning based on your specific use case and graph characteristics.

#### Usage Considerations
When deciding whether to use HashGNN versus an alternative algorithm, a data scientist should consider the following factors:

1. Graph complexity: If the graph is heterogeneous (i.e., it has multiple types of nodes and relationships), HashGNN is one of the few graph algorithms that can handle this complexity.
2. Feature type: If the graph has non-binary features, the data scientist would need to consider the impact of binarizing these features on the overall quality of the embeddings and the downstream task.
3. Runtime considerations: If computational efficiency and lower runtime are a priority, HashGNN would be a better choice due to its low runtime and parallelizable nature.
4. Hardware constraints: If the available hardware lacks a high-performance GPU, but has multiple CPU cores, then HashGNN would be advantageous as it can execute and parallelize well on CPUs.
5. Consistency: If the need for consistent embeddings across different runs is important, the data scientist would need to consider the impact of the random nature of HashGNN. Other deterministic graph embedding methods could be considered in this case.

#### Use Cases
As a graph data scientist, the primary use cases for HashGNN or any node embedding algorithm are often related to tasks that require learning and leveraging the structural information of a graph. This includes but is not limited to:

1. Link prediction: HashGNN can be used to create node embeddings which can then be used to predict whether a link should exist between two nodes. This can be beneficial in social network analysis, bioinformatics, or recommendation systems where predicting relationships or connections is crucial.
2. Node classification: Node embeddings generated by HashGNN can also be used in node classification tasks where each node in the graph needs to be assigned a label or class. This is particularly useful in scenarios like community detection, fraud detection, or any scenario where you need to categorize nodes based on their connectivity and feature patterns.
3. Graph visualization: Embeddings can be used to visualize high-dimensional graph data in a lower-dimensional space, which can be useful for exploratory data analysis.

#### Pros and Cons
Regarding the pros and cons of using HashGNN:

Pros:

1. Efficiency: HashGNN tends to have a lower runtime than traditional GNNs and does not require GPU for execution. This makes it an efficient option for graph embedding.
2. No model training required: Unlike other GNNs, HashGNN replaces the neural networks with random hash functions, thereby eliminating the need for model training.
3. Parallelizable: HashGNN can parallelize well across many CPU cores, making it scalable for larger graphs.
4. Heterogeneous graph support: HashGNN supports heterogeneous graphs where relationships of different types are associated with different hash functions. This preserves the relationship-typed graph topology.

Cons:

1. Binary features only: HashGNN can only run on binary features, so a transformation step may be required if your graph has non-binary features.
2. Randomness: Due to the use of random hash functions, the embeddings may not be consistent across different runs unless a specific random seed is provided.

## Comparing and Selecting an Embedding Algorithm
The following section provides a conceptual approach and recommendations for selecting any of the four graph embedding algorithms currently supported in the Neo4j Graph Data Science library.


