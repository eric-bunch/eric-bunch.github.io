---
layout: post
title: "Spectral Clustering"
date: 2018-06-01
blurb: In this post we will investigate spectral clustering, which uses the eigenvalue decomposition of a data set's Laplacian matrix. We will look into the eigengap heuristic, which give guidelines on how many clusters to choose, as well as an example using breast cancer proteome data.
author: Eric Bunch
---

# Introduction

Spectral clustering is a way to cluster data that has a number of benefits and applications. It relies on the *eigenvalue decomposition* of a matrix, which is a useful factorization theorem in matrix theory. We will look into the eigengap heuristic, which give guidelines on how many clusters to choose, as well as an example using breast cancer proteome data.

### Graph Clustering Method

Spectral clustering should really be viewed as a graph clustering algorithm, in the sense that a data clustering problem is first translated to a graph clustering problem, graph clusters are found, and then translated back to the data clustering problem. Spectral clustering gives a way of grouping together nodes in a graph that are similarly connected.

<!-- (give example of medium-sized graph) -->

### Pros & Cons

Here is a short list of some pros and cons of spectral clustering compared to other clustering methods.

**Pros**
- Clusters not assumed to be any certain shape/distribution, in contrast to e.g. k-means. This means the spectral clustering algorithm can perform well with a wide variety of shapes of data.
- Do not necessarily need the actual data set, just similarity/distance matrix, or even just Laplacian
  - Because of this, we can cluster one dimensional data as a result of this; other algos that can do this are k-medoids and heirarchical clustering.

**Cons**
- Need to choose the number of clusters $k$, although there is a heuristic to help choose
- Can be costly to compute, although there are algorithms and frameworks to help

Speak here about importance of assumptions of kmeans clustering.

## Eigenvalue Decomposition



## Adjacency Matrix and Laplacian

In order to formulate the spectral clustering algorithm, we need to translate the data into a graph representation. Our aim will be to form the *Laplacian matrix* of the graph, and then perform spectral clustering on that. To do this we need a few objects:

- Graph representation of the data
- Degree matrix of the graph
- Adjacency matrix of the graph

### Representing the Data as a Graph

There are a number of meaningful ways to represent a data set as a graph, and the most appropriate one to choose will of course depend on the particular data set and problem being addressed. We'll outline a few standard methods here to get us started.

#### Similarity Graphs

In the following three similarity graphs, the set of nodes of the graph is defined to be the set of data points in the data set; denote them by $\{v_1,...,v_n \}$.

- **$\epsilon$-neighborhood Graph**
Here real number $\epsilon$ is fixed, and all nodes that are less than $\epsilon$ in distance apart are connected with an edge. This graph is typically considered as an unweighted graph.

- **$k$-nearest Neighbor Graph**
Here we place an edge between $v_i$ and $v_j$ if $v_j$ is among the $k$ nearest neighbors of $v_i$ *or* if $v_i$ is among the $k$ nearest neighbors of $v_j$.

- **Mutual $k$-nearest neighbor graph**
Here we place an edge between $v_i$ and $v_j$ if $v_j$ is among the $k$ nearest neighbors of $v_i$ *and* if $v_i$ is among the $k$ nearest neighbors of $v_j$.

### Degree, Adjacency, and Laplacian Matrices of the Graph

For these definitions, we'll think in general terms of a graph, and then come back to our special case. So let $G = (V, E)$ be an undirected graph with vertex set $V = {v_1,...,v_n}$.

#### Degree Matrix

The degree matrix $D$ of a graph $G = (V, E)$ is the $\mid V\mid \times \mid V\mid$ matrix defined by

$$
D_{i,j} :=
\begin{cases}
      deg(v_i) & \text{if} \hspace{5pt} i = j \\
      0 & \text{otherwise}
   \end{cases}
$$

where $deg(v_i)$ of a vertex $v_i \in V$ is the number of edges that terminate at $v_i$.

#### Adjacency Matrix

The adjacency matrix $A$ of a *simple* graph--no edges starting and ending at the same vertex--$G = (V, E)$ the adjacency matrix is the $\mid V\mid \times \mid V\mid$ matrix defined by

