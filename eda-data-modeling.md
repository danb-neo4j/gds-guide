# Graph EDA and Graph Data Modeling
## EDA and Graph EDA

### Importance of Traditional (Non-Graph) Exploratory Data Analysis (EDA)
Performing EDA on your data before developing a data model and loading data into Neo4j provides numerous benefits that often pay dividends further into the analysis. These include:
* **Identifying data quality issues:** EDA can reveal data quality problems like missing, inconsistent, or duplicate values, which may impact the accuracy and reliability of the graph data model. Addressing these issues early on helps avoid complications during data loading, such as when necessary properties are missing in the underlying data.
* **Discover relationships:** EDA can help identify less-obvious relationships among data elements, potentially leading to a more realistic and impactful graph data model. 
* **Optimizing the graph data model:** By understanding the data's characteristics and patterns, you can design a more efficient graph data model with appropriate nodes and  labels, relationship types, and property constraints, which can facilitate better database performance and scalability.
* **Understanding data characteristics:** By understanding the characteristics of both numeric and categorical data, you can make decisions about how to leverage each for nodes, relationships, and properties as you design the data model and perform data analysis.
* **Streamline data transformations:** EDA enables you to identify the necessary data numeric and categorical transformations and preprocessing steps required to convert data into a graph format. 
* **Validate domain assumptions:** EDA allows you to validate domain knowledge and assumptions against the actual data, ensuring that the graph data model both aligns with real-world understanding and meets end-user requirements. 

### How is Graph EDA Different from Traditional EDA?
Graph EDA and traditional EDA differ in their data representation, focus, analysis methods, and handling of relationships and connectivity. Graph EDA is specifically tailored for analyzing complex networks and relationships, while traditional EDA is better suited for analyzing tabular data with direct relationships between variables. More specifically, they are different in the following ways:
* **Data format:** While traditional EDA addresses data typically in tabular formats such as spreadsheets, CSV files, or relational databases, graph EDA focuses on the nodes and relationships that encompass graph data.
* **Data structure:** Traditional EDA primarily focuses on the distribution and relationships between individual variables within tabular data. In contrast, Graph EDA emphasizes the topology and connectivity of a graph, exploring patterns and structures among the nodes and relationships. 
* **Analytic methods:** Traditional EDA typically uses statistical methods and visualization techniques like histograms, bar charts, box plots, and scatter plots to explore data distributions and correlations between variables. Graph EDA often employs graph-specific techniques and algorithms, such as centrality measures, community detection, and path analysis to understand the graph's structure and characteristics. However, graph EDA can also leverage statistical techniques such as counting different types of relationships a node has to analyze distributions and identify outliers. 
* **Connectivity analysis:** Graph EDA allows for the analysis of data connectivity, such as detecting disconnected components or measuring the shortest path between nodes. Traditional EDA does not have a direct equivalent since tabular data lacks explicit connections between records.
* **Handling of relationships:** Traditional EDA focuses on direct relationships between variables and may require complex join operations in relational databases to explore relationships between multiple tables. Graph EDA, however, is designed to handle complex relationships between entities, enabling the discovery of indirect connections or multi-hop patterns. 

### Why Perform Graph EDA
Performing graph EDA after loading tabular data into Neo4j is a critical step in the overall graph analysis process because transforming non-graph data into a graph format fundamentally changes the nature of the data. Rather than rows and columns (or potentially other formats), the data now comprises nodes, explicit relationships, and properties. Therefore, graph EDA is essential to validating the data model, understanding the nature of the new graph data, and identifying potential data issues or outliers that may affect subsequent analysis, especially if planning to leverage the Neo4j Graph Data Science (GDS) library of algorithms. 

### Performing Graph EDA with Neo4j and Python
There are a variety of approaches you can use when performing graph EDA. The following section will contain primarily Cypher statements that you can use in the Neo4j browser or via the Neo4j GDS Python Client in a Jupyter Notebook or other development environment. One of the benefits of leveraging Python and a Notebook is the ability to visualize the results in ways that make them more interpretable for end-users. You can do so by wrapping the Cypher statement in the following: `gds.run_cypher(“““ <insert Cypher here> ”””)`

#### Graph Schema Validation
Start by examining the schema of the graph, which will provide an overview of the nodes, relationships, and properties in the database and enable you to validate that they align to your expectations. To do so use the `CALL db.schema.visualization()` command in the Neo4j Browser. Submitting this command via the Python client will not return the schema visualization.

#### Analyze Data Distributions
Several queries can enable you to analyze the counts and distributions of nodes, relationships and properties in your graph database. 

**Node Counts**

```
CALL apoc.meta.stats()
YIELD labels
UNWIND keys(labels) AS nodeLabel
RETURN nodeLabel, labels[nodeLabel] AS nodeCount
```

**Relationship Counts**

```
CALL apoc.meta.stats()
YIELD relTypesCount
UNWIND keys(relTypesCount) AS relationshipType
RETURN relationshipType, relTypesCount[relationshipType] AS relationshipCount`
```

**Node Property Distribution**
<br>*Returning the hypothetical age property on Patient nodes*

```
MATCH (p:Patient)
RETURN
	p.age AS Age,
	count(p) AS count
ORDER BY Age
```

**Node Property Statistics**
<br>*Using the same hypothetical age property on Patient nodes*

```
MATCH (p:Patient)
RETURN
	min(p.age) AS minAge,
	max(p.age) AS maxAge,
	avg(p.age) as avgAge,
	percentileCount(p.age, 0.5) AS medianAge
```

**Node Degree Distribution**
<br>*Distribution of all relationships for all nodes in the database. This query can be modified to address specific node types by modifying the initial MATCH (n) to include a label. It can also be modified for a specific relationship type by including [:REL_TYPE] between the dashes of (n)--(). If your graph is large, you can also add a LIMIT statement to return only the top N nodes.* 

```
MATCH (n)
WITH n, size((n)--()) AS degree
RETURN
	degree AS Degree,
	count(n) AS Count
ORDER BY Degree
```

**Relationship Property Distribution**
```
MATCH ()-[r:SENDS_MONEY]-()
RETURN
	r.amount AS Amount,
	count(r) AS count
ORDER BY Amount
Relationship Property Statistics
MATCH ()-[r:SENDS_MONEY]-()
RETURN
	min(r.amount) AS minAmount,
	max(r.amount) AS maxAmount,
	avg(r.amount) AS avgAmount,
  percentileCount(r.amount, 0.5) AS medianAmount
```

