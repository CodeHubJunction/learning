# Elastic Search

## What is Apache Lucene?

Imagine you have a huge library with millions of books, and you want to quickly find all the books that mention the word "volcano." Searching through every single book page-by-page would take forever, right? Apache Lucene is a technology that helps computers quickly search through massive amounts of text, like that library, and find exactly what you're looking for.

One of the most powerful tools Lucene uses to do this is called an inverted index.
## What is an Inverted Index?

Let’s understand this concept by comparing it to something familiar:

> In a regular book index (like the one at the end of your textbooks), you look up a topic, and it tells you the pages where that topic is mentioned. An inverted index does the opposite: it takes all the words from a collection of documents and lists which documents each word appears in. So instead of saying "Page 5 talks about photosynthesis," an inverted index says, "The word 'photosynthesis' appears in Documents 2, 7, and 10."

### How an Inverted Index Works (Simple Example)

Imagine you have three small documents:

- Document 1: "I love pizza"
- Document 2: "Pizza is delicious"
- Document 3: "I love ice cream"

### Step 1: Breaking Down the Text

First, Lucene breaks down each document into individual words (we call this tokenizing). Here’s what it finds:

- Document 1: "I", "love", "pizza"
- Document 2: "Pizza", "is", "delicious"
- Document 3: "I", "love", "ice", "cream"

### Step 2: Building the Inverted Index

Next, Lucene builds an inverted index:
| Word | Documents |
| ------ | ------ |
| "I"	 | Document 1, Document 3|
|"love"	| Document 1, Document 3|
|"pizza"|	Document 1, Document 2|
|"is"|	Document 2|
|"delicious"|	Document 2|
|"ice"|	Document 3|
|"cream"|	Document 3|

So, if you search for the word "pizza," Lucene can quickly look at its index and tell you that "pizza" appears in Document 1 and Document 2. Because the inverted index is already built, searching for a word like "pizza" is super fast. Lucene doesn't have to go through all the documents again—it just checks the index to see which documents contain that word.

## Why is an Inverted Index Useful?

Speed: Searching with an inverted index is much faster than going through all the text line-by-line. Efficiency: It uses less memory and makes it easy to handle huge amounts of data. Relevance: Lucene also ranks results based on how often and where words appear in documents, so you get the most relevant results first.

## Performance Bottlenecks with a Single Lucene Instance:

- If you rely on a single Lucene instance (i.e., a single shard), it can become a bottleneck as the dataset grows. This is because that single instance would have to handle all indexing, searching, and data retrieval, which can slow down performance and potentially lead to errors due to resource exhaustion. 

- For large datasets, using just one shard means there is a limit to how much data can be efficiently indexed and searched. At some point, the performance will degrade.

## Determining Limits Through Testing:

- The threshold at which a single Lucene instance becomes inefficient isn't something that can be universally determined with a formula because it depends on multiple factors:
    - The amount of data being indexed.
    - The complexity of the search queries.
    - The resources (CPU, memory, disk I/O) available on the server.
    
- Thus, finding the optimal configuration requires empirical testing and tuning based on your specific use case.




## Enhancing Search Capabilities with Multiple Lucene Instances:

- To improve search performance and scalability, Elasticsearch allows you to use multiple Lucene instances, which are referred to as shards.

- By splitting your data across multiple Lucene instances (shards), you can double the search and indexing capabilities since the workload is distributed.

### How Multiple Lucene Instances (Shards) Work:

When you increase the number of shards, Elasticsearch will:
- Distribute your data across these multiple Lucene instances.
- Parallelize search queries by having each shard process its portion of the data independently.

For example, if you now have two Lucene instances (shards), the search workload is split between the two:
- Each shard contains a subset of your data.
- When a search query is executed, Elasticsearch sends the query to both shards simultaneously, allowing them to process it in parallel.
- The results are then merged to provide a complete response.

### Benefits of Using Multiple Lucene Instances:

Improved Search Speed: By distributing the data and search workload across multiple Lucene instance.

## Scaling Lucene Instances:

We should be able to add multiple lucene instances easily  and it should aid in 
- Storing Data Across Shards :
    - uses a routing algorithm to decide which shard a document should go to.
    - ensures that data is evenly distributed across the available Lucene instances.

- Fault Tolerance: 
    - Data stored across multiple shards can be replicated (using replicas), which improves availability in case one Lucene instance fails.

To managing and co-ordinanting, indexing are the begiing of elastic search node. It is responsible for storing data and handling search and indexing requests.

## ElasticSearch Nodes or OpenSearch Nodes:
- The node manages multiple Lucene instances, which in Elasticsearch terminology are called shards.
- The node acts as the central controller that manages these shards and coordinates their activities, such as indexing documents, executing searches, and retrieving results
-  it serves as the new search interface that wraps and extends Lucene's capabilities.
-  Additional functionalities
    - Distributing queries across shards.
    - Merging search results from different shards.
    - Providing cluster management features like shard allocation, replication, and fault tolerance.
