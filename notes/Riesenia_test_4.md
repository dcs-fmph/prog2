---
title: Riešenia testu č. 4
---

* TOC
{:toc}

## Úloha č. 1

``` java
package graphs;

import java.util.*;

public class CheapestVertexCover {
    private UndirectedGraph g;
    private List<Integer> vertexPrices;
    private Set<Integer> cheapestVertexCover;
    private int minimumPrice = Integer.MAX_VALUE;
    private LinkedList<Integer> currentVertices;
    private int currentPrice;

    public CheapestVertexCover(UndirectedGraph g, List<Integer> vertexPrices) {
        this.g = g;
        this.vertexPrices = new ArrayList<>(vertexPrices);
        currentVertices = new LinkedList<>();
        cheapestVertexCover = new HashSet<>();
        search(0);
    }

    private boolean isVertexCover(Collection<Integer> vertices) {
        for (int u = 0; u <= g.getVertexCount() - 1; u++) {
            if (!vertices.contains(u)) {
                for (int v : g.outgoingEdgesDestinations(u)) {
                    if (!vertices.contains(v)) {
                        return false;
                    }
                }
            }
        }
        return true;
    }

    private void search(int vertex) {
        if (vertex == g.getVertexCount()) {
            if (isVertexCover(currentVertices) && currentPrice < minimumPrice) {
                cheapestVertexCover = new HashSet<>(currentVertices);
                minimumPrice = currentPrice;
            }
        } else {
            currentVertices.addLast(vertex);
            currentPrice += vertexPrices.get(vertex);
            search(vertex + 1);
            currentVertices.removeLast();
            currentPrice -= vertexPrices.get(vertex);
            search(vertex + 1);
        }
    }

    public Set<Integer> getCheapestVertexCover() {
        return Collections.unmodifiableSet(cheapestVertexCover);
    }
}
```

## Úloha č. 2

``` java
package graphs;

import java.util.*;

public class OptimalTopologicalOrder {

    public static List<Integer> optimalTopologicalOrder(DirectedGraph g, List<Integer> vertexPriorities) {
        int n = g.getVertexCount();
        List<Integer> unprocessedPredecessors = new ArrayList<>();
        for (int v = 0; v <= n - 1; v++) {
            unprocessedPredecessors.add(0);
        }
        for (int v = 0; v <= n - 1; v++) {
            for (int successor : g.outgoingEdgesDestinations(v)) {
                unprocessedPredecessors.set(successor, unprocessedPredecessors.get(successor) + 1);
            }
        }
        PriorityQueue<Integer> ready = new PriorityQueue<>((u, v) -> vertexPriorities.get(v) - vertexPriorities.get(u));
        for (int v = 0; v <= n - 1; v++) {
            if (unprocessedPredecessors.get(v) == 0) {
                ready.add(v);
            }
        }
        List<Integer> result = new LinkedList<>();

        while (!ready.isEmpty()) {
            int vertex = ready.remove();
            result.add(vertex);
            for (int successor : g.outgoingEdgesDestinations(vertex)) {
                unprocessedPredecessors.set(successor, unprocessedPredecessors.get(successor) - 1);
                if (unprocessedPredecessors.get(successor) == 0) {
                    ready.add(successor);
                }
            }
        }

        if (result.size() == n) {
            return result;
        } else {
            return null;
        }
    }
}
```

## Úloha č. 3

``` java
package graphs;

import java.util.*;

public class MinimumCycleCovering {
    private UndirectedGraph g;

    private List<Integer> walk;
    private ArrayList<Boolean> visited;
    private List<List<Integer>> allCycles;
    private int cycleCount;

    private Set<Integer> currentCycleSet;
    private Set<List<Integer>> minimumCycleCovering;

    public MinimumCycleCovering(UndirectedGraph g) {
        this.g = g;
        allCycles = new ArrayList<>();
        visited = new ArrayList<>();
        for (int v = 0; v <= g.getVertexCount() - 1; v++) {
            visited.add(false);
        }
        for (int v = 0; v <= g.getVertexCount() - 1; v++) {
            walk = new LinkedList<>();
            walk.add(v);
            visited.set(v, true);
            findCycles();
            visited.set(v, false);
        }
        cycleCount = allCycles.size();
        currentCycleSet = new HashSet<>();
        search(0);
    }

    private boolean isCycleCovering(Set<Integer> cycleSet) {
        Set<Integer> vertices = new HashSet<>();
        for (int cycleIndex : cycleSet) {
            vertices.addAll(allCycles.get(cycleIndex));
        }
        return vertices.size() == g.getVertexCount();
    }

    private void search(int cycleIndex) {
        if (cycleIndex == cycleCount) {
            if (isCycleCovering(currentCycleSet) && (minimumCycleCovering == null || currentCycleSet.size() < minimumCycleCovering.size())) {
                minimumCycleCovering = new HashSet<>();
                for (int index : currentCycleSet) {
                    minimumCycleCovering.add(allCycles.get(index));
                }
            }
        } else {
            currentCycleSet.add(cycleIndex);
            search(cycleIndex + 1);
            currentCycleSet.remove(cycleIndex);
            search(cycleIndex + 1);
        }
    }

    private void findCycles() {
        if (walk.size() != 2 && g.hasEdge(walk.getLast(), walk.getFirst())) {
            walk.add(walk.getFirst());
            allCycles.add(Collections.unmodifiableList(new LinkedList<>(walk)));
            walk.removeLast();
        }
        for (int successor : g.outgoingEdgesDestinations(walk.getLast())) {
            if (!visited.get(successor)) {
                if (successor > walk.getFirst()) {
                    visited.set(successor, true);
                    walk.add(successor);
                    findCycles();
                    walk.removeLast();
                    visited.set(successor, false);
                }
            }
        }
    }

    public Set<List<Integer>> getMinimumCycleCovering() {
        if (minimumCycleCovering == null) {
            return null;
        }
        return Collections.unmodifiableSet(minimumCycleCovering);
    }
}
```

