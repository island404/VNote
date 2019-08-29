# Java Enumeration 枚举

### fail-fast 机制

```java
package java.util;
public interface Enumeration<E> {
    boolean hasMoreElements();
    E nextElement();
}
```

Iterator

```java
package java.util;
public interface Iterator<E> {
    boolean hasNext();
    E next();
    void remove();
}
```

HashTable Iterator底层实现了 Enumeration的遍历方式，所以使用enmu遍历更快

HashMap Iterator底层应该不是 Enumeration，enmu是线程安全的，HashMap不是线程安全的，所以没必要使用枚举遍历。