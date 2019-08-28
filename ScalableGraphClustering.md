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

##### 2.1.1 Top-down method: 
* *Gurvan-Newman algorithm* is a divisible clustering algorithm based on *betweenness centrality*. It identifies the edge with high betweenness centrality and remove them so as to bisecting a graph into clusters. 
#### 2.2 betweenness centrality
 * the proportion of shortest paths between any nodes that pass through the edge.  (social butterfly)
 * $cp(e,i,j)$: the number of shortest paths between node i and j that pass through edge $e$
 * $sp(i,j)$: the total number of shortest paths between node $i$ and $j$
 * between centrality
  $$B(e)=\frac{cp(e,i,j)}{sp(i,j)}$$
##### 2.2.1 Bottom-up method 
  * NP-hard complexity **??**
  * to reduce the computional cost of Newman clustering, a greedy modularity-based algorithm called $CNM$ was proposed.$CNM$ uses *modularity gain*, which is obtained after merging a pair of nodes, and nested heap structures of modularity gain for all pairs of nodes.  
  However, $CNM$ does not work in reasonable comptational time for graphs with more than 500,000 nodes and also has a tendency to produce super-clusters, which contains large fractions of nodes with significant modularity. 
* $BGLL$, in contrast to $CNM$, computes the modularity gain only for theadjoined nodes pairs for local optimization.  
<u>effective</u> for extracting high modularity clusters  
<u>difficult</u> to realize quick responses for graphs of unprecedented(空前的，前所未有的) size: Web graphs with few billion edges. size, such as Web graphs with few billion edges. This is because it iteratively scans all nodes and edges as long as the modularity is incrementing. It is known that BGLL has near-linear time complexity for the size of edges.
* Shiokawa's solutioin:  
  *incremental aggregation algorithm*  
  to be effective, they use three techs: incremental aggretion, incremental pruning, efficient nodes ordering.
#### 2.3 to summerize
  * modularity-based method
    * handle large-scale graphs
    * widely used
    * cannot identify hubs and outliers in graphs. 
    * not a scale-invariant measure
    * difficult to find small clusters in large-scale graphs
    * fail to fully reproduce the ground-truth which is known as the *resolution limit* of modularity.
### 3. Structual Similarity Based Algorithms
  * resolution limit of modularity
  * main ideas: if adjacent nodes are densely connected to each other, they should be assigned to the same cluster. They can also find special role nodes, hubs and outliers which are not belong to any clusters to achieve mote accurate results.
  * to evaluate the density of adjacent nodes, *structual modularuty* is proposed: 
  $$\sigma(u,v)=\frac{|N[u]\bigcap N[v]|}{\sqrt{|N[u]||N[v]|}}$$
  where $N[u]=\{v \in V | (u,v) \in E\} \bigcup \{u\}$
  * main task is to find all clusters, hubs and outliers from the graph based on the equation above and user-specified parameters. 
#### 3.1 Top-down method
  * *DHSSACN*:  
      iteratively removes edges between nodes in the ascending order of the structual similarity of the node. The graph is divided into disconnected components by the removal of edges.   
      it produces a dendrogram(树状) showing the hierarchial structure of the clusters.
  * requires high computaional cost for clustering because it iteratively computes the structual similarity for all edges.
  * diffcult to apply DHSCAN to large scale graphs.  
#### 3.2 Structual Similairty-Based Modilairty $Q_s$
  * $IS_u$: the sum of the structual similarity of the nodes within cluster $C_u$
  * $DS_u$: the sum of the structual similarity between nodes in cluster $C_u$ and any nodes in the graph
  * $TS$: the total structual similarity of any adjacent nodes in the graph
  * the structual similarity based modularity score of the clustering result: 
  $$Q_s = \sum_{C_u\in C}\{\frac{IS_u}{TS}-(\frac{DS_u}{TS})^2\}$$ 
  the quality of $Q_s$ is better than using modularity based algorithms.

#### 3.3 Bottom-up method
* to be more effective without losing quality
* *SCAN*, the most popular method based on the structual similarity, is an extension of a traditional density-based clustering method, *DBSCAN*. This algorithm can find clusters as well as hubs and outliers in a graph by specifying two parameters $\epsilon$ and $\mu$. For finding clusters, SCAN first extracts seeds of clusters called *cores* from the graph.
#### 3.4 Cores
* $N_{\epsilon}[u]=\{v \in V | \sigma(u,v)\ge\epsilon\}$, $\epsilon$ and $\mu$ be the user-specified parameters
* A node $u$ is a core only if $|N_{\epsilon}[u]|\ge\mu$
* Once SCAN determines node u as a core, it assigns all nodes in $N_\epsilon[u]$ to the same cluster of a core. 
* parameter-free methods *SHRINK* and *gSkeletonClu*. SHRINK shares the advantages of structual similarity and modularity-based methods. They are user-friendly algorithm since they don't require user-specified parameters. And the clustering quality of them is better than that of SCAN
* LinkSCAN uses SCAN to find overlapping communities from graphs.
  * one of the most effective methods for structual similarity-based clustering.
  * degrades the quality of clustering results
* SCAN++
  * SCAN++ is based on a property of real-worldgraphs:if node u is two hops away from node v,their structural  neighborhoods,  N[u]and N[v],are likely to share large portion of nodes.
  * new data structure: DTAR
## Key Applications
### Information Networks
  * market analysis
  * infrastructure management
  * network topology design
  * wireless and hoc networks
  * sensor networks
### Social Networks
  * social trends
  * social behavior
  * information diffusion process
### Biological Networks
  * transcriptional modules
  * protein complexes
  * gene functional groups
  * signaling pathways
## Future Directions
  * structual similarity based method now requires high computational cost and is still difficult to find clusters to find cluster from billion-nodes graphs. So efficient clustering methods for structural similarity-based algorithms is one of the important future works.