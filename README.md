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



