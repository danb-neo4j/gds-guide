# HashGNN
## Summary
* HashGNN is a node embedding algorithm that combines ideas of GNNs and fast randomized algorithms, producing embeddings that do not require model training. 
* The Neo4j Graph Data Science implementation of HashGNN includes support for heterogenous graphs, setting this algorithm apart from others in the library. 
* The HashGNN algorithm can only run on binary features, requiring any non-binary features to be binarized. The GDS implementation includes features to assist with this process.
* HashGNN requires significantly less time than other GNNs and in published research has shown comparable results. The Neo4j implementation parallelizes well across CPU cores. 

## Pros and Cons
### Pros
* **Heterogenous Graphs**: HashGNN supports heterogenous graphs, providing the ability to capture embeddings where multiple node and relationship types exist.
* **Efficiency and Scalability**: The HashGNN algorithm does not require a model or training, and it does not require GPUs for execution which can make it more cost-effective and accessible across large graphs. 
* **Customizability**: The Neo4j implementation of HashGNN  allows the user to control the influence of neighbor features over node features via the 'neighborInfluence' parameter. This makes the algorithm adaptable to various types of graphs and use cases.

### Cons
* **Limited to Binary Features**: HashGNN works only on binary features, necessitating a first step to transform non-binary input features into binary ones. The Neo4j implementation, however, does provide features to assist with this. However, it still may result in some information loss. 
* **Dependency on Randomness**: The use of random hash functions introduces an element of randomness into the process. This requires the use of a consistent random seed, which plays a unique role in tuning the algorithm.

## Use Cases
* **Node Prediction**: Given the algorithm's support for heterogenous nodes, HashGNN is well suited for making predictions in a heterogenous graph. It is also demonstrated to perform comparably to other GNNs, but in significantly less time.
* **Node Property Prediction**: HashGNN's ability to create representative embeddings can be used to predict the properties or attributes of a node. This can be applied in scenarios like predicting the category of a product in an e-commerce platform, identifying the role of a person in a social network, or predicting potential churn in a customer network.
* **Fraud Detection in Financial Networks**: In financial networks with heterogeneous transaction types, HashGNN's ability to distinguish between different relationship types could provide more meaningful embeddings. It can potentially identify anomalous nodes (indicative of fraudulent activity) more accurately by taking into account the varied transaction types.

*This is an unofficial guide, so as always be sure to consult the [official Neo4j Graph Data Science documentation](https://neo4j.com/docs/graph-data-science/current/) for the most up-to-date, authoritative content about the GDS library and individual algorithms.*




