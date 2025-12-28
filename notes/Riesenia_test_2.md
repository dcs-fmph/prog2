---
title: Riešenia testu č. 2
---

* TOC
{:toc}

## Úloha č. 1

``` java
import java.util.*;

public class ComparableHashSet<E extends Comparable<E>> extends TreeSet<E> implements Comparable<Set<E>> {

    public ComparableHashSet() {
        super();
    }

    public ComparableHashSet(Collection<? extends E> c) {
        super(c);
    }

    @Override
    public int compareTo(Set<E> o) {
        if (equals(o)) {
            return 0;
        }
        if (size() != o.size()) {
            return size() - o.size();
        }
        Set<E> difference1 = new TreeSet<>(this);
        difference1.removeAll(o);
        Set<E> difference2 = new TreeSet<>(o);
        difference2.removeAll(this);
        return Collections.min(difference1).compareTo(Collections.min(difference2));
    }
}
```

## Úloha č. 2

``` java
import java.util.*;

public class ExtendedHashMap<K,V> extends HashMap<K,V> {

    public ExtendedHashMap() {
        super();
    }

    public ExtendedHashMap(Map<? extends K,? extends V> m) {
        super(m);
    }

    public Set<V> image(Set<K> set) {
        Set<V> result = new HashSet<>();
        for (K key : set) {
            if (containsKey(key)) {
                result.add(get(key));
            }
        }
        return Collections.unmodifiableSet(result);
    }

    public Set<K> preimage(Set<V> set) {
        Set<K> result = new HashSet<>();
        for (K key : keySet()) {
            if (set.contains(get(key))) {
                result.add(key);
            }
        }
        return Collections.unmodifiableSet(result);
    }

    public boolean isInjective() {
        Set<K> keySet = keySet();
        Set<V> valueSet = new HashSet<>(values());
        return keySet.size() == valueSet.size();
    }
}
```

## Úloha č. 3

``` java
import java.util.*;

public class RepetitiveIterator<E> implements Iterator<E> {
    private Iterator<E> iterator;
    private List<Integer> repetitions;
    private boolean hasNext;
    private E next;
    private int nextIndex;
    private int repetitionsLeft;

    public RepetitiveIterator(Iterator<E> iterator, List<Integer> repetitions) {
        if (iterator == null || repetitions == null || repetitions.size() == 0) {
            throw new IllegalArgumentException();
        }
        this.iterator = iterator;
        this.repetitions = new ArrayList<>(repetitions);
        if ((new HashSet<>(repetitions)).equals(Set.of(0))) {
            hasNext = false;
        } else {
            findNext();
        }
    }

    private void findNext() {
        if (repetitionsLeft == 0) {
            repetitionsLeft = repetitions.get(nextIndex);
            nextIndex = (nextIndex + 1) % repetitions.size();
            hasNext = iterator.hasNext();
            if (hasNext) {
                next = iterator.next();
            }
            if (repetitionsLeft == 0) {
                findNext();
                return;
            }
        }
        repetitionsLeft--;
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

