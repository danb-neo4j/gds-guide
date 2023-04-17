# Neo4j Database Resources
## Overview of Neo4j and Graph Databases 
*The following resources provide a high-level overview of graph databases and Neo4j. They are recommended for those who have not used graph databases or graph data before.*
* Video: [Neo4j in 100 Seconds](https://www.youtube.com/watch?v=T6L9EoBy8Zk)
* Guide: [What is a graph database?](https://neo4j.com/docs/getting-started/current/get-started-with-neo4j/graph-database/)
* Graph Academy: [Neo4j Fundamentals](https://graphacademy.neo4j.com/courses/neo4j-fundamentals/)
* Webinar: [Road to NODES 2022 Workshop Series: Intro to Neo4j](https://www.youtube.com/live/2oMqZUX1vZs?feature=share)

## Memory Configuration
*The following documentation should help you install Neo4j on various operating systems. In particular, memory configuration and consumption can have a dramatic impact on database and GDS algorithm performance.* 
* [Neo4j GDS System Requirements](https://neo4j.com/docs/graph-data-science/current/installation/System-requirements/)
* [Neo4j Memory Consumption](https://neo4j.com/developer/kb/understanding-memory-consumption/)

It is worth noting that the memory configuration for a Neo4j database with a heavy GDS workflow may be different than the configuration of a database where the workload is more transactional. This is because GDS workloads, including graph projections and the graph catalog, run on heap, increasing its overall importance for analytic workloads.
* In the neo4j.conf file increase both the heap initial size and heap max size parameters. For purely analytical workloads, a general recommendation is to set the heap space to about 90% of the available main memory. The specific parameters will depend on if you are running Neo4j 4.x or Neo4j 5.x. 

The page cache is used to cache the Neo4j data and will help to avoid costly disk access. While less important for GDS-focused analytic workloads, it can improve performance when writing back to the database from a graph projection.
* Also in neo4j.conf, adjust the dbms.memory.pagecache.size parameter to at least 50% of the remaining system memory after the heap size is considered. 

While these are general best practices, the optimal configuration for any Neo4j database instance will depend on the data, algorithms, and system processors and memory. It is also a best practice to run any graph algorithm in ‘estimate’ mode to identify if it will require more memory than is available. CALL gds.debug.sysinfo() can also provide information about available heap.

## Cypher
The following resources should be enough to get started with Cypher if you have a basic understanding of graphs and SQL. Additional Graph Academy courses along with the book referenced below are great options if you wish to dive deeper into Cypher. 
* Getting Started: [Various Cypher Resources](https://neo4j.com/docs/getting-started/current/cypher-intro/resources/)
* Graph Academy: [Cypher Fundamentals](https://graphacademy.neo4j.com/courses/cypher-fundamentals/)
* [Cypher Reference Card](https://neo4j.com/docs/cypher-refcard/current/)
* Book: [Graph Data Processing with Cypher](https://www.packtpub.com/product/graph-data-processing-with-cypher/9781804611074)
