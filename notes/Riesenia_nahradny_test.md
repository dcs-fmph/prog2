---
title: Riešenia náhradného testu
---

* TOC
{:toc}

## Úloha č. 1

``` java
// ...

public class Shapes extends Application {

    @Override
    public void start(Stage primaryStage) {
        // ...

        /* ------------------------------------------------------------------------ */

        ArrayList<Circle> circles = new ArrayList<>();
        ArrayList<Rectangle> rectangles = new ArrayList<>();

        canvas.setOnMouseClicked(event -> {
            MouseButton mouseButton = event.getButton();
            if (mouseButton == MouseButton.PRIMARY) {
                Circle circle = new Circle(event.getX(), event.getY(), 20);
                circle.setFill(Color.RED);
                circles.add(circle);
                canvas.getChildren().add(circle);
            }
            if (mouseButton == MouseButton.SECONDARY) {
                Rectangle rectangle = new Rectangle(event.getX(), event.getY(), 30, 30);
                rectangle.setFill(Color.BLUE);
                rectangles.add(rectangle);
                canvas.getChildren().add(rectangle);
            }
        });

        MenuBar menuBar = new MenuBar();
        borderPane.setTop(menuBar);
        Menu mView = new Menu("Zobrazenie");
        menuBar.getMenus().add(mView);
        MenuItem miClearCircles = new MenuItem("Zmazať kruhy");
        MenuItem miClearRectangles = new MenuItem("Zmazať štvorce");
        MenuItem miSwapColours = new MenuItem("Vymeniť farby");
        MenuItem miClose = new MenuItem("Zavrieť");
        mView.getItems().addAll(miClearCircles, miClearRectangles, miSwapColours, miClose);

        miClearCircles.setOnAction(event -> {
            canvas.getChildren().removeAll(circles);
            circles.clear();
        });

        miClearRectangles.setOnAction(event -> {
            canvas.getChildren().removeAll(rectangles);
            rectangles.clear();
        });

        miSwapColours.setOnAction(event -> {
            for (Circle circle : circles) {
                if (circle.getFill() == Color.RED) {
                    circle.setFill(Color.BLUE);
                } else {
                    circle.setFill(Color.RED);
                }
            }
            for (Rectangle rectangle : rectangles) {
                if (rectangle.getFill() == Color.RED) {
                    rectangle.setFill(Color.BLUE);
                } else {
                    rectangle.setFill(Color.RED);
                }
            }
        });

        miClose.setOnAction(event -> {
            Platform.exit();
        });

        /* ------------------------------------------------------------------------ */

        // ...
    }

    // ...

}
```

## Úloha č. 2

``` java
import java.util.*;
import java.util.function.*;

public class FilteredIterator<E> implements Iterator<E> {
    private Iterator<E> iterator;
    private Predicate<E> predicate;
    private boolean hasNext;
    private E next;

    public FilteredIterator(Iterator<E> iterator, Predicate<E> predicate) {
        this.iterator = iterator;
        this.predicate = predicate;
        findNext();
    }

    private void findNext() {
        if (iterator.hasNext()) {
            next = iterator.next();
            if (!predicate.test(next)) {
                findNext();
            } else {
                hasNext = true;
            }
        } else {
            hasNext = false;
            next = null;
        }
    }

    @Override
    public boolean hasNext() {
        return hasNext;
    }

    @Override
    public E next() {
        if (!hasNext) {
            throw new NoSuchElementException();
        }
        E result = next;
        findNext();
        return result;
    }
}
```

## Úloha č. 3

``` java
package graphs;

import java.util.*;

public class FixedSizeBiclique {
    private UndirectedGraph g;
    private int s;
    private int t;
    private LinkedList<Integer> currentVertices1;
    private LinkedList<Integer> currentVertices2;
    private Set<Set<Integer>> biclique;

    public FixedSizeBiclique(UndirectedGraph g, int s, int t) {
        this.g = g;
        this.s = s;
        this.t = t;
        currentVertices1 = new LinkedList<>();
        currentVertices2 = new LinkedList<>();
        search(0);
    }

    private void search(int vertex) {
        if (vertex == g.getVertexCount()) {
            if (currentVertices1.size() == s && currentVertices2.size() == t && isBiclique(currentVertices1, currentVertices2)) {
                biclique = Set.of(Collections.unmodifiableSet(new HashSet<>(currentVertices1)),
                        Collections.unmodifiableSet(new HashSet<>(currentVertices2)));
            }
        } else {
            currentVertices1.addLast(vertex);
            search(vertex + 1);
            currentVertices1.removeLast();
            currentVertices2.addLast(vertex);
            search(vertex + 1);
            currentVertices2.removeLast();
            search(vertex + 1);
        }

    }

    private boolean isBiclique(Collection<Integer> vertices1, Collection<Integer> vertices2) {
        for (int u : vertices1) {
            for (int v : vertices2) {
                if (!g.hasEdge(u, v)) {
                    return false;
                }
            }
        }
        return true;
    }

    public Set<Set<Integer>> getBiclique() {
        return biclique;
    }

}
```

