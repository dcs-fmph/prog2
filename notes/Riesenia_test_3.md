---
title: Riešenia testu č. 3
---

* TOC
{:toc}

## Úloha č. 1

``` java
package graphs;

import java.util.*;

public class EdgeColourings {

    public static boolean isEdgeColouring(UndirectedGraph g, Map<UndirectedEdge, Integer> colours) {
        if (colours.size() != g.getUndirectedEdgeCount()) {
            return false;
        }
        for (UndirectedEdge e : colours.keySet()) {
            LinkedList<Integer> eVertices = new LinkedList<>(e.getIncidentVertices());
            int u = eVertices.remove();
            int v = eVertices.isEmpty() ? u : eVertices.remove();
            if (!g.hasVertex(u) || !g.hasVertex(v) || !g.hasEdge(u,v) || colours.get(e) < 0) {
                return false;
            }
        }
        for (UndirectedEdge e1 : colours.keySet()) {
            for (UndirectedEdge e2 : colours.keySet()) {
                if (!e1.equals(e2) && !Collections.disjoint(e1.getIncidentVertices(), e2.getIncidentVertices()) &&
                        colours.get(e1).equals(colours.get(e2))) {
                    return false;
                }
            }
        }
        return true;
    }
}
```

## Úloha č. 2

``` java
package graphs;

import java.util.*;

public class GeneralisedDominatingSets {

    public static boolean isGeneralisedDominatingSet(UndirectedGraph g, Set<Integer> vertices, int k) {
        Queue<Integer> queue = new LinkedList<>();
        ArrayList<Integer> distances = new ArrayList<>();
        int n = g.getVertexCount();
        for (int v = 0; v <= n - 1; v++) {
            distances.add(-1);
        }
        for (int v : vertices) {
            distances.set(v, 0);
            queue.add(v);
        }
        while (!queue.isEmpty()) {
            int vertex = queue.remove();
            for (int v : g.outgoingEdgesDestinations(vertex)) {
                if (distances.get(v) == -1) {
                    distances.set(v, distances.get(vertex) + 1);
                    queue.add(v);
                }
            }
        }
        for (int v = 0; v <= n - 1; v++) {
            if (distances.get(v) < 0 || distances.get(v) > k) {
                return false;
            }
        }
        return true;
    }
}
```

## Úloha č. 3

``` java
package graphs;

import java.util.*;

public class FixedLengthCycles {
    private DirectedGraph g;
    private int length;
    private int vertex;

    private LinkedList<Integer> walk;
    private ArrayList<Boolean> visited;

    private Set<List<Integer>> cycles;

    public FixedLengthCycles(DirectedGraph g, int length, int vertex) {
        this.g = g;
        this.vertex = vertex;
        this.length = length;
        cycles = new HashSet<>();

        visited = new ArrayList<>();
        for (int i = 0; i <= g.getVertexCount() - 1; i++) {
            visited.add(false);
        }
        walk = new LinkedList<>();
        walk.add(vertex);
        visited.set(vertex, true);
        if (length > 0) {
            search();
        }
    }

    private void search() {
        if (walk.size() == length) {
            if (g.hasEdge(walk.getLast(), vertex)) {
                walk.add(vertex);
                cycles.add(Collections.unmodifiableList(new LinkedList<>(walk)));
                walk.removeLast();
            }
        } else {
            for (int successor : g.outgoingEdgesDestinations(walk.getLast())) {
                if (!visited.get(successor)) {
                    visited.set(successor, true);
                    walk.add(successor);
                    search();
                    walk.removeLast();
                    visited.set(successor, false);
                }
            }
        }
    }

    public Set<List<Integer>> getCycles() {
        return Collections.unmodifiableSet(cycles);
    }
}
```

