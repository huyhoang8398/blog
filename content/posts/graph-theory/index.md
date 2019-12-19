---
title: "Introduction to Graph Theory"
date: 2019-12-19T08:33:26+07:00
cover: "/posts/graph-theory/images/graph1.png"
author: kn
description: "Introduction to Graph Theory"
---

## Graph basic concepts
* Graph as figure
* Graph denfinition: G(V, E):
  - V: Set of vertices
  - E: Set of edges
- Directed graphs ((u,v)), non-directed graphs ({u,v})

## Graph representations
- Adjacent: set of neighbor of Node
- Adjacent list
- Adjacent matrix

*Example*
![Graph Example](/posts/graph-theory/images/graphex.jpeg)

Ex 1: 
- G:= (V,E) with
  - V (0, 1, 2, 3,...,9) 
  - E [(0,1), (0,2), (0,3), (2,2), (2,4), (4,0), (3,4), (3,5), (5,6), (5,8), (6,8), (8,6), (8,7), (7,9), (9,8)]

- Incomming Adjacent nodes: 
  - Adj^(in)(1) = (0)
  - Adj^(in)(0) = (4)
  - Adj^(in)(2) = (0, 2)
  - Adj^(in)(3) = (0)
  - Adj^(in)(4) = (2, 3)
  - Adj^(in)(5) = (3)
  - Adj^(in)(6) = (5, 8)
  - Adj^(in)(7) = (8)
  - Adj^(in)(8) = (5, 6, 9)
  - Adj^(in)(9) = (7)

- Outcomming Adjacent node

  - Adj^(out)(0) = (2, 3)
  - Adj^(out)(2) = (2, 4)
  - Adj^(out)(3) = (4, 5 )
  - Adj^(out)(4) = (0)
  - Adj^(out)(5) = (6, 8)
  - Adj^(out)(6) = (8)
  - Adj^(out)(7) = (9)
  - Adj^(out)(8) = (6, 7)
  - Adj^(out)(9) = (8)

- Incomming Adjacent edge

  - Adj^(in)(0,1) = (4,0)
  - Adj^(in)(0,2) = (4,0)
  - Adj^(in)(0,3) = (4,0)
  - Adj^(in)(2,2) = (0,2)
  - Adj^(in)(2,4) = (2,2) (0,2)

    . . .

### Example of Graph

**Cycle Graph**
{{< image src="/posts/graph-theory/images/cycleG.png" alt="Cycle Graph" position="center" style="border-radius: 12px;" >}}

* Cycles: In graph theory, a cycle graph or circular graph is a graph that consists of a single cycle, or in other words, some number of vertices (at least 3) connected in a closed chain. The cycle graph with n vertices is called Cn. The number of vertices in Cn equals the number of edges, and every vertex has degree 2; that is, every vertex has exactly two edges incident with it.
  * Trivial cycle: C1 (only one node). 
  * C2, C3, C4, etc.
  * To write the graphs.
  * Size of a cycle.

**Wheel Graph**
{{< image src="/posts/graph-theory/images/wheelG.png" alt="Wheel Graph" position="center" style="border-radius: 12px;" >}}

* A wheel graph is a graph formed by connecting a single universal vertex to all vertices of a cycle
* W4, W5, etc