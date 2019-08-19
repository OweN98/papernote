# Scalable Graph Clustering
https://link.springer.com/referenceworkentry/10.1007/978-1-4614-7163-9_110185-1
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

### 2. ***Modularity-Based Algorithms***
* overcome the drawback of edge-cut-based algorithms: no gurantee of the optimality of the clustering result.
* to achieve multiway partitioning, we have to specify the number of clusters that we like to extract from graphs.
* *modularity* indicates the degree of differance of graph from its random graphs. 
#### 2.1 Modularuty Q:
* $e_{uv}$: the number of edges between cluster $C_u$ and $C_v$;  
* $a_u$: the number of edges that are attaches to the nodes in cluster $C_u$;  
* $m$ = $|E|$;
* the modularity score of the clustering result: 
$$Q=\sum_{C_u\in{C}}\{{\frac{e_{uu}}{2m}-(\frac{a_u}{2m})^2}\}$$
* $\frac{a_u}{2m}$ indicates the expected fraction of edges of cluster $C_u$ that can be obtained when we assume the graph to be a random graph.
* well-clustered graphs have better modularity since the value of $e_{uu}$ is larger than the random graph.
* the main task is to find groups of nodes that maximizes $Q$.

#### 2.2 Top-down method: 
* *Gurvan-Newman algorithm* is a divisible clustering algorithm based on *betweenness centrality*. It identifies the edge with high betweenness centrality and remove them so as to bisecting a graph into clusters. 
* betweenness centrality of an edge: the proportion of shortest paths between any nodes that pass through the edge.  (soci al butterfly)