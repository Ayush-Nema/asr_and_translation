## Introduction
- Clustering is one of the main tasks in unsupervised machine learning. The goal is to assign unlabeled data to groups, where similar data points hopefully get assigned to the same group.
- Spectral clustering is a technique with roots in graph theory, where the approach is used to identify communities of nodes in a graph based on the edges connecting them. The method is flexible and allows us to cluster non graph data as well.
- Spectral clustering uses information from the eigenvalues (spectrum) of special matrices built from the graph or the data set.

## Key concepts
1. [[#Eigenvectors and Eigenvalues |Eigenvalues and Eigenvectors]]
2. [[#Graph]] 
3. [[#Adjacency matrix]]
4. [[#Degree matrix]]
5. [[#Graph Laplacian]]
6. [[#Eigenvalues of Graph Laplacian]]


## What is spectral clustering?
Clustering is one of the most widely used techniques for exploratory data analysis, with applications ranging from statistics, computer science, biology to social sciences or psychology. People attempt to get a first impression on their data by trying to identify groups of “similar behavior” in their data.

Spectral clustering is an EDA technique that reduces complex multidimensional datasets into clusters of similar data in rarer dimensions. The main outline is to cluster the all spectrum of unorganized data points into multiple groups based upon their uniqueness “Spectral clustering is one of the most popular forms of multivariate statistical analysis” ‘Spectral Clustering uses the connectivity approach to clustering’, wherein communities of nodes (i.e. data points) that are connected or immediately next to each other are identified in a graph. The nodes are then mapped to a low-dimensional space that can be easily segregated to form clusters. Spectral Clustering uses information from the eigenvalues (spectrum) of special matrices (i.e. Affinity Matrix, Degree Matrix and Laplacian Matrix) derived from the graph or the data set.

Spectral clustering methods are attractive, easy to implement, reasonably fast especially for sparse data sets up to several thousand. Spectral clustering treats the data clustering as a graph partitioning problem without making any assumption on the form of the data clusters.

## Difference between Spectral Clustering and Conventional Clustering Techniques

Spectral clustering is flexible and allows us to cluster non-graphical data as well. It makes no assumptions about the form of the clusters. Clustering techniques, like K-Means, assume that the points assigned to a cluster are spherical about the cluster centre. This is a strong assumption and may not always be relevant. In such cases, Spectral Clustering helps create more accurate clusters. It can correctly cluster observations that actually belong to the same cluster, but are farther off than observations in other clusters, due to dimension reduction.

The data points in Spectral Clustering should be connected, but may not necessarily have convex boundaries, as opposed to the conventional clustering techniques, where clustering is based on the compactness of data points. Although, it is computationally expensive for large datasets, since eigenvalues and eigenvectors need to be computed and clustering is performed on these vectors. Also,for large datasets, the complexity increases and accuracy decreases significantly.

## Eigenvectors and Eigenvalues
Eigenvectors are an important part of linear algebra, because they help describe the dynamics of systems represented by matrices. There are numerous applications which utilize eigenvectors, and we’ll use them directly here to perform spectral clustering.

To learn about it, refer below articles:
1. [Video: Eigen values and vectors - 3blue1brown](https://www.youtube.com/watch?v=PFDu9oVAE-g)
2. [PDF: Eigenvalues and Eigenvectors](https://math.mit.edu/~gs/linearalgebra/ila0601.pdf)
3. [Video: Introduction to eigenvalues and eigenvectors - Khan Academy](https://www.khanacademy.org/math/linear-algebra/alternate-bases/eigen-everything/v/linear-algebra-introduction-to-eigenvalues-and-eigenvectors)
4. [Article: Linear Algebra — Part 6: eigenvalues and eigenvectors - Medium](https://medium.com/sho-jp/linear-algebra-part-6-eigenvalues-and-eigenvectors-35365dc4365a)
5. [Article: Eigenvectors and Eigenvalues — All you need to know - Towards Data Science](https://towardsdatascience.com/eigenvectors-and-eigenvalues-all-you-need-to-know-df92780c591f)
## Graph
Let’s use the following undirected graph as a running example:
![[undirected_graph.png |400]]

This graph has 10 nodes and 12 edges. It also has two connected components {0,1,2,8,9} and {3,4,5,6,7}. A connected component is a maximal subgraph of nodes which all have paths to the rest of the nodes in the subgraph.
Connected components seem important, if our task is to assign these nodes to communities or clusters. A simple idea would be to make each connected component its own cluster. This seems reasonable for our example graph, but it’s possible that the entire graph might be connected, or that the connected components are very large. There could also be smaller structures within a connected component which are good candidates for communities. 

## Adjacency matrix
We can represent our example graph as an adjacency matrix, where the row and column indices represent the nodes, and the entries represent the absence or presence of an edge between the nodes. The adjacency matrix for our example graph looks like this:
```python
A = np.array([
	[0, 1, 1, 0, 0, 0, 0, 0, 1, 1],
	[1, 0, 1, 0, 0, 0, 0, 0, 0, 0],
	[1, 1, 0, 0, 0, 0, 0, 0, 0, 0],
	[0, 0, 0, 0, 1, 1, 0, 0, 0, 0],
	[0, 0, 0, 1, 0, 1, 0, 0, 0, 0],
	[0, 0, 0, 1, 1, 0, 1, 1, 0, 0],
	[0, 0, 0, 0, 0, 1, 0, 1, 0, 0],
	[0, 0, 0, 0, 0, 1, 1, 0, 0, 0],
	[1, 0, 0, 0, 0, 0, 0, 0, 0, 1],
	[1, 0, 0, 0, 0, 0, 0, 0, 1, 0]])
```
In the matrix, we see that row 0, column 1 has a value of 1. That means that there is an edge connecting node 0 with node 1. If the edges were weighted, the weights of the edges would go in this matrix instead of just 1s and 0s. Since our graph is undirected, the entries for row $i$, col $j$ will be equal to the entry at row $j$, col $i$. The last thing to notice is that the diagonal of this matrix is all 0, since none of our nodes have edges to themselves.

## Degree matrix
The degree of a node is how many edges connect to it. In a directed graph we could talk about in-degree and out-degree, but in this example we just have degree since the edges go both ways. Looking at our graph, we see that node 0 has degree 4, since it has 4 edges. We could also get the degree by taking the sum of the node’s row in the adjacency matrix.
The degree matrix is a diagonal matrix where the value at entry $(i, i)$ is the degree of node $i$. Let’s find the degree matrix for our example:

```python
D = np.diag(A.sum(axis=1))
print(D)

# [[4 0 0 0 0 0 0 0 0 0]
# [0 2 0 0 0 0 0 0 0 0]
# [0 0 2 0 0 0 0 0 0 0]
# [0 0 0 2 0 0 0 0 0 0]
# [0 0 0 0 2 0 0 0 0 0]
# [0 0 0 0 0 4 0 0 0 0]
# [0 0 0 0 0 0 2 0 0 0]
# [0 0 0 0 0 0 0 2 0 0]
# [0 0 0 0 0 0 0 0 2 0]
# [0 0 0 0 0 0 0 0 0 2]]
```

First, we took the sum across axis 1 (the rows) of our adjacency matrix, and then we put those values into a diagonal matrix. From the degree matrix, we can easily see that nodes 0 and 5 have 4 edges, while the rest of the nodes have only 2.

## Graph Laplacian
Now we’re going to calculate the Graph Laplacian. The Laplacian is just another matrix representation of a graph. It has several beautiful properties, which we will take advantage of for spectral clustering. To calculate the normal Laplacian (there are several variants), we just subtract the adjacency matrix from our degree matrix:

```python
L = D-A
print(L)

# [[ 4 -1 -1  0  0  0  0  0 -1 -1]
#  [-1  2 -1  0  0  0  0  0  0  0]
#  [-1 -1  2  0  0  0  0  0  0  0]
#  [ 0  0  0  2 -1 -1  0  0  0  0]
#  [ 0  0  0 -1  2 -1  0  0  0  0]
#  [ 0  0  0 -1 -1  4 -1 -1  0  0]
#  [ 0  0  0  0  0 -1  2 -1  0  0]
#  [ 0  0  0  0  0 -1 -1  2  0  0]
#  [-1  0  0  0  0  0  0  0  2 -1]
#  [-1  0  0  0  0  0  0  0 -1  2]]
```
The Laplacian’s diagonal is the degree of our nodes, and the off diagonal is the negative edge weights. This is the representation we are after for performing spectral clustering.


## Eigenvalues of Graph Laplacian
As mentioned, the Laplacian has some beautiful properties. To get a sense for this, let’s examine the eigenvalues associated with the Laplacian as I add edges to our graph:

![](https://miro.medium.com/max/1400/1*p2vrLlFxdJgGZxCGO5WBmA.gif)

We see that when the graph is completely disconnected, all ten of our eigenvalues are 0. As we add edges, some of our eigenvalues increase. In fact, the number of 0 eigenvalues corresponds to the number of connected components in our graph!

Look closely as that final edge is added, connecting the two components into one. When this happens, all of the eigenvalues but one have been lifted:

![[evs of graph laplacian plot.png]]
Image caption: Number of 0-eigenvalues is number of connected components.

The first eigenvalue is 0 because we only have one connected component (the whole graph is connected). The corresponding eigenvector will always have constant values (in this example all the values are close to 0.32).

The first nonzero eigenvalue is called the spectral gap. The spectral gap gives us some notion of the density of the graph. If this graph was densely connected (all pairs of the 10 nodes had an edge), then the spectral gap would be 10.

The second eigenvalue is called the Fiedler value, and the corresponding vector is the Fiedler vector. The Fiedler value approximates the minimum graph cut needed to separate the graph into two connected components. Recall, that if our graph was already two connected components, then the Fiedler value would be 0. Each value in the Fiedler vector gives us information about which side of the cut that node belongs. Let’s color the nodes based on whether their entry in the Fielder vector is positive or not:

![[spectral clustering_1.png]]
Image caption: Nodes colored based on whether their entry in the Fiedler Vector is >0.

This simple trick has segmented our graph into two clusters! Why does this work? Remember that zero eigenvalues represent connected components. Eigenvalues near zero are telling us that there is almost a separation of two components. Here we have a single edge, that if it didn’t exist, we’d have two separate components. So the second eigenvalue is small.

To summarize what we know so far: the first eigenvalue is 0 because we have one connected component. The second eigenvalue is near 0 because we’re one edge away from having two connected components. We also saw that the vector associated with that value tells us how to separate the nodes into those approximately connected components.

You may have noticed that the next two eigenvalues are also pretty small. That tells us that we are “close” to having four separate connected components. In general, we often look for the first large gap between eigenvalues in order to find the number of clusters expressed in our data. See the gap between eigenvalues four and five?

Having four eigenvalues before the gap indicates that there is likely four clusters. The vectors associated with the first three positive eigenvalues should give us information about which three cuts need to be made in the graph to assign each node to one of the four approximated components. Let’s build a matrix from these three vectors and perform K-Means clustering to determine the assignments:

```python
from sklearn.cluster import KMeans

# our adjacency matrix
print("Adjacency Matrix:")
print(A)

# Adjacency Matrix:
# [[0. 1. 1. 0. 0. 1. 0. 0. 1. 1.]
#  [1. 0. 1. 0. 0. 0. 0. 0. 0. 0.]
#  [1. 1. 0. 0. 0. 0. 0. 0. 0. 0.]
#  [0. 0. 0. 0. 1. 1. 0. 0. 0. 0.]
#  [0. 0. 0. 1. 0. 1. 0. 0. 0. 0.]
#  [1. 0. 0. 1. 1. 0. 1. 1. 0. 0.]
#  [0. 0. 0. 0. 0. 1. 0. 1. 0. 0.]
#  [0. 0. 0. 0. 0. 1. 1. 0. 0. 0.]
#  [1. 0. 0. 0. 0. 0. 0. 0. 0. 1.]
#  [1. 0. 0. 0. 0. 0. 0. 0. 1. 0.]]

# diagonal matrix
D = np.diag(A.sum(axis=1))

# graph laplacian
L = D-A

# eigenvalues and eigenvectors
vals, vecs = np.linalg.eig(L)

# sort these based on the eigenvalues
vecs = vecs[:,np.argsort(vals)]
vals = vals[np.argsort(vals)]

# kmeans on first three vectors with nonzero eigenvalues
kmeans = KMeans(n_clusters=4)
kmeans.fit(vecs[:,1:4])
colors = kmeans.labels_

print("Clusters:", colors)

# Clusters: [2 1 1 0 0 0 3 3 2 2]
```

![[spectral clustering_2.png]]
Image caption: Spectral Clustering for 4 clusters.

The graph has been segmented into the four quadrants, with nodes 0 and 5 arbitrarily assigned to one of their connected quadrants. That is really cool, and that is spectral clustering!

To summarize, we first took our graph and built an adjacency matrix. We then created the Graph Laplacian by subtracting the adjacency matrix from the degree matrix. The eigenvalues of the Laplacian indicated that there were four clusters. The vectors associated with those eigenvalues contain information on how to segment the nodes. Finally, we performed K-Means on those vectors in order to get the labels for the nodes. Next, we’ll see how to do this for arbitrary data.


---
## Reference(s)
1. [Spectral clustering article](https://towardsdatascience.com/spectral-clustering-aba2640c0d5b)
2. [Introduction to Spectral clustering](https://www.mygreatlearning.com/blog/introduction-to-spectral-clustering/)
