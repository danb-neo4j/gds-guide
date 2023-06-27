# GraphSAGE
## Summary
* [GraphSAGE](https://neo4j.com/docs/graph-data-science/current/machine-learning/node-embeddings/graph-sage/) is an inductive graph embedding algorithm that generates node embeddings based on local neighborhood characteristics, making it possible to compute embeddings for unseen nodes or graphs. 
* Instead of training individual embeddings for each node, the algorithm learns a function that generates embeddings by sampling and aggregating features from a node’s local neighborhood. 
* GraphSAGE requires training, which may require significant time and computational resources. Therefore, training on a representative subgraph may be recommended because the learned function can be applied to the entire graph.  
* Because of its reliance on local neighborhood and node properties, GraphSAGE may produce a vector of zeros for isolated nodes or nodes where all of the properties are zero. 

## Pros and Cons
### Pros
* **Inductive Learning**: Unlike transductive methods, GraphSAGE can generate embeddings for unseen nodes or entirely new graphs by learning from the node's local neighborhood features. This makes it more flexible and applicable to real-world situations where the graph might be dynamic and constantly changing.
* **Scalability**: GraphSAGE uses neighborhood sampling techniques to create fixed-size training instances. This makes the algorithm scalable to large graphs as it does not require the full graph to be loaded into memory, unlike many other graph embedding methods.

### Cons
* **Training Required**: GraphSAGE requires training, which may require significant time and computational resources. Therefore, training on a representative subgraph may be recommended because the learned function can be applied to the entire graph.
* **Isolated Nodes**: Because of its reliance on local neighborhood and node properties, GraphSAGE may produce a vector of zeros for isolated nodes or nodes where all of the properties are zero.

## Use Cases
* **Dynamic Graphs**: Given the inductive nature of GraphSAGE, it can be highly beneficial for use cases involving dynamic graphs, where the structure of the graph can change over time, or new nodes can be added. Examples could include real-time traffic networks, where new nodes (vehicles, intersections, etc.) may be added, or temporal social networks, where the connections between individuals evolve over time. GraphSAGE can generate embeddings for these new nodes based on their local neighborhood, thus effectively capturing the dynamic nature of the graph.
* **Node Classification**: Given its ability to generate embeddings that incorporate node features, GraphSAGE can be utilized effectively for node classification tasks, such as classifying users in a social network based on their behavior, or categorizing scientific articles in a citation network based on their content and citation relationships.

*This is an unofficial guide, so as always be sure to consult the [official Neo4j Graph Data Science documentation](https://neo4j.com/docs/graph-data-science/current/) for the most up-to-date, authoritative content about the GDS library and individual algorithms.*