- How Nodes and Shards Work Together:
    - When you send a search request to a node, it acts as the coordinator.    
    - It forwards the query to all relevant shards.
    - Each shard processes its portion of the data and returns results to the node.
    - The node aggregates the results from all shards and returns the final output to the user.

## Shards as the Scaling Unit:

In Elasticsearch, shards are the fundamental scaling units for your data. By dividing an index into multiple shards, Elasticsearch allows:
- Parallel processing of searches and indexing.
- Distribution of data across multiple nodes in a cluster.
- Horizontal scalability, meaning you can handle more data and queries by simply adding more nodes (each managing its own set of shards).
    
For example, if your index is split into 5 shards, each shard is a Lucene instance managed by the node, and the data is distributed among them. This improves performance by enabling Elasticsearch to process multiple queries simultaneously.

## Cluster:

image

Will be fault tolerant, scaleable resilient, 

Elastic search is also used to view log entries, So fir this we will have seperte cluster as the requirements are different from the product search. 

The data growth in log and product search are differnet.  Also Esl will then exactly know which shareds to query if we are searching for only survey responses or only for log enteries. 

Elasticsearch is a versatile search engine commonly used for a variety of purposes, such as:

- Product Search: Searching through product catalogs on an e-commerce site.
- Log Analysis: Searching through logs generated by applications, servers, or network devices to monitor system performance and diagnose issues.

Why Separate Clusters for Different Use Cases?:
It is often beneficial to set up separate Elasticsearch clusters for different use cases like product search and log analysis because:
 - Data Growth: The rate at which data grows can vary significantly between logs and product data.
    - Logs tend to grow rapidly as they accumulate continuously from various systems.
    - Product data may change less frequently and have a relatively stable size.
- Search Requirements: The way you search and query logs is different from how you search for products.
    - Log searches may involve time-based filters and focus on full-text search to identify errors or patterns.
    - Product searches may prioritize structured searches like filtering by categories, prices, or attributes.

## Understand of Index, Mapping, Documetn

- Index
    - An **index** in Elasticsearch is similar to a **database** in traditional systems. It is a logical container that stores related documents.
    -   Each index is given a name (like products, logs, etc.), and it contains **documents** (which are like rows in a database) organized in **shards**.
- Mapping
    - A mapping defines the structure of the documents in an index. It's like a schema in a database.
    - The mapping specifies the **field names** (like name, price, timestamp) and their **data types** (like string, integer, date).
    - All documents stored in the same index follow the same mapping, ensuring that fields are consistently structured.

## Where Are the Shards Stored: In Index or Index in Shards?

Shards are contained within an index.
- When you create an index in Elasticsearch, it is automatically divided into shards.
- Think of an index as a logical container, and shards are the physical partitions of that index.
- Each shard is essentially a Lucene instance that stores a portion of the index's data and handles indexing and searching for that subset.

How it works:
- Let's say you create an index called logs with 5 shards.
- The logs index will be split into 5 shards, with each shard holding a portion of the documents in that index.
- When you run a search query on the logs index, Elasticsearch will distribute the query across all 5 shards, gather the results, and combine them into the final output.

## Index as Database: What is the "Table" Equivalent?

In Elasticsearch, the analogy to a relational database can be broken down like this:

| Relational Database	 | Elasticsearch Equivalent |
| ------ | ------ |
| Database	 | Index|
| Table	 | Type (deprecated)|
| Row	 | Document|
| Column	 | Field|

### Why was type feature removed?

- The types concept caused confusion and led to potential conflicts because different types in the same index could have fields with the same name but different data types.
- Now, each index can only have one mapping. This means one index = one mapping, and all documents in that index must follow the same mapping.


## Interacting with Elasticsearch to Store a Document:

- When you store a document in Elasticsearch, you interact with an Elasticsearch node at the index level. This means:
    - You are sending your request to an index (e.g., products, logs, etc.) to store a specific document.
- Elasticsearch automatically routes the document to a primary shard within that index.
    - Each document is routed to a specific shard based on its document ID using a hashing algorithm.

## Replication for Fault Tolerance:

- Once the document is stored on the primary shard, Elasticsearch automatically replicates the document to one or more replica shards (if replicas are configured).
- This means that:
    - The document is stored on both the primary shard and its replica shards.
    - This replication ensures fault tolerance: if the node holding the primary shard goes down, the replica shard can still serve requests.

## Searching for Documents:

- When you perform a search query on an index, Elasticsearch searches all the shards (both primary and replicas) within that index.
- The query is distributed to all relevant shards, and each shard processes its portion of the data independently.
- The results are then combined and returned to the user.

# Key Benefits of This Distributed Approach:

- Distributed Document Store: Elasticsearch distributes data across multiple shards, allowing it to scale horizontally (i.e., by adding more nodes).
- Fault Tolerance: By storing data on both primary and replica shards, Elasticsearch ensures high availability even if some nodes fail.
- Support for Multiple Datasets: Elasticsearch can handle multiple indices (each with its own set of shards), making it capable of storing and searching across different types of data (e.g., logs, product catalogs, etc.).


