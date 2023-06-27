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

## Additional Resources
* Original FastRP Paper: [Fast and Accurate Network Embeddings via Very Sparse Random Projection](https://arxiv.org/pdf/1908.11512.pdf)
* Video: [FastRP Explained by Example](https://youtu.be/uYvniQlSvyQ)

*This is an unofficial guide, so as always be sure to consult the [official Neo4j Graph Data Science documentation](https://neo4j.com/docs/graph-data-science/current/) for the most up-to-date, authoritative content about the GDS library and individual algorithms.*