$$
A_{i,j} :=
\begin{cases}
      1 & \text{if there is an edge between } v_i \text{ and } v_j \\
      0 & \text{otherwise}
   \end{cases}
$$

Notice that since we assumed $G$ was a simple graph, the diagonal entries of $A$ are all zero.

#### Lapacian Matrix
 The Laplacian matrix $L$ of a simple graph $G$ is defined as

$$
L = D - A.
$$

Stated differently,

$$
L_{i, j} =
\begin{cases}
      deg(v_i) & \text{if  } i = j \\
      -1 & \text{if } i eq j \text{ there is an edge between } v_i \text{ and } v_j\\
      0 & \text{otherwise}
   \end{cases}
$$

##### Normalized Laplacian

The *normalized* Laplacian matrix $L_{norm}$ of a simple graph $G$ is defined to be

$$
L_{norm} := D^{-1}L = I - D^{-1}W
$$

## Algorithm

Input: Similarity matrix $S \in \mathbb{R}^{n×n}$, number $k$ of clusters to construct.
- Construct a similarity graph by one of the ways described above. Let $W$ be its weighted adjacency matrix.
- Compute the normalized Laplacian $L_{norm}$.
- Compute the first $k$ eigenvectors $u_1,..., u_k$ of $L_{norm}$.
- Let $U \in \mathbb{R}^{n×k}$ be the matrix containing the vectors $u_1,..., u_k$ as columns.
- For $\{i = 1,..., n\}$, let $y_i \in \mathbb{R}^k$ be the vector corresponding to the $i^{th}$ row of $U$.
- Cluster the points $(y_i)_{i=1,...,n} $ in <!-- a_ --> $\mathbb{R}^k$ with the $k$-means algorithm into clusters
$C_1,..., C_k$.
Output: Clusters $A_1,..., A_k$ with $A_i = \{j| y_j ∈ C_i\}$.


## Example

In this section we will use spectral clustering on a manufactured data set from `Scikit-learn`; the noisy moons:

<center>
<img src="/images/noisy_moons.png" style="width: 50%; margin-top: 30px; margin-bottom: 30px;">
</center>

I chose to generate this data set to have 120 points, and a noise factor of `0.05`. Below is a representation of the epsilon graph of this data set with $\epsilon = 0.1$.


