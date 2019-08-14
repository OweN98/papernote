# Scalable Graph Clustering
## Background
* Graph Clustering problem is traditionally related to the graph partitioning problem. The goal is to find clusters that minimize the numbers of edges that bridge clusters. But it has drawbacks that there is no guarantee of the optimality of the partitioning result.
* So it comes:
  1. ***modularity***
      * a measure of graph clustering quality. It measures the difference of a cluster structure from an expected random graph and try to extract clusters that maximize the score of the modularity.
      * simple & effective for complex graph
      * difficult to find small clusters hidden in large-scale graphs
      * fail to fully reproduce the ground-truth

  2. ***structual graph clustering***
      * extract densely connected subgraphs as clusters
      * find densely connected clusters, hubs and outliers to achieve more accurate results
  
## Clustering Algorithms

* three types: *edge-cut based algorithms* & *modularity-based algorithms* & *structual similarity based algorithms*. 
### 1. ***edge-cut based algorithms***
* graph glustering problem is related to the minimum cut & graph partitioning problem.
    #### Top-down method
    ##### two-way minimum-cut problem: 
    * minimize the numbers of the edges across the clusters.
  * ***min-max cut method***: 
    * minimize the number of edges between $C_u$ and $C_v$
    * maximize the number of edges within each of them
    * a cut is evaluated: edges that connect the nodes in $C_u$ and $C_v$
    * drawback: 
      * local optimum
  * ***normalized cut method***:
    * to overcome the drawback 
    * an extension that normalizes the total number edges between each cluster to the rest of the graph.
  * However 
    * no guarantee for the optimality of the recursive clustering results
    * no indicator to stop the bisection procedure
  * ***Kerninghan-Lin algorithm***:
    * traditonal tech for multiway graph partitioning
    * based on hill climbing tech for optimizing graph partitioning
    * local optimum, not global
    * not suitable for large-scale graphs
  #### Bottom-up method --- to acheive scalable multiway partitioning
  * MLP = multilevel label propagation
    * use multilevel label propagation to iteratively coarsen a graph until the coarsened graph is small enough