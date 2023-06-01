# Graphs and Large Language Models
This page will provide an overview and links to resources for using Large Language Models (LLMs) with Neo4j and the Graph Data Science Library (GDS).

*Note: As with all content in this repo, this page is unofficial and intended to provide high-level information and links to various Neo4j and GDS resources. Large Language Models (LLMs) in particular are a fast-moving technology and this page will be updated as frequently as possible. However, always refer to [official Neo4j Documentation](https://neo4j.com/docs/) and releases for the most up-to-date information.* 

# How LLMs Can Support Graph Data Science
The following is based upon a conversation with ChatGPT 4.0, reviewed and edited by Daniel Bukowski, Neo4j GDS CSA. It is unofficial and intended to provide a high-level overview of how LLMs can potentially support Graph Data Science workflows, while also demonstrating how LLMs can also assist at the start of a research project such as this.  

## Initial Prompt
*I want you to act as an experienced graph data scientist who works at Neo4j. A customer asks you how large language models (LLMs) like ChatGPT can assist with graph data science, specifically using Neo4j Graph Data Science algorithms. How would you advise this customer to explore integrating LLMs into their graph data science workflows? What would likely be the easiest or most impactful ways in which an LLM can make them more productive and effective?*

## Response Summary
The below summarizes the following areas where ChatGPT suggested LLMs can potentially enhance GDS workflows:
* **Natural Language Processing (NLP) to Graph:** LLMs like GPT are fantastic for NLP tasks. They can process and understand text data at an advanced level. You can use this ability to convert unstructured data into a structured graph format. This is especially beneficial when working with large text datasets like articles, reviews, or social media posts. For example, an LLM can identify entities and their relationships from text data, which can then be used to build a knowledge graph in Neo4j.
* **Graph Embeddings:** A Graph Embedding represents nodes in a graph as a vector in a lower-dimensional space. These vectors can be used to perform tasks like link prediction, node classification, and community detection. LLMs can assist in generating node or edge embeddings by using graph properties or labels, which can then be input to Neo4j GDS algorithms. 
* **Natural Language Graph Interaction and Exploration:** LLMs can assist with graph data exploration by providing a natural language interface to your Neo4j database. For instance, you can build a system where a user asks a question in natural language, the LLM converts this question into a Cypher query, executes it, and then returns the results back in a human-friendly format. This allows non-technical users to explore and interact with the graph data easily.
* **Predictive Modeling and Recommendations:** LLMs can be used to augment the recommendation engines built on top of Neo4j. For example, the language model could understand the context and nuances of user reviews or feedback and enhance the quality of recommendations.
* **Graph Completion:** LLMs can assist with a variety of tasks including link prediction, node attribute prediction, edge attribute prediction, and even graph generation. For example, you can use an LLM to predict the missing edges in a graph, which can then be used to improve the quality of recommendations.
* **Graph Neural Networks:** LLMs can indeed play a supportive role when it comes to using Graph Neural Networks (GNNs) or other forms of Geometric Deep Learning. As with other applications, LLMs should be seen as enhancements to and not replacements of GNN-focused tools. 


## Response Details
*As a graph data scientist, it's exciting to see how large language models (LLMs) such as ChatGPT can be integrated into your graph data science workflows. There are numerous ways in which LLMs can be utilized to enhance and streamline graph data science tasks, specifically with the use of Neo4j Graph Data Science (GDS) algorithms. Here are some suggestions:*

### Natural Language Processing (NLP) to Graph

**Entity Recognition:**

LLMs can be especially helpful when identifying entities in text data. Like nodes in a graph, entities are usually nouns such as people, places, organizations, products, or any other object of interest. For instance, in the sentence "Apple Inc. is planning to open a new store in New York", the entities would be "Apple Inc.", "store", and "New York".

**Relationship Extraction:**

After identifying the entities, the next step is to determine the relationships between these entities. In the above sentence, the relationships could be represented as "Apple Inc. - Is planning to open - Store" and "Store - In - New York". LLMs can also assist in this task by understanding the context and nuances of the text data, potentially after fine-tuning.

**Graph Creation:**

Once entities and relationships have been identified, these can be transformed into a graph. In the graph, entities would be represented as nodes, and relationships as edges between the nodes.

A large language model like GPT can assist greatly in this process, thanks to its advanced understanding of language context and syntax. The model can be used to extract entities and relationships from a wide range of text data, ranging from social media posts and articles to scientific papers and business reports.

The resulting graph can then be used with graph algorithms to find the shortest path between entities, detect communities, predict future connections, and much more. All of this could potentially provide valuable insights that could not be obtained from the raw, unstructured text data.

However, it's important to note that NLP is a complex field and extracting entities and relationships from text can be challenging, especially when dealing with ambiguous or complex sentences. There are various NLP techniques and models to handle such tasks, and depending on the complexity of the text and the domain, additional work like domain-specific training or fine-tuning of the model might be necessary.

**Resources:** 

[Creating a Knowledge Graph From Video Transcripts With ChatGPT](https://medium.com/neo4j/creating-a-knowledge-graph-from-video-transcripts-with-gpt-4-52d7c7b9f32c)  

### Graph Embeddings

LLMs are primarily used for NLP tasks and not directly used for creating graph embeddings. However, they can indirectly contribute to generating graph embeddings in a few ways:

**Node Feature Extraction:** LLMs like GPT can process node features that are text data. For instance, if you have nodes representing scientific articles, an LLM could be used to understand the article text and create meaningful feature representations. These could then be used as input to a graph embedding algorithm.

**Annotation and Tagging:** LLMs can assist in the annotation of nodes and edges based on the context or other text data associated with them. These annotations can later be used as features for the nodes or edges while creating graph embeddings.

**Sequence-to-sequence tasks:** If a graph traversal path can be treated as a sequence, GPT, which is good at handling sequence data, or another LLM might be used for encoding these sequences. However, this is an experimental application and might not always yield good results.

*Remember, the primary objective of using an LLM like GPT in graph data science is not to replace existing graph algorithms, but rather to supplement and enhance them. This is particularly true for tasks involving text data, where LLMs can provide significant advantages over traditional methods.*

In most standard graph data science tasks, dedicated graph embedding algorithms like node2vec, DeepWalk, or GraphSAGE are used, as they are designed specifically for the unique challenges and requirements of working with graph data. LLMs like GPT can complement these algorithms, especially when the graph involves complex text data as node or edge attributes.

**Resources:**
[GDS Guide: Graph Embeddings Resources](https://github.com/danb-neo4j/gds-guide/blob/main/gds-resources.md#embeddings)

[Explanation of GDS Embedding Algorithms](https://github.com/danb-neo4j/gds-guide/blob/main/graph-embeddings.md)

[Text Embedding — What, Why and How?](https://medium.com/@yu-joshua/text-embedding-what-why-and-how-13227e983ba7) 

### Natural Language Graph Interaction and Exploration

**Natural Language Interaction** 

LLMs can make the data exploration process more intuitive and efficient by allowing users to interact with the data in a natural language format. For instance, a user could ask a question about the data in plain English, such as "What are the top 10 nodes with the highest degree centrality?" The LLM can parse this question, convert it into a corresponding Cypher query, execute the query on the database, and then translate the result back into plain English.

This kind of Natural Language Interface to Databases (NLIDB) makes the data more accessible to non-technical users and can save data scientists time by allowing them to quickly query the data without having to write complex queries manually.

**Enhanced Data Explanation:**

Another benefit of LLMs is their ability to generate human-friendly explanations of complex data and models. They can provide context, summarize findings, and explain the meaning behind the data and the results of algorithms.

For instance, suppose you've run a community detection algorithm on your graph data. The LLM could generate a summary of the results, explaining how many communities were detected, what the key characteristics of each community are, and what this might imply in the context of the data. Such capability is extremely valuable for presenting findings to stakeholders and for making the results of data science workflows understandable to non-experts.

**Resources:** 
[LangChain with Neo4j](https://towardsdatascience.com/langchain-has-added-cypher-search-cb9d821120d5)
[Building An Academic Knowledge Graph with OpenAI & Graph Database](https://medium.com/@yu-joshua/building-an-academic-knowledge-graph-with-openai-graph-database-12b320f08ef0) 

### Predictive Modeling and Recommendations

**Predictive Modeling:**

Predictive modeling involves using statistical techniques and machine learning algorithms to predict future outcomes based on historical data. In the context of graph data science, predictive modeling often involves tasks like link prediction (predicting whether an edge should exist between two nodes) and node classification (predicting the category or properties of a node).

LLMs can assist with predictive modeling by providing rich, contextual representations of node or edge data, particularly when it comes to textual data. For instance, if nodes represent documents or users, and these nodes have associated text (e.g., document text, user reviews), an LLM can be used to generate meaningful representations of this text data. These representations can serve as additional features to enhance the performance of graph-based predictive models.

**Recommendations:**

In the realm of recommendation systems, LLMs can be particularly beneficial. Most recommendation systems are based on the idea of similarity - if two users have similar tastes, they're likely to enjoy the same items. Graph databases are excellent at storing and retrieving this kind of relational data.

Here's where LLMs come in: they can be used to enhance the similarity measures used in these recommendation algorithms. If user or item profiles contain text data (like user reviews or product descriptions), an LLM can understand and extract meaningful features from this text. This could lead to more accurate similarity measures and thus more precise recommendations. 

For example, imagine a scenario where users write reviews for books. By analyzing the reviews using LLM, you can understand the sentiment expressed by users, their favorite aspects of the books, and even nuanced preferences. This enriched profile can lead to more personalized and accurate recommendations.

Furthermore, LLMs can assist in generating natural language explanations for the recommendations, providing users with understandable reasons for why a particular item was recommended to them. Such explainability can lead to increased user trust and engagement with the recommendation system.

Integrating LLMs into these workflows doesn't replace existing graph algorithms; it supplements them. The combination of traditional graph algorithms and the contextual understanding provided by LLMs can significantly enhance the performance and usefulness of predictive modeling and recommendation systems in graph data science.

**Resources:**
[GDS Guide: Recommendation Systems](https://github.com/danb-neo4j/gds-guide/blob/main/usecase-specific.md#recommendation-systems)

### Graph Completion

**Link Prediction:**

In graph completion tasks, one common problem is link prediction, i.e., predicting whether a relationship (edge) should exist between two entities (nodes). This can be especially challenging when the graph is large and sparse. When nodes in a graph have associated text data, an LLM can be used to generate embeddings or semantic representations of this text data. These representations can provide additional context that can improve the accuracy of link prediction algorithms.

For example, consider a graph where nodes represent academic papers and edges represent citations. If we want to predict potential future citations, the text data associated with each paper (like the abstract or even the full text) can be used by an LLM to generate meaningful embeddings. These embeddings can capture the semantic similarity between papers, which can then be used to predict potential citations.

**Node Attribute Prediction:**

Another aspect of graph completion is predicting missing node attributes. Again, if nodes have associated text data, an LLM can be utilized. For instance, consider a social network where each user (node) has a profile description. An LLM can process these descriptions to predict missing attributes for some users, such as their interests or profession.

**Edge Attribute Prediction:**

Similar to node attribute prediction, LLMs can also help predict missing edge attributes. For instance, in a graph representing email communication between employees in a company, if each email (edge) has associated text data (the content of the email), an LLM can be used to infer missing edge attributes, such as the sentiment or subject of the email.

**Graph Generation:**

More generally, LLMs could potentially be used in graph generation tasks, where the goal is to generate a realistic graph that has similar properties to a given graph. If the graph has associated text data, an LLM could be used to generate text for the nodes or edges in the new graph.


### Graph Neural Networks (Geometric Deep Learning)

**Node Feature Extraction:**

As discussed previously, when nodes in a graph have associated textual data, an LLM like GPT can be instrumental in deriving meaningful representations of this data. These representations can then serve as input features to the GNN. For instance, in a citation network where each node represents a scientific paper and contains the paper's abstract, an LLM could generate a vector representation of each abstract, capturing the semantic meaning in a form that the GNN can utilize.

**Data Annotation and Labeling:**

In supervised learning tasks with GNNs, node or edge labels are often necessary. Sometimes, these labels can be inferred from associated text data using an LLM. For example, in a social network, user-generated text could potentially be used to infer user attributes or interests.

**Model Interpretability:**

GNNs, like many other types of neural networks, can often be "black boxes", where it's difficult to understand why they're making certain predictions. LLMs could potentially be used to generate natural language explanations of a GNN's output. For instance, given the input features and weights of a GNN, an LLM could be trained to generate a textual explanation of why the GNN is making a certain prediction.

**Semi-Supervised Learning Support:**

GNNs are often used in a semi-supervised learning context where only a small fraction of nodes have labels. In such cases, LLMs can be used to provide additional context or features that can assist the GNN in propagating labels to the unlabeled nodes.

One more point worth mentioning is the advent of Graph Transformers, which are an adaptation of the transformer models (the underlying model architecture of GPT) for graph-structured data. This is an exciting research area that attempts to directly incorporate the strengths of LLMs into a graph neural network framework.

**Resources:**

[GNNs & Neo4j - Examples and Technical Resources](https://github.com/neo4j-product-examples/graph-machine-learning-examples/blob/main/gnns-with-neo4j/README.md)
