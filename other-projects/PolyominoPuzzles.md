---
layout: other-project
title: Polyomino Puzzle
nav-title: Other Projects
---
When I was younger, we had a Puzzle consisting of bricks you had to place in a given form.

Those ``bricks`` are commonly known as polyominos and there are numerous solvers already.

Once you have the solutions however, the questions do not end.
For example: 
- How similar are two solutions?
- Or more general, how do the solutions relate to each other?
- Is there a way to transverse the solutions?
- Is there a structure behind them -
- and could we predict the local/global behavior of solutions when viewed as a graph?

For example these two solutions only differ in the position of two bricks:

![](/assets/images/Polyomino/solution1.png)
![](/assets/images/Polyomino/solution2.png)

We could therefore say, these have a difference of 2.
In general, saying the difference of two solution is the 
number of bricks that differ in their position, 
gives rise to a metric on the set of solutions.

We can view the solutions as a graph on the vertex set (= set of solutions) 
by connecting any two leq-m-solutions (the solutions are identical up to at most 5 bricks) for a given integer m.

However due to the many solutions such a puzzle may have, 
a graph with vertex set consisting of all solutions 
might not give us much insight.

One idea is to use methods of graph-coarsening to simplify the graph. The one I used is sketched out below:

Given two numbers 1 <= n < m <= #polyominos
 - nodes of the graph are the equivalence classes of solutions modulo the composite closure of leq-n-connectedness;
   One equivalence class contains all the solutions, for which there
   exists a finite number of leq-n-changes to get from one solution to the other.
 - edges are drawn between two equivalence classes of solutions, if there are two representatives of the equivalence classes, which are leq-m-connected.

This narrows down the number of nodes in the example puzzle given from 32288 to just 971, if we take n=4 and m=5.
The biggest connected component of this result is the uppermost - left picture. The node highlighted in green contains about 90% of all possible solutions.

<img src="/assets/images/Polyomino/solution-graph-overview.png" width="90%">

And we can repeat this step. Given the subgraph of only the green highlighted equivalence class. We repeat this with n=3 and m=4 and obtain the middle picture.
Repeated again with n=2 and m=3, we get the third picture above. At last we get the fourth picture when setting n=1 (no equivalence classes, except two or more polyomino fill the same space) and m=2. There is a remarking symmetry in the last picture.

This approach let's you 'zoom-in' on a specific solution. 

In an effort to quickly test arbitrary puzzles 
I started a Java/Scala project.
At 'zoom-in' it creates the following files:

- In directory
  - `*_solution_ids.txt` - a listing of all solution-ids (of the canonical representative element) occurring in the graph (no equivalence classes)
  - `*_nodes.csv` - a listing of all nodes occurring (representative element, no equivalence classes)
- Out directory
  - `*_size[X]_leqm-n.csv_EQ.csv` - nodes as integer values (equivalence classes)
  - `*_size[X]_leqm-n.csv_ET.csv` - edge table containing all edges
  - `*_size[X]_leqm-n.csv_NT.csv` - nodes weight table (weight = size of equivalence class)
    
The files generated in the output directory are easily imported 
into a graph-viewing program like [Gephi](https://gephi.org/), which I used to obtain the above graphs.

### Quicklinks
- [old zoom-in program](https://github.com/jananica/PolyominoZoomInSolution/) (no longer maintained.)
- [PPSF - PolyominoPuzzleSolutionFinder](https://github.com/jananica/PolyominoPuzzleSolutionFinder/)
create puzzles and zoom into solutions. (still being in development.)


