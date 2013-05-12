---
layout: post
title: "study in community detection in network"
description: ""
category: 
tags: [clique, cluster, community, graph, network]
---
{% include JB/setup %}
Recently I'm thinking if I can find out any interest preference within a bunch of online novel reading logs. The idea is intuitive, people may be interested in some kind of novel like love story, magic, sci-fi or military etc. , then the the novels in the same kind may be read by a same person frequently than two novels out of the same kind. It seems easy to identify for coarse&nbsp;categories as I already have category tag for each of the novel, but identify interest within more subdivided categories or even within totally another kind of&nbsp;hierarchy of categories like interest of different ages seems to have much more fun.
<br />
The first thing comes into my head is using clustering, classical algorithms are hierarchical, k-means, canopy, Gaussian mixture model, Dirichlet process clustering, etc. The limitation is using only novel reading logs, novel can not be represented as point in space. In this case, k-means, center-based hierarchical and Gaussian mixture model is not suitable. In spite of this, normal cluster algorithm only considers links between 2 nodes, but rarely consider links among all nodes within a cluster.
<br />
<a href="http://en.wikipedia.org/wiki/Clustering_coefficient">Clustering Coefficient</a>&nbsp;is a good way to describe the attribute of a cluster, but&nbsp;exhaustively&nbsp;enumerate all possible clusters and calculate clustering coefficient is clearly unacceptable.
<br />
the paper&nbsp;<a href="http://arxiv.org/pdf/cond-mat/0308217">Finding and evaluating community structure in networks, MEJ Newman, M Girvan - Physical review E, 2004 - APS</a>&nbsp;is trying to solve similar problem, and the features of its method is, first it's an dividing method not an&nbsp;agglomerating method, second, it uses "betweenness" which is defined on an edge as weighted sum of shorted path between any 2 nodes passes through this edge, to divide the graph.
<br />
but it's different from what I thought.
<br />
first, In real world, a node can belong to different communities at the same time
<br />
second, it's fine some nodes are alone, or the cluster is very small, but the middle size clusters which is very cohesive is what I'm looking for. so I'm still prefer aggregating method of clustering.
<br />

<br />
Recently, I'm thinking through the clique method,&nbsp;it is:
<br />

<br />
<ul>
<li>first generate all <span style="background-color: lime;">maximal cliques</span> of the whole graph.&nbsp;</li>
<li>second, use the maximal cliques as core and attach other nodes to a clique to form a cluster if the node is close enough to most of the nodes in the clique.&nbsp;</li>
</ul>

<br />
like the picture below, the red nodes and deep green nodes are 2 group of maximal cliques, and the orange nodes are attached to red clique to form a cluster, and grass green nodes are attached to deep green nodes to form another cluster. there are some white nodes which are not in any cluster, and the middle node have both orange and grass green belongs to both cluster
<br />
<div class="separator" style="clear: both; text-align: center;">
<a href="http://2.bp.blogspot.com/-8Q8ANE80OvQ/UNlrr_wuGhI/AAAAAAAAABU/UVnNYGBR4h4/s1600/nodes.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://2.bp.blogspot.com/-8Q8ANE80OvQ/UNlrr_wuGhI/AAAAAAAAABU/UVnNYGBR4h4/s1600/nodes.png" /></a></div>
but the problem is finding clique is NP-Complete problem, when the graph is as large as social network,&nbsp;sequential&nbsp;&nbsp;method will be too slow. there are some useful thing I found:
<br />

<br />
<ol>
<li><a href="http://books.google.com.hk/books?hl=en&amp;lr=&amp;id=ce7bEB6Q1gMC&amp;oi=fnd&amp;pg=PA37&amp;ots=0t00beNxrm&amp;sig=yGA0ZLBvr0wmssFW9xdIrySkTTY&amp;redir_esc=y&amp;hl=zh-CN&amp;sourceid=cndr#v=onepage&amp;q&amp;f=true">"On Computing All Maximal Cliques Distributedly",Fábio Protti, Felipe M. G. Fran?a, and Jayme Luiz Szwarcfiter</a>&nbsp;, a divide and&nbsp;conquer&nbsp;method, recursively divide graphs into v1, v2, and computer cliques in v1, v2 and between v1, v2</li>
<li><a href="http://xrime.sourceforge.net/">xrime</a>, a graph algorithm package, which include a maximal clique method. It generates the neighbors of neighbors for each node, and find maximal clique in each set of neighbors of neighbors. the method is clean and tricky but to emit all adjacent list to all neighbors seems to be too heavy.</li>
</ol>
<div>
here is a map-reduce friendly algorithm I work out to find maximal clique level by level, see the blog&nbsp;<a href="http://wonderyl.blogspot.com/2013/01/a-simple-maximal-clique-algorithm-for.html">a simple Maximal Clique algorithm for map reduce</a>&nbsp;for detail</div>
<div>

<br /></div>

<br />

<br />