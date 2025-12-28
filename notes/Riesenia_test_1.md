---
title: Riešenia testu č. 1
---

* TOC
{:toc}

## Úloha č. 1

``` java
import java.math.*;

public class BigBinomialCoefficient {
    private int n;
    private int k;

    public BigBinomialCoefficient(int n, int k) {
        this.n = n;
        this.k = k;
    }

    public int getUpperIndex() {
        return n;
    }

    public int getLowerIndex() {
        return k;
    }

    public BigInteger evaluate() {
        BigInteger product = BigInteger.valueOf(1);
        for (int i = 0; i <= k - 1; i++) {
            product = product.multiply(BigInteger.valueOf(n).subtract(BigInteger.valueOf(i)));
        }
        BigInteger factorial = BigInteger.valueOf(1);
        for (int i = 1; i <= k; i++) {
            factorial = factorial.multiply(BigInteger.valueOf(i));
        }
        return product.divide(factorial);
    }
}
```

## Úloha č. 2

``` java
public class Node {
    
    // ... 

    public int keyFrequency(String key) {
        int result = 0;
        if (left != null) {
            result += left.keyFrequency(key);
        }
        if (right != null) {
            result += right.keyFrequency(key);
        }
        return result + (key.equals(this.key) ? 1 : 0);
    }

    public int longestKeyLength() {
        int result = key.length();
        if (left != null) {
            result = Math.max(result, left.longestKeyLength());
        }
        if (right != null) {
            result = Math.max(result, right.longestKeyLength());
        }
        return result;
    }

    public void inorderConcatenate(StringBuilder stringBuilder) {
        if (left != null) {
            left.inorderConcatenate(stringBuilder);
        }
        stringBuilder.append(key);
        if (right != null) {
            right.inorderConcatenate(stringBuilder);
        }
    }
}
```

## Úloha č. 3

``` java
import java.util.*;

public class LaurentPolynomial {
    private int minExponent;
    private int maxExponent;
    private int[] coefficients;

    public LaurentPolynomial(int minExponent, int[] coefficients) {
        int first = 0;
        while (first < coefficients.length && coefficients[first] == 0) {
            first++;
        }
        int last = coefficients.length - 1;
        while (last >= 0 && coefficients[last] == 0) {
            last--;
        }
        if (last < 0) {
            this.minExponent = Integer.MAX_VALUE;
            this.maxExponent = Integer.MIN_VALUE;
            this.coefficients = new int[0];
        } else {
            this.minExponent = minExponent + first;
            this.maxExponent = minExponent + last;
            this.coefficients = Arrays.copyOfRange(coefficients, first, last + 1);
        }
    }

    public int getDegree() {
        return maxExponent;
    }

    public int getCodegree() {
        return minExponent;
    }

    public int getCoefficient(int n) {
        if (n >= minExponent && n <= maxExponent) {
            return coefficients[n - minExponent];
        } else {
            return 0;
        }
    }

    public LaurentPolynomial add(LaurentPolynomial p) {
        int newMinExponent = Math.min(getCodegree(), p.getCodegree());
        int newMaxExponent = Math.max(getDegree(), p.getDegree());
        if (newMaxExponent < newMinExponent) {
            return new LaurentPolynomial(0, new int[0]);
        }
        int[] newCoefficients = new int[newMaxExponent - newMinExponent + 1];
        for (int i = newMinExponent; i <= newMaxExponent; i++) {
            newCoefficients[i - newMinExponent] = getCoefficient(i) + p.getCoefficient(i);
        }
        return new LaurentPolynomial(newMinExponent, newCoefficients);
    }
}
```

