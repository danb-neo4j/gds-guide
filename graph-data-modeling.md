# Graph Data Modeling
*Developing the proper data model is critical for any graph use case. When using GDS, the algorithms and use case will also need to be considered when developing the model.*

## General Graph Data Modeling Best Practices
* Getting Started: [Graph Data Modeling](https://neo4j.com/docs/getting-started/current/data-modeling/)
* Graph Academy: [Graph Data Modeling Fundamentals](https://graphacademy.neo4j.com/courses/modeling-fundamentals/)
* Webinar: [Identifying Graph Shaped Problems](https://www.youtube.com/watch?v=keZURbOo4-M)
* Graph Data Modeling Blog Series by David Allen
  * [Categorical Variables](https://medium.com/neo4j/graph-data-modeling-categorical-variables-dd8a2845d5e0)
  * [Labels](https://medium.com/neo4j/graph-modeling-labels-71775ff7d121)
  * [Relationships](https://medium.com/neo4j/graph-data-modeling-all-about-relationships-5060e46820ce)
  * [Keys](https://medium.com/neo4j/graph-data-modeling-keys-a5a5334a1297)

## Temporal Graph Data Modeling
### Overview
A temporal graph data model is a specific type of graph data model that captures the order, timing, and relationships between events or entities as they occur in sequence. It incorporates time-based node properties and relationships to represent the temporal aspects of the data, enabling efficient querying, traversal, and analysis of time-ordered sequences and temporal relationships. 
<br>
Temporal graph data models are typically used when:
* Time-based relationships are essential: When you need to incorporate time-based relationships between entities or analyze the sequence of events over time, a temporal graph data model is ideal. 
* You require efficient traversal of connected data: Graph databases are designed for efficient traversal of related data. If your use case involves finding patterns, paths, or sequences in your data, a graph data model can offer faster query performance compared to other data models, such as relational or document-oriented databases.

Common industry or other use cases for temporal graph data models include:
* **Customer Journey**, where the graph captures specific events where the customer interacts with the organization. This can include both digital and physical encounters. In the healthcare or life sciences industries this type of analysis would be characterized as a Patient Journey.
* **Manufacturing and Logistics**, where the graph captures steps in a manufacturing process or supply chain. Such a graph can also be used to optimize routes and identify potential bottlenecks in the supply chain or manufacturing process. 

### Creating Temporal Graph Relationships
In a temporal graph data model, nodes usually represent events or time-based entities, while relationships denote the temporal connections or sequences between these nodes. Temporal properties such as timestamps or durations can be stored as properties on the nodes or relationships to capture the time-related information. Relationships often have labels such as :NEXT, :PRECEDES, or :FOLLOWS, but any label that accurately captures the relationship would be appropriate. 

#### Simple Temporal Example 
The following is a very simple example for creating Event nodes connected in sequence.

```
// Create Event nodes with timestamp properties
CREATE (e1:Event {timestamp: datetime("2023-01-01T00:00:00"), description: "Event 1"})
CREATE (e2:Event {timestamp: datetime("2023-01-02T00:00:00"), description: "Event 2"})
CREATE (e3:Event {timestamp: datetime("2023-01-04T00:00:00"), description: "Event 3"})

// Create :FOLLOWS relationships between the events with the duration as a relationship property 
CREATE (e1)-[:NEXT {duration: duration.between(e1.timestamp, e2.timestamp)}]->(e2)
CREATE (e2)-[:NEXT {duration: duration.between(e2.timestamp, e3.timestamp)}]->(e3)

// OPTIONAL create an index on the Event timestamp property to improve performance if querying based upon these timestamps 
CREATE INDEX event_timestamp_index FOR (e:Event) ON (e.timestamp)

// Query the database
MATCH (e2:Event)-[r:FOLLOWS]->(e3:Event)
RETURN e2.description, e3.description, r.duration
```

### Patient Journey Example
The following [example code](https://github.com/Neo4jSolutions/patient-journey-model/blob/master/ingest/config.yml) will create relationships across a large Patient Journey database. The example is based upon the blog post [Modeling Patient Journeys with Neo4j](https://medium.com/neo4j/modeling-patient-journeys-with-neo4j-d0785fbbf5a2), which uses similar data to that in the book [Graph Data Processing with Cypher](https://www.packtpub.com/product/graph-data-processing-with-cypher/9781804611074). The underlying data model for this example has each Patient node directly connected to one or more Encounter nodes via the :HAS_ENCOUNTER relationship. More details are available in the blog post or book.

#### Full, Uncommented Code
This Cypher code snippet uses the APOC apoc.periodic.iterate procedure to create :NEXT relationships between consecutive patient encounters in a Patient Journey analysis, connecting encounters in the order they occurred based on their date property.

```
CALL apoc.periodic.iterate(
    'MATCH (p:Patient) RETURN p',
    'MATCH (p)-[:HAS_ENCOUNTER]->(e)
      WITH e
      ORDER BY e.date
      WITH collect(e) AS encounters
      WITH encounters, encounters[1..] as nextEncounters
      UNWIND range(0,size(nextEncounters)-1,1) as index
      WITH encounters[index] as first, nextEncounters[index] as second
      CREATE (first)-[:NEXT]->(second)',
    {iterateList:false})
```
#### Line-by-Line Code Explanation
`CALL apoc.periodic.iterate(`  This statement invokes the APOC (Awesome Procedures on Cypher) library's `apoc.periodic.iterate()` procedure which allows you to perform a large data operation in smaller, more manageable transactions. The procedure takes two Cypher queries as arguments and an optional configuration map. Managing transaction size is recommended for mid-to-large size graphs. 

`'MATCH (p:Patient) RETURN p'` This is the first argument of the apoc.periodic.iterate procedure. It retrieves all nodes in the database that have the label Patient.

`'MATCH (p)-[:HAS_ENCOUNTER]->(e)` This is the beginning of the second argument of the apoc.periodic.iterate procedure. It matches each Patient node to its corresponding Encounter nodes via the :HAS_ENCOUNTER relationship.

`WITH e ORDER BY e.date`  This orders the encounters by their date property. In Cypher ORDER BY defaults to ascending order.

`WITH collect(e) AS encounters` This collects each patient’s ordered encounters into a list called encounters.

`WITH encounters, encounters[1..] as nextEncounters` This line creates a new list called nextEncounters by removing the first element from the encounters list.

`UNWIND range(0, size(nextEncounters)-1, 1) as index` The UNWIND statement generates a range of index values from 0 to size(nextEncounters) - 1, with a step of 1. Each value in this range will be used to access the elements of the encounters and nextEncounters lists.

`WITH encounters[index] as first, nextEncounters[index] as second` This code assigns the encounter at the current index in the encounters list to the variable first, and the encounter at the same index in the nextEncounters list to the variable second.

`CREATE (first)-[:NEXT]->(second)',` This, the final line of the Cypher code in the second argument passed to apoc.periodic.iterate, creates a :NEXT relationship between the first encounter and the second encounter, representing the temporal sequence of each Patient’s encounters.

`{iterateList: false}` This is the optional configuration map for the apoc.periodic.iterate procedure. In this case, setting iterateList to false indicates that the procedure should process the entire set of matched nodes (Patient) rather than iterating over a list of results.

### Example Using apoc.nodes.link()
An alternative approach for creating temporal relationships is to use `apoc.nodes.link()`. To do so, you create an ordered list of nodes and then pass it to the function with the specified relationship type. An example with explanations is below.

#### Full, Uncommented Code

```
// Create a list of events with timestamp properties
CREATE (e1:Event {timestamp: datetime("2023-01-01T00:00:00"), description: "Event 1"})
CREATE (e2:Event {timestamp: datetime("2023-01-02T00:00:00"), description: "Event 2"})
CREATE (e3:Event {timestamp: datetime("2023-01-04T00:00:00"), description: "Event 3"})

// Order and create temporal relationships
MATCH (e:Event)
WITH e
ORDER BY e.timestamp
WITH collect(e) AS orderedEvents
CALL apoc.nodes.link(orderedEvents, 'NEXT')
RETURN count(*) as relationshipsCreated
```

#### Line-by-Line Code Explanation
`MATCH (e:Event)` Match all nodes with the label Event.

`WITH e ORDER BY e.timestamp` Order the events based on the timestamp property.

`WITH collect(e) AS orderedEvents` Collect the ordered events into a list called orderedEvents.

`CALL apoc.nodes.link(orderedEvents, 'NEXT')` Call the apoc.nodes.link() procedure with the orderedEvents list and the relationship type 'NEXT'. This creates a :NEXT relationship between each consecutive pair of events in the list.

`RETURN count(*) as relationshipsCreated` Return the number of relationships created.

### Traversing Temporal Relationships
You can use Cypher traversals to view the paths of specific nodes or to create Perspectives in Neo4j Bloom. An example of querying the full path of a specific Patent would be:

```
// Match a specific Patient node by a unique id property
MATCH (p:Patient {patient_id: 'unique_patient_id'})

// Traverse from the Patient node to the initial Event node
MATCH (p)-[:NEXT_EVENT]->(startEvent:Event)

// Traverse the sequence of Event nodes connected by :NEXT_EVENT relationships
MATCH path = (startEvent)-[:NEXT_EVENT*]->(e:Event)

// Return the Patient and all encountered Event nodes in the order they occurred
RETURN p AS patient, nodes(path) AS encountered_events
```

## Additional Resources for Temporal Graph Data Models
Documentation: [Creating Data](https://neo4j.com/labs/apoc/4.0/graph-updates/data-creation/), which includes a demonstration video and a section on creating data via linked lists.



