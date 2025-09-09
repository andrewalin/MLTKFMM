# Clustering and the spatial distance metaphor #

## Spatial clustering as the default mental model ##

Most common clustering methods (like k-means, hierarchical clustering, DBSCAN, Gaussian mixtures) are indeed "spatial" in a sense: they depend on some notion of distance or similarity in a feature space. That could be Euclidean distance, cosine similarity, Mahalanobis distance, etc. In those cases, cluster membership is about “points being close to each other” across all dimensions after aggregation.

## But not all clustering is strictly spatial ##

There are families of clustering methods that don’t rely purely on a geometric distance:

Graph-based clustering: Methods like spectral clustering, community detection (e.g., Louvain, Girvan–Newman) use connectivity structure, not distance. Two nodes are in the same cluster if they’re strongly connected or share many paths, not necessarily because they’re “close” in vector space.

Model-based clustering: Mixture models (like Gaussian Mixture Models) do use distances, but more fundamentally they assume data comes from a combination of underlying probability distributions. Assignment is about likelihood, not just distance.

Density-based clustering (DBSCAN, OPTICS): Still spatial in spirit, but the emphasis is on regions of high density separated by low-density voids — not purely point-to-point closeness.

Information-theoretic clustering: Clusters can be defined by minimizing information loss, maximizing mutual information, or reducing description length, rather than direct spatial proximity.

Conceptual/semantic clustering: In text clustering, for example, clusters may emerge from shared features (topics, words, embeddings) that aren’t purely spatial, depending on the representation and similarity metric.

## In Summary ##

The reason distance-based intuition is so common is that most clustering methods rely on embedding data into some metric space (numeric features, embeddings, kernels). Once you have that, it’s natural to use spatial closeness. But the clustering principle can just as well be framed in terms of connectivity, density, probability, or information content.

So: spatial clustering is one major approach, but not the only one. It’s best thought of as the most common operationalization of clustering, but the underlying idea of “finding structure without labels” can be instantiated in many ways beyond distance.

