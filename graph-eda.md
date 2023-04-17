# Graph Exploratory Data Analysis (EDA)

## Importance of Traditional (Non-Graph) Exploratory Data Analysis (EDA)
Performing EDA on your data before developing a data model and loading data into Neo4j provides numerous benefits that often pay dividends further into the analysis. These include:
* **Identifying data quality issues:** EDA can reveal data quality problems like missing, inconsistent, or duplicate values, which may impact the accuracy and reliability of the graph data model. Addressing these issues early on helps avoid complications during data loading, such as when necessary properties are missing in the underlying data.
* **Discover relationships:** EDA can help identify less-obvious relationships among data elements, potentially leading to a more realistic and impactful graph data model. 
* **Optimizing the graph data model:** By understanding the data's characteristics and patterns, you can design a more efficient graph data model with appropriate nodes and  labels, relationship types, and property constraints, which can facilitate better database performance and scalability.
* **Understanding data characteristics:** By understanding the characteristics of both numeric and categorical data, you can make decisions about how to leverage each for nodes, relationships, and properties as you design the data model and perform data analysis.
* **Streamline data transformations:** EDA enables you to identify the necessary data numeric and categorical transformations and preprocessing steps required to convert data into a graph format. 
* **Validate domain assumptions:** EDA allows you to validate domain knowledge and assumptions against the actual data, ensuring that the graph data model both aligns with real-world understanding and meets end-user requirements. 

## How is Graph EDA Different from Traditional EDA?
Graph EDA and traditional EDA differ in their data representation, focus, analysis methods, and handling of relationships and connectivity. Graph EDA is specifically tailored for analyzing complex networks and relationships, while traditional EDA is better suited for analyzing tabular data with direct relationships between variables. More specifically, they are different in the following ways:
* **Data format:** While traditional EDA addresses data typically in tabular formats such as spreadsheets, CSV files, or relational databases, graph EDA focuses on the nodes and relationships that encompass graph data.
* **Data structure:** Traditional EDA primarily focuses on the distribution and relationships between individual variables within tabular data. In contrast, Graph EDA emphasizes the topology and connectivity of a graph, exploring patterns and structures among the nodes and relationships. 
* **Analytic methods:** Traditional EDA typically uses statistical methods and visualization techniques like histograms, bar charts, box plots, and scatter plots to explore data distributions and correlations between variables. Graph EDA often employs graph-specific techniques and algorithms, such as centrality measures, community detection, and path analysis to understand the graph's structure and characteristics. However, graph EDA can also leverage statistical techniques such as counting different types of relationships a node has to analyze distributions and identify outliers. 
* **Connectivity analysis:** Graph EDA allows for the analysis of data connectivity, such as detecting disconnected components or measuring the shortest path between nodes. Traditional EDA does not have a direct equivalent since tabular data lacks explicit connections between records.
* **Handling of relationships:** Traditional EDA focuses on direct relationships between variables and may require complex join operations in relational databases to explore relationships between multiple tables. Graph EDA, however, is designed to handle complex relationships between entities, enabling the discovery of indirect connections or multi-hop patterns. 

## Why Perform Graph EDA
Performing graph EDA after loading tabular data into Neo4j is a critical step in the overall graph analysis process because transforming non-graph data into a graph format fundamentally changes the nature of the data. Rather than rows and columns (or potentially other formats), the data now comprises nodes, explicit relationships, and properties. Therefore, graph EDA is essential to validating the data model, understanding the nature of the new graph data, and identifying potential data issues or outliers that may affect subsequent analysis, especially if planning to leverage the Neo4j Graph Data Science (GDS) library of algorithms. 

## Performing Graph EDA with Neo4j and Python
There are a variety of approaches you can use when performing graph EDA. The following section will contain primarily Cypher statements that you can use in the Neo4j browser or via the Neo4j GDS Python Client in a Jupyter Notebook or other development environment. One of the benefits of leveraging Python and a Notebook is the ability to visualize the results in ways that make them more interpretable for end-users. You can do so by wrapping the Cypher statement in the following: `gds.run_cypher(“““ <insert Cypher here> ”””)`

### Graph Schema Validation
Start by examining the schema of the graph, which will provide an overview of the nodes, relationships, and properties in the database and enable you to validate that they align to your expectations. To do so use the `CALL db.schema.visualization()` command in the Neo4j Browser. Submitting this command via the Python client will not return the schema visualization.

### Analyze Data Distributions
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
<br>*This query returns the distribution of all relationships for all nodes in the database. It can be modified to address specific node types by modifying the initial MATCH (n) to include a label. It can also be modified for a specific relationship type by including [:REL_TYPE] between the dashes of (n)--(). If your graph is large, you can also add a LIMIT statement to return only the top N nodes.* 

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

### Identify Missing or Incorrect Data
It is important to check for missing or incorrect property values in nodes and relationships. For example, look for Artist nodes with missing names via:

```
MATCH (p:Patient) 
WHERE NOT EXISTS(p.name) 
RETURN p 
```

Similarly, you can validate relationships that exist only between the proper types of nodes. For example, to identify that :HAS_ENCOUNTER only exists between Patient and Encounter nodes, you could use the following Cypher:

