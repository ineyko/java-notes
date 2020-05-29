# SpringDataJPA 中的分页踩坑。

在 SpringDataJPA 的分页对象 Page 中，有一个方法 `getContent()` 可以直接获取分好页之后的 list，源码：
```java
public interface Slice<T> extends Streamable<T> {
    List<T> getContent();

    // other method...
}
```
Page 类继承自 Slice 类。

看似没有什么问题，当调用 `getContent()` 方法之后对返回的这个 list 进行 增、删、改 操作时，则会抛出 `UnsupportedOperationException`

所以就跟踪一下源码会发现虽然返回的是 list 接口，但它的实现是 `Collections.unmodifiableList()`，那么这个 `unmodifiableList` 就是 `Collections` 中的一个内部类 `UnmodifiableList`，看到源码就恍然大悟了，源码如下：
```java
 public E get(int index) {return list.get(index);}
        public E set(int index, E element) {
            throw new UnsupportedOperationException();
        }
        public void add(int index, E element) {
            throw new UnsupportedOperationException();
        }
        public E remove(int index) {
            throw new UnsupportedOperationException();
        }
        public int indexOf(Object o)            {return list.indexOf(o);}
        public int lastIndexOf(Object o)        {return list.lastIndexOf(o);}
        public boolean addAll(int index, Collection<? extends E> c) {
            throw new UnsupportedOperationException();
        }

        @Override
        public void replaceAll(UnaryOperator<E> operator) {
            throw new UnsupportedOperationException();
        }
        @Override
        public void sort(Comparator<? super E> c) {
            throw new UnsupportedOperationException();
        }
```

可以看到与增、删、改的相关操作全部都是直接抛出异常，这也与这个类的名字对应起来，到这问题原因就找到了。

既然问题原因找到了之后，解决方法其实就有很多中，这里写几种比较常使用的：

1、重新构造一个 ArrayList
```java
List list = new ArrayList(page.getContent());
```

2、使用 lambda 表达式
```java
List list = page.getContent().stream().collect(Collectors.toList());
```


完。