<div id="noisy_moons_graph"></div>
<style>
.node {stroke: #fff; stroke-width: 1.5px;}
.link {stroke: #999; stroke-opacity: .6;}
</style>

As we can see from the plot of the data as well as the graph visualization, there are two distinct clusters represented. We then compute the normalized graph Laplacian matrix $L_{norm}$, and find the eigenvalue decomposition it; that is, we find an invertible matrix $U$ and diagonal matrix $D$ where

$$
L_{norm} = UDU^{-1}
$$

Since we are able to see the data, we can "cheat" and say we want to cluster the data into two clusters. In general, it is not possible to look at data and tell so easily how many clusters we should have. But there is a heuristic to help us choose the number of clusters, which we will learn about in the next section.

Since we have already determined that we are looking for two clusters, we take the two columns, ${\bf u_1}$ and ${\bf u_2}$, of $U$ corresponding to the two smallest eigenvalues of $L_{norm}$. Then we use $k$-means clustering with $k=2$ to obtain two clusters within the reduced data set $[{\bf u_1}, {\bf u_2}]$. We then "pull back" the cluster labels from $[{\bf u_1}, {\bf u_2}]$ to $U$, to $L_{norm}$, and finally to the data set. Labeling the clusters with different colors, we can see the graph depiction is colored as expected


<div id="noisy_moons_graph_clusters"></div>
<style>
.node {stroke: #fff; stroke-width: 1.5px;}
.link {stroke: #999; stroke-opacity: .6;}
</style>

And if we plot the original data set with the clusters marked with different colors, we get an expected picture.

<center>
<img src="/images/noisy_moons_clusters.png" style="width: 50%; margin-top: 30px; margin-bottom: 30px;">
</center>



## How to Choose $k$: Eigengap Heuristic

With spectral clustering, there is a way to choose the number of clusters $k$ (of course it is not a hard rule, but a *heuristic* to help inform you). If we let $\lambda_1,...,\lambda_n$ be the eigenvalues of the Laplacian, the goal is to choose $k$ such that $\lambda_1,...,\lambda_k$ are relatively small, but $\lambda_{k+1}$ is relatively large. Unfortunately, it does not seem so far like there is a simple, single-number metric that can be used to evaluate what number of clusters should be chosen.

### Example: Breast Cancer Proteomes

In this example we look at breast cancer [proteome](https://en.wikipedia.org/wiki/Proteome?oldformat=true) data. This data can be found on [Kaggle](https://www.kaggle.com/piotrgrabo/breastcancerproteomes), and contains the proteome profiles for 77 breast cancer samples. After some preprocessing, a sample of the data looks like this:

<div style="overflow-x: scroll;">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>NP_057427</th>
      <th>NP_002408</th>
      <th>NP_000415</th>
      <th>NP_000413</th>
      <th>NP_000517</th>
      <th>NP_004439</th>
      <th>NP_005219</th>
      <th>NP_058519</th>
      <th>NP_058518</th>
      <th>NP_001116539</th>
      <th>NP_061155</th>
      <th>NP_001035932</th>
      <th>NP_077006</th>
      <th>NP_000917</th>
      <th>NP_065178</th>
      <th>NP_006836</th>
      <th>NP_006614</th>
      <th>NP_001784</th>
      <th>NP_006092</th>
      <th>NP_001153651</th>
      <th>NP_001159403</th>
      <th>NP_000116</th>
      <th>NP_004314</th>
      <th>NP_060601</th>
      <th>NP_005931</th>
      <th>NP_003003</th>
      <th>NP_113611</th>
      <th>NP_002002</th>
      <th>NP_004487</th>
      <th>NP_008950</th>
      <th>NP_114172</th>
      <th>NP_001062</th>
      <th>NP_001444</th>
      <th>NP_057547</th>
      <th>NP_054895</th>
      <th>NP_001246</th>
      <th>NP_055606</th>
      <th>NP_036451</th>
      <th>NP_000624</th>
      <th>NP_569082</th>
      <th>NP_001159</th>
      <th>NP_001229</th>
      <th>NP_002458</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>TCGA-A2-A0CM</th>
      <td>2.160157</td>
      <td>2.623021</td>
      <td>4.768355</td>
      <td>0.639321</td>
      <td>4.933663</td>
      <td>-4.419112</td>
      <td>-0.271711</td>
      <td>-6.013418</td>
      <td>-6.013418</td>
      <td>-6.318320</td>
      <td>2.277710</td>
      <td>-4.029719</td>
      <td>-4.029719</td>
      <td>-6.509343</td>
      <td>1.906685</td>
      <td>2.215260</td>
      <td>-1.601524</td>
      <td>1.631171</td>
      <td>2.013217</td>
      <td>-6.395464</td>
      <td>0.584219</td>
      <td>-4.937078</td>
      <td>-0.433346</td>
      <td>3.126292</td>
      <td>-1.182743</td>
      <td>0.286664</td>
      <td>0.980958</td>
      <td>1.509945</td>
      <td>-4.433806</td>
      <td>2.549550</td>
      <td>2.733226</td>
      <td>0.896468</td>
      <td>1.576068</td>
      <td>-1.292949</td>
      <td>3.541400</td>
      <td>3.177722</td>
      <td>0.001309</td>
      <td>-1.792547</td>
      <td>-0.830502</td>
      <td>-0.538113</td>
      <td>2.516489</td>
      <td>2.556897</td>
      <td>-2.035927</td>
    </tr>
    <tr>
      <th>TCGA-A2-A0D2</th>
      <td>2.249702</td>
      <td>3.576941</td>
      <td>2.169868</td>
      <td>2.968207</td>
      <td>0.543251</td>
      <td>-5.421010</td>
      <td>-1.206443</td>
      <td>-5.297932</td>
      <td>-5.277974</td>
      <td>-5.311238</td>
      <td>2.934943</td>
      <td>-5.567372</td>
      <td>-5.567372</td>
      <td>-5.287953</td>
      <td>1.198555</td>
      <td>3.264258</td>
      <td>2.449287</td>
      <td>2.332862</td>
      <td>3.606879</td>
      <td>-7.124134</td>
      <td>-0.853843</td>
      <td>-4.153646</td>
      <td>-2.417258</td>
      <td>-0.604362</td>
      <td>2.545753</td>
      <td>7.053044</td>
      <td>2.921637</td>
      <td>3.743262</td>
      <td>-6.269245</td>
      <td>2.376105</td>
      <td>2.781928</td>
      <td>6.836827</td>
      <td>2.898353</td>
      <td>-3.694601</td>
      <td>2.495856</td>
      <td>2.722053</td>
      <td>0.373604</td>
      <td>-1.342826</td>
      <td>-4.183584</td>
      <td>-2.889608</td>
      <td>3.487128</td>
      <td>1.029442</td>
      <td>-0.714133</td>
    </tr>
    <tr>
      <th>TCGA-A2-A0EQ</th>
      <td>-0.020957</td>
      <td>1.884936</td>
      <td>-7.407249</td>
      <td>-7.675146</td>
      <td>-5.187535</td>
      <td>-2.795601</td>
      <td>7.158672</td>
      <td>-9.114133</td>
      <td>-8.762041</td>
      <td>-9.573385</td>
      <td>-0.859091</td>
      <td>-1.241800</td>
      <td>-1.241800</td>
      <td>-7.047502</td>
      <td>1.961478</td>
      <td>0.863101</td>
      <td>1.842838</td>
      <td>-0.369223</td>
      <td>0.266075</td>
      <td>-3.055843</td>
      <td>-0.836128</td>
      <td>-6.308873</td>
      <td>-0.686872</td>
      <td>0.162743</td>
      <td>-2.791774</td>
      <td>-5.554936</td>
      <td>0.047930</td>
      <td>3.388984</td>
      <td>0.063239</td>
      <td>0.545453</td>
      <td>-0.273546</td>
      <td>1.460128</td>
      <td>-0.174083</td>
      <td>-1.410193</td>
      <td>0.702364</td>
      <td>-1.402538</td>
      <td>0.001309</td>
      <td>-0.210764</td>
      <td>1.934688</td>
      <td>-0.538113</td>
      <td>0.798041</td>
      <td>2.003576</td>
      <td>-2.035927</td>
    </tr>
    <tr>
      <th>TCGA-A2-A0EV</th>
      <td>-1.364604</td>
      <td>-2.246793</td>
      <td>-3.750716</td>
      <td>-3.882344</td>
      <td>-2.252395</td>
      <td>-3.252209</td>
      <td>-1.574649</td>
      <td>-2.190781</td>
      <td>-2.871327</td>
      <td>-2.190781</td>
      <td>-4.632905</td>
      <td>-2.608071</td>
      <td>-2.608071</td>
      <td>-1.266583</td>
      <td>1.158737</td>
      <td>-2.039549</td>
      <td>-1.160160</td>
      <td>-1.216172</td>
      <td>-1.185366</td>
      <td>0.466989</td>
      <td>-1.924724</td>
      <td>0.649028</td>
      <td>-0.488016</td>
      <td>-6.134027</td>
      <td>0.534203</td>
      <td>1.080321</td>
      <td>-0.709263</td>
      <td>-1.199369</td>
      <td>-1.723081</td>
      <td>-2.179579</td>
      <td>-3.311022</td>
      <td>0.139319</td>
      <td>0.733046</td>
      <td>0.018893</td>
      <td>-1.574649</td>
      <td>-4.515280</td>
      <td>0.001309</td>
      <td>-0.210764</td>
      <td>2.049328</td>
      <td>-0.538113</td>
      <td>-0.266769</td>
      <td>-3.201798</td>
      <td>-7.724769</td>
    </tr>
    <tr>
      <th>TCGA-A2-A0EX</th>
      <td>-2.506723</td>
      <td>-2.953194</td>
      <td>-0.803378</td>
      <td>-2.315378</td>
      <td>-0.098028</td>
      <td>-1.643795</td>
      <td>-1.212331</td>
      <td>4.186597</td>
      <td>3.976493</td>
      <td>3.942726</td>
      <td>-2.521730</td>
      <td>0.427232</td>
      <td>0.415977</td>
      <td>1.545287</td>
      <td>1.725376</td>
      <td>-1.812629</td>
      <td>-2.476708</td>
      <td>-0.923438</td>
      <td>-1.966455</td>
      <td>-2.037740</td>
      <td>-1.973959</td>
      <td>-2.409174</td>
      <td>0.115828</td>
      <td>-0.882167</td>
      <td>0.419729</td>
      <td>1.031282</td>
      <td>-1.666306</td>
      <td>-0.083021</td>
      <td>-0.281869</td>
      <td>-3.752341</td>
      <td>-3.317125</td>
      <td>-2.769353</td>
      <td>-0.174083</td>
      <td>-0.822137</td>
      <td>-2.938187</td>
      <td>-3.395914</td>
      <td>-1.827636</td>
      <td>0.082061</td>
      <td>0.044543</td>
      <td>-2.079011</td>
      <td>-3.046991</td>
      <td>2.554537</td>
      <td>-0.443199</td>
    </tr>
  </tbody>
</table>
</div>

We will go through this example twice: once with forming the epsilon graph from the data, and proceeding with spectral clustering, and another with forming the mutual nearest neighbors graph instead.

#### Epsilon Graph
Taking $\epsilon = 0.37$, we can form the epsilon graph from the data, and get the following graph

<div id="proteome_epsilon_graph"></div>
<style>
.node {stroke: #fff; stroke-width: 1.5px;}
.link {stroke: #999; stroke-opacity: .6;}
</style>


Next, we perform eigenvalue decomposition on the normalized Laplacian; $L_{norm} = UDU^{-1}$. We then plot the eigenvalues of $L_{norm}$ in increasing order



<center>
<img src="/images/proteome_epsilon_graph_eigenvalues_plot.png" style="width: 50%; margin-top: 30px; margin-bottom: 30px;">
</center>


Using the eigenmap heuristic, we wish to choose an $n$ such that the first $n$ eigenvalues are relatively small, and $\mid \lambda_{n+1} - \lambda_n\mid$ is relatively large. It's now up to our discretion what to choose, but looking at the eigenvalue plot as well as the network graph, I think it is reasonable to choose $n=4$. We then proceed with the spectral clustering algorithm: we choose the four eigenvectors in $U$ corresponding to the first four eigenvalues. Then we cluster on the lower dimensional data, and assign clusters to the original dataset accordingly. We show the graph representation of the data with clusters colored:

<div id="proteome_epsilon_graph_clusters"></div>
<style>
.node {stroke: #fff; stroke-width: 1.5px;}
.link {stroke: #999; stroke-opacity: .6;}
</style>


These are fairly decent clusterings, although it is a little "clumpy": there are two main tightly connected clusters and a lot of nodes that are more loosely connected.

#### Mutual Neighbors Graph

Next, we look at the same proteome data set, but this time we will form the mutual neighbors graph during the spectral clustering process. With a little experimentation, we can find a good number of neighbors: here I chose 9 neighbors for the following graph. Already, we can see that this graph does not have the same "clumpy" look as the epsilon graph, and looks to have better clusters.

<div id="nneighbors_graph"></div>
<style>
.node {stroke: #fff; stroke-width: 1.5px;}
.link {stroke: #999; stroke-opacity: .6;}
</style>



We perform the eigenvalue decomposition on the normalized Laplacian $L_{norm}$ and again plot the eigenvalues


<center>
<img src="/images/nneighbors_eigenvalues_plot.png" style="width: 50%; margin-top: 30px; margin-bottom: 30px;">
</center>


Using the eigengap heuristic, let's choose 5 clusters to cluster the data into. Then we proceed with the spectral clustering algorithm, and display the graph with the clusters colored.


<div id="nneighbors_graph_clusters"></div>
<style>
.node {stroke: #fff; stroke-width: 1.5px;}
.link {stroke: #999; stroke-opacity: .6;}
</style>


These clusters look much better than those from the epsilon graph.


## Conclusion

We have seen how spectral clustering works, and how it differs from other methods like $k$-means, as well as the eigengap heuristic and how it can be used to choose the number of clusters. The code to generate the examples in this post as can be found [here](https://github.com/eric-bunch/spectral_clustering_examples).



<script src="/scripts/d3_scripts_spectral_clustering.js">
</script>
