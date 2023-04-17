# Usecase-Specific GDS Resources
## Fraud and Anti-Money Laundering
Blog with Notebook and Data: [Exploring Fraud Detection With Neo4j & Graph Data Science](https://neo4j.com/developer-blog/exploring-fraud-detection-neo4j-graph-data-science-summary/) This four-part Neo4j blog series with an accompanying data set and notebook dives deep into a fraud use case. The series and notebook were published by Zach Blumenfeld, a graph data science product specialist at Neo4j. Note: Using the same data set, Tomaz Bratanic has also published excellent blogs that cover [enhancing machine learning models](https://medium.com/neo4j/using-neo4j-graph-data-science-in-python-to-improve-machine-learning-models-c55a4e15f530) and [user segmentation](https://medium.com/neo4j/user-segmentation-based-on-node-roles-in-the-peer-to-peer-payment-network-1a766c60a4ee).

## Customer/Patient Journey
Customer Journey Analysis aims to understand how an individual interacts with an organization including all of their touchpoints and experiences from becoming a prospect through conversion into a customer into retention or churn. Customer Journey Analysis includes or can focus on both digital and non-digital interaction, as well as specific industries such as healthcare (patient journey analysis). 
<br>
This analysis can be used in several ways:
* Improving the experience as a prospect, such as by shortening or simplifying the process of becoming a customer to improve overall conversion.
* Identifying customer pain points that reduce overall product adoption or effectiveness, decrease customer satisfaction, and potentially lead to customer churn.
* Identifying points of intervention, including specific timing and methods, that may improve overall customer satisfaction.
* Reducing costs by diverting customers from high-cost support channels (e.g., support agents) to lower-cost support methods (e.g., automated assistants, support portals, etc…) that can be equally effective. 
* Increasing revenue per customer by understanding opportunities to expand the customer relationship via upsells. 
* Personalizing marketing materials based upon customer interactions and identified customer segments. 

Several GDS Algorithm Families can be combined to perform highly-impactful customer or patient journey analysis:
* **Centrality:** Degree Centrality can provide statistics about specific customer encounters or actions, which PageRank can help identify influential or representative customers within a community or population. 
* **Similarity:** Node Similarity can be used to identify similarities based upon graph structure, while K-Nearest Neighbors (KNN) can be used to calculate similarity based upon node properties. KNN can be more performant than Node Similarity on large graphs and can leverage graph embeddings as part of its calculation. 
* **Community Detection:** Label Propagation can be used to identify communities within a fully connected similarity graph, while Louvain or Weakly Connected Components can be used on other graphs and subgraphs. 
* **Pathfinding:** With the graph data formatted as journeys, pathfinding algorithms can identify the length of patient journeys, key events, and specific patterns within journeys.
* **Embeddings:** Graph embeddings are highly effective in capturing graph structures, including full journeys and similarity graphs, in low-dimensional vectors. Embeddings can be used to capture subgraphs, such as if the data is too large for the Node Similarity algorithm to complete, and be used when running KNN. Embeddings are also effective in helping to visualize communities and detect outliers. 

The following resources are patient journey and health care focused. 
* Blog: [Modeling Patient Journeys with Neo4j](https://medium.com/neo4j/modeling-patient-journeys-with-neo4j-d0785fbbf5a2)
* Book: [Graph Data Processing with Cypher](https://www.packtpub.com/product/graph-data-processing-with-cypher/9781804611074) Although this book is about Cypher, it uses a health care and patient data set similar to in the Patient Journey blog above.

Performing customer or patient journey analysis might require refactoring the data model to include temporal connections among steps of each journey. An example of creating this type of relationship is available in the [Github repo](https://github.com/Neo4jSolutions/patient-journey-model/blob/master/ingest/config.yml) for the [Modeling Patient Journeys with Neo4j](https://medium.com/neo4j/modeling-patient-journeys-with-neo4j-d0785fbbf5a2) blog post.

Additionally, a [Patient Journey Demo with GDS](https://github.com/danb-neo4j/patient_journey) is available on Github. As of April 2023 this repo is still in development and being updated.

## Recommendation Systems 
* Blog with Notebook and Data: [Exploring Practical Recommendation Systems In Neo4j](https://towardsdatascience.com/exploring-practical-recommendation-engines-in-neo4j-ff09fe767782)

## Machine Learning Features
* Blog: [Using Neo4j Graph Data Science in Python to Improve Machine Learning Models](https://neo4j.com/developer-blog/using-neo4j-graph-data-science-in-python-to-improve-machine-learning-models/)
* Book: [Graph-Powered Machine Learning](https://www.manning.com/books/graph-powered-machine-learning) This book by Alessandro Negro, one of the most experienced graph data science practitioners today, focuses on the conceptual and practical application of graph data science for various machine learning use cases. 

## Logistics
Blog with Notebook and Data: Graph Data Science for Supply Chains ([pt1](https://neo4j.com/developer-blog/supply-chain-neo4j-gds-bloom/), [pt2](https://neo4j.com/developer-blog/gds-supply-chains-metrics-performance-python/), [pt3](https://neo4j.com/developer-blog/gds-supply-chain-pathfinding-optimization/))

## Health Care
Workshop: [Road to NODES Workshop Series - Healthcare Analytics Using Neo4j](https://www.youtube.com/live/5DZfOLspVDM?feature=share)
