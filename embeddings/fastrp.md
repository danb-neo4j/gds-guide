# Fast Random Projection (FastRP)

## Summary
- [Fast Random Projection (FastRP)](https://neo4j.com/docs/graph-data-science/current/machine-learning/node-embeddings/fastrp/) is a graph embedding algorithm that uses random projection for dimensionality reduction, preserving distance information between nodes. 
- In the Neo4j Graph Data Science (GDS) library's implementation, FastRP supports weighted, directed, and undirected graphs. The was designed for undirected graphs and it generally performs best on this kind of structure.
- The Neo4j GDS extends FastRP by incorporating node properties, making the generated embeddings more comprehensive in their representations of the graph. 
- FastRP is random and inductive, necessitating similar distribution of embeddings during training and prediction for effective outcomes. 
- To optimize FastRP embeddings, *hyperparameter tuning is crucial*, with key parameters including the embedding dimension, iteration weights, normalization strength, and node self-influence.

## Pros and Cons
### Pros
* **Node Properties and Relationship Weights**: Neo4j's implementation of FastRP can incorporate node properties. The implementation can also capture weighted relationships, further enhancing the inputs to the embeddings. 
* **Efficiency**: FastRP is an efficient algorithm and performs well on larger graphs.
* **Scalability**: FastRP also scales well to high-dimensional data and large graph sizes.

### Cons
* **Tuning Required**: Optimal performance with FastRP often requires careful tuning of its hyperparameters. This can be a time-consuming process that requires an understanding of the individual hyperparameters.
* **Recommendation for Undirected Graphs**: Although FastRP in Neo4j GDS supports directed graphs, the original algorithm was primarily evaluated on undirected graphs. Consequently, its performance might not be as robust on directed graphs, potentially limiting its applicability in certain scenarios compared to other algorithms like Node2Vec or GraphSAGE which are well suited for directed graphs.
* **Randomness**: Due to the use of random projections, the algorithm may generate different embeddings for the same graph across different runs unless a random seed is set, which is an option in the Neo4j GDS implementation. 

## Use Cases
- **Large-Scale Graph Embedding**: FastRP's key strength lies in its scalability, making it a strong choice for working with large graphs where other algorithms might struggle due to computational or memory constraints. If your project involves very large graph datasets, FastRP is likely to be an efficient choice compared to Node2Vec, GraphSAGE, or HashGNN.
- **Machine Learning Tasks**: If your use case involves downstream machine learning tasks such as link prediction or node property prediction, FastRP can be useful. The embeddings it generates can feed into machine learning models, and its support for node properties and weighted relationships can add an extra level of granularity. 

## FastRP Hyperparameter Configuration
Given the impact on embedding quality, Neo4j's FastRP documentation provides [an explanation and guidance about the parameters](https://neo4j.com/docs/graph-data-science/current/machine-learning/node-embeddings/fastrp/#algorithms-embeddings-fastrp-parameter-tuning) that can be tuned for the algorithm. Details and configuration guidelines for these parameters are provided below:

### Iteration Weights
#### Description:
* The `iterationWeights` parameter comprises a list of numbers that have a dual function: 
	* The length of the list dictates the number of iterations the algorithm performs.
	* Each number in the list acts as a weight that determines how much the embeddings from that particular iteration contribute to the final node embedding.
* The zero index of `iterationWeights` corresponds to the initial iteration, where the algorithm assigns random vectors to each node. This is not really an "iteration" in the same sense as the others, but it is included for completeness. 
* The position of a weight in the list corresponds to the degree of neighbors that the algorithm considers during that iteration. For example:  
	* A weight at position 1 corresponds to the first iteration, where only direct neighbors are considered.
	* A weight at position 2 corresponds to the second iteration where second-degree neighbors are considered.

#### Configuration Guidance:
* The default `iterationWeights` are set to [0.0, 1.0, 1.0]. This is generally a good starting point fastRP because:
	* The zero-index, representing the initial random weights, is set to zero because the initial iteration vectors are random and may not add much value to the final embeddings.  
	* The first actual iteration of the algorithm (corresponding to the index 1), where the embeddings are calculated based on direct neighbors of each node, contributes fully to the final embeddings.
	* The second iteration of the algorithm (corresponding to the index 2), where the embeddings are calculated based on second-degree neighbors (including potential reach-back paths), also contributes fully to the final embeddings.
* It is recommended to have at least one non-zero weight at an even position and at least one non-zero weight at an odd position in the list. 
	* Even positions in the list (2nd position, 4th position, etc) correspond to iterations where nodes can potentially "reach back" to themselves via even-length paths. Having a non-zero weight at an even position allows the final node embeddings to incorporate information from these even-length paths. 
	* A non-zero weight at an odd position allows the final node embeddings to incorporate information from odd-length paths, which correspond to further away, indirect neighbors.
	* With both an even and odd weight, the final node embeddings should incorporate information from both direct and indirect neighbors, as well as potentially from the nodes themselves via even-length paths. 
* If nodes further from the source node can contribute meaningful information to the final embedding, consider expanding the `iterationWeight` vector by one or two values. Also consider setting these additional, further weights to lower weighting such as 0.5 or 0.75 so they include information to the final embeddings, but not as much as the closer nodes. 

### Embedding Dimension
#### Description:
* The `embeddingDimension` parameter in the FastRP algorithm determines the size of the final embedding vectors. This is a crucial parameter as it affects both the precision of the embeddings and the computational resources needed to generate and work with the embeddings.
* There is no default value for embedding dimension, but typical values are a power of two in the range 128 to 1024.

#### Configuration Guidance:
* Dimensions of 128 or 256 are often a good starting point. 
* For smaller projections (under 100,000 nodes) or projections without properties, consider starting at 128.
* A value of at least 256 gives good results on graphs (graph projections) in the order of 10^5 nodes. Also consider this or larger embeddings if you are including node properties. 
* For very large graphs or graphs with many node properties, an even larger embedding such as 512 might be warranted. This will, however, increase compute and storage requirements. 

### Node Self Influence
#### Description:
* `nodeSelfInfluence` is an optional hyperparameter that controls how much its initial random vector contributes to its final embedding. 
* It effectively behaves the the same as the zero-index position of `iterationWeights`.

#### Configuration Guidance:
* If your graph has low connectivity or a significant number of isolated nodes, setting a non-zero nodeSelfInfluence value might be beneficial.   
    * This is because for isolated nodes, particularly when propertyRatio is 0.0, the embeddings might result in all zeros. 
    * By contrast, using node properties along with node self influence can produce more meaningful embeddings for low-connection or isolated nodes. 
    * A higher starting value, such as 0.5, could be a reasonable starting point for generating embeddings. 
* An alternative use is for dimensionality reduction, where nodeSelfInfluence can be used for pure dimensionality reduction to compress node properties used for node classification. In this case a very high nodeSelfInfluence value, such as 0.8 or 0.9, would be applicable. 

### Normalization Strength
#### Description:
* `normalizationStrength`is used to control how node degrees influence the embedding. 
* Using a negative value between 0 and -1 will downplay the importance of high degree neighbors, while a positive value between 0 and 1 will instead increase their importance.

#### Configuration Guidance:
* If the graph has highly connected 'supernodes', these nodes could dominate the embeddings due to the number of their connections. In this case, a negative `normalizationStrength` parameter could be appropriate to reduce the influence of these highly connected nodes on the final embedding output. 
* If the graph has nodes sparse connections, and it is important to emphasize the influence of these connections in the embeddings,  a positive `normalizationStrength` could be appropriate.

### featureProperties and propertyRatio
#### Descriptions:
* `featureProperties` is a list that names of the node properties that should be used as input features. All property names must exist in the projected graph and be of type Float or List of Float.
* `propertyRatio` is  desired ratio of the property embedding dimension to the total `embeddingDimension`. A positive value requires featureProperties to be non-empty.

#### Configuration Guidance:
* Assuming that properties will be included in the embeddings, the starting point for `propertyRatio` depends on the specific properties of the nodes in your graph and how much you believe those properties should contribute to the final embeddings. 
* A balanced starting point would be 0.5, which would assign half of the embedding dimension to represent node properties and the other half to represent the graph structure. A higher value would emphasize the properties over the graph structure while a lower value would emphasize the graph structure over the properties.
* If the properties are numerical representations of categorical values (i.e., one-hot encoded as a vector or individual properties) and there is a large number of properties, a higher `propertyRatio` may be necessary to capture the diversity of values. Conversely, if the properties are purely numeric (i.e., not representing categorical features), then a lower `propertyRatio` may be sufficient.


## Additional Resources
* Original FastRP Paper: [Fast and Accurate Network Embeddings via Very Sparse Random Projection](https://arxiv.org/pdf/1908.11512.pdf)
* Video: [FastRP Explained by Example](https://youtu.be/uYvniQlSvyQ)

*This is an unofficial guide, so as always be sure to consult the [official Neo4j Graph Data Science documentation](https://neo4j.com/docs/graph-data-science/current/) for the most up-to-date, authoritative content about the GDS library and individual algorithms.*