```
MATCH (p)-[r:HAS_ENCOUNTER]->(e)
WHERE NOT (p:Patient AND e:Encounter)
RETURN p, r, e
```

### Identify Unconnected Nodes
As part of validating the data and identifying potential issues, we may want to identify nodes that are completely unconnected, or that lack a specific type of transaction. This can help uncover data loading issues, data quality issues, or nodes that are simply anomalous.

```
// Cypher query to count unconnected nodes
MATCH (n)
WHERE NOT (n)--()
RETURN count(n)

// Cypher query to identify specific unconnected nodes
MATCH (n)
WHERE NOT (n)--()
RETURN n
```

The GDS library can also be used to identify unconnected nodes. For instance, you could project a portion of the graph into memory then use the Weakly Connected Components algorithm to identify components that contain single nodes. 

### Centrality Analysis for Outlier Detection
There are a variety of ways to identify anomalies and outliers in your graph database. Several of the statistical queries under the Analyze Data Distributions section of this document can identify the presence of outlier nodes or relationships. However, additional techniques can be used to identify and analyze specific outliers that might occur. 
<br>

**Query Nodes with Most Relationships**<br> 
The following query can be helpful to identify the top N nodes that have the most of a specific type of relationship. This can identify supernodes that may impact Cypher query and GDS Algorithm performance. 
```
MATCH (p:Patient)
MATCH (p)-[r:HAS_ENCOUNTER]->()
RETURN p.id, count(r) AS encounterCount
ORDER BY encounterCount DESC
LIMIT 25
```

**GDS: Degree Centrality**<br>
The GDS Degree Centrality algorithm is useful for creating statistics that can support calculating ratios and identifying outliers. The same could also be performed using Cypher (and, if a large graph, apoc.periodic.iterate()). However, one of the benefits of using GDS and Graph Projections is that we can create a single projection and run multiple algorithms on it.

<br>
For example, if we were analyzing a financial transaction network we may want to identify customers who had the most transactions. We will demonstrate the following using Python and the GDS Python Client.

```
# generate graph projection
g, _ = gds.graph.project(
         graph_name = 'customer_tx',
         node_spec = [‘Customer’, ‘Transaction’],
         relationship_spec = {‘SENT_MONEY’:{'properties':‘tx_amount’}
)

# create total transaction count property on Customer nodes
_ = gds.degree.write(G=g, 
                     nodeLabels = [‘Customer’, ‘Transaction’],
                     relationshipTypes = [‘SENT_MONEY’],
                     writeProperty=’total_tx_count’)

# create total transaction amount property on Customer nodes
_ = gds.degree.write(G=g, 
                     nodeLabels = [‘Customer’, ‘Transaction’],
                     relationshipTypes = [‘SENT_MONEY’],
                     relationshipWeightProperty = ‘tx_amount’,
                     writeProperty=’total_tx_amount’)
```

**GDS: Ratio Analysis**<br>
Once we create properties on Nodes we can use them to calculate ratios that can indicate data issues or outliers in our graph. Continuing the financial transaction above, we could calculate the ratio of (total_tx_amount / total_tx_count) to calculate an average amount per transaction. 

```
// create tx_ratio property on customers with at least one transaction
MATCH (c:Customer) 
WHERE c.total_transaction_count <> 0
SET c.tx_ratio = c.total_transaction_amount * 1.0 / c.total_transaction_count

// identify customers with five or more transactions and an abnormal tx_ratio
MATCH (c:Customer)
WHERE c.total_transaction_count >= 5 AND c.tx_ratio < 1.0
RETURN 
	c.id AS id, 
	c.name AS name, 
	c.total_transaction_count AS tx_count,
	c.total_transaction_amount AS tx_amount,
	c.tx_ratio AS tx_ratio
```

### Community Detection 
Community Detection is often part of an overall GDS workflow, and it can be especially useful when exploring a newly created graph database. Depending on the nature of your graph, you can create a graph projection containing some or all nodes and relationships and then apply different community detection algorithms to identify both densely connected groupings as well as isolated or unconnected nodes. The resulting communities can indicate underlying structures within your graph that may merit further exploratory analysis. 
```
# generate graph projection via Python client
g, _ = gds.graph.project(
         graph_name = 'customer_tx',
         node_spec = [*],
         relationship_spec = [*])
```

For overall graphs with unclear structure, [Louvain](https://neo4j.com/docs/graph-data-science/current/algorithms/louvain/) or [Weakly Connected Components](https://neo4j.com/docs/graph-data-science/current/algorithms/wcc/) would likely be a good algorithm to start with. Note, with these algorithms you can specify specific node and relationship types, and like with other GDS algorithms can write the results back to the underlying nodes rather than streaming the results to a dataframe.
```
# stream WCC results back to Python dataframe
_ = gds.louvain.stream(G=g)

# stream WCC results back to Python dataframe
_ = gds.wcc.stream(G=g)
```

For graphs that are fully connected, Label Propagation could be a more appropriate starting algorithm. 
```
# stream WCC results back to Python dataframe
_ = gds.labelPropagation.stream(G=g)
```
## Additional Graph EDA Resources
[Road to NODES Workshop: Graph EDA Using the Neo4j GDS Client](https://www.youtube.com/live/oG9InPntehQ?feature=share)


