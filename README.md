# pathdb-eval1
Experimental evaluation of PathDB versus baselines and other database systems.

This repository contains information about an experimental evaluation of PathDB.
First, we compare PathDB with traversal-based baseline algorithms in order to isolate the impact of the proposed path algebra on query evaluation. Second, we compare PathDB with representative graph database systems that support recursive path queries, allowing us to examine both the expressiveness and the practical performance of the proposed approach in realistic scenarios.

## Experimental Setup

### Dataset
As evaluation dataset, we used a property graph produced with SNB Datagen, the data generator included in the LDBC Social Network Benchmark (SNB).
The SNB Datagen produces synthetic yet realistic social network graphs characterized by heterogeneous node and edge types, skewed degree distributions, and a rich set of structural patterns.
A graphical representation of the graph schema is available here.
The evaluation dataset was generated using scale factor 1 (SF1), resulting in a graph containing 3,181,724 nodes and 17,256,038 edges.
Considering that PathDB just support the Property Graph Data Format (PGDF) for data loading, the CSV files produced by the SNB Datagen were transformed into PGDF files ([nodes.pgdf](https://drive.google.com/file/d/114q_XijFHe1wJKNfyZzvE-PwnkSnoTfx/view?usp=sharing) and [edges.pgdf](https://drive.google.com/file/d/1MMBvpoU5PsVOw7Rg8FtaLVisvLAIhIv8/view?usp=sharing)). When loaded into main memory by PathDB, the graph required approximately 5.73 GB of RAM.

### Query Workload
The query workload used in our experimental evaluation was designed to systematically exercise different constructs of regular path queries. In particular, the workload focuses on patterns derived from the fundamental operators of regular expressions, such as concatenation, disjunction, recursion, and optionality, which are commonly used when expressing recursive path queries.

To structure the workload generation process, we distinguish three levels of query abstraction: Abstract Queries, Template Queries, and Real Queries. An Abstract Query is essentially a pattern that represents a specific operation within regular expressions (e.g. A+). A Template Query is obtained by replacing the variables in an abstract query with valid edge labels from the graph schema (e.g. Knows+). A Real Query is a concrete query that can be executed on a graph database system in its native query language. For example, a real query in the PathDB query language will be: MATCH TRAIL p = (x)-[(knows)+]-(y)} WHERE x.id = "p3378" RETURN p.

The idea was to create a diverse set of abstract queries by combining all the operators supported in a regular expression, resulting in 26 abstract queries (see table below). For each abstract query, we defined between 2 and 6 template queries according to the edge labels of the test graph. Each template query was then instantiated into a real query by selecting a source node whose out-degree, with respect to the corresponding adjacent label, was located at the median of the distribution. The query workload resulted in a total of 142 real queries which are listed in the file Queries.xlsx.

<img width="594" height="626" alt="queries" src="https://github.com/user-attachments/assets/57c477ed-5e87-4271-b53e-37012e18559b" />

### Evaluation Metrics
For each real query, we measured the query execution time, defined as the elapsed time between query submission and the production of the final result set. Each query was executed three times, and the reported execution time corresponds to the average over these runs.

To ensure comparability across systems and avoid excessive memory consumption, query results were limited to at most 100 paths. A timeout threshold of 120 seconds was applied to each query execution. These limits were consistently enforced across all experiments, including baseline algorithms and commercial graph database systems.

### Hardware Specifications 
All experiments were conducted on a dedicated server with the following configuration:
* CPU: x86-64 virtual CPU (QEMU/KVM), x86-64-v2 with AES, running on Proxmox VE
* Memory: 16 GB DDR4
* Storage: SSD
* Operating System: Ubuntu 22.04
* Java Virtual Machine: OpenJDK 21.0.9

## Experiment 1: Comparison with traversal baselines
This experiment compares PathDB with two traversal-based baseline algorithms.
The baseline algorithm for evaluating regular path queries is automata-based graph traversal, often called the product-graph algorithm.
We implemented two variants of the automata-based approach: DFS+A and BFS+A, which differ in the search strategy used to explore the product graph. DFS+A performs a depth-first traversal, while BFS+A uses breadth-first search.
Both baselines were designed to support the same class of regular path queries as PathDB and enforce identical path restrictors (Walk, Trail, Simple, and Acyclic). No indexing or pruning techniques beyond those strictly required by the restrictors were applied, ensuring that the comparison focuses exclusively on the evaluation strategy rather than optimizations.

