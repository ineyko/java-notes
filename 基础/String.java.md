# String

> Tips：代码参考 JDK8

## 一. 什么是 String 的不可变性？

创建一个新的 String 对象。
```java
String s = "abcd";
```
String 对象 "abcd" 被存放在堆中，而变量 s 指向堆中的地址。

```java
String s2 = s; 
```
现在，s 与 s2 分别对应两个不同的 reference，但都指向同一个堆中的地址。

```java
s = s.concat("ef");
```
此时的变量 s，不再指向 "abcd" 的地址，而是在堆中重新分配了一块区域，用来存放 "abcdef" ，而 s 则重新指向 "abcdef" 的地址。

那么，由此可以看出 String 类的不可变性，即便是通过方法对一个 String 对象进行修改，Java 也会在堆中重新创建一个新的 String 对象，而不是去修改原来的 String 对象。

如果需要原对象修改，那么建议使用 StringBuffer 或者 StringBuilder，仍然选择使用 String 会让垃圾收集器浪费大量的时间去收集很多无用的 String 对象。

## 二. 为什么 String 是不可变的？

1) 
    String 池是一个在方法区里的特殊区域，当一个字符串被创建的时候，如果在 String 池> 里不存在，则创建一个新的字符串并且存储到 String 池；如果 String 池里存在，那么> 这个对象的 reference 都指向同一个堆地址。

    ```java
    String str1 = "abcd";
    String str2 = "abcd";
    ```
    此处的 `str1` 和 `str2` 虽说是两个不同的 reference，但都指向了同一块堆内存的地址。也就是说：
    ```java
    System.out.println(str1 == str2);
    ```

    答案是 true。

    回到正题，在这样的情况下，如果 String 类是可变的，那么修改这个字符串的值，会导致其他同样指向该对象的 reference 都将受到改变。

2)
    String 类中有一个对象

    ```java
    private final int hashcode;
    ```

    hashcode 在 Java 中被大量使用，那么 String 的不可变性保证了在使用 String 类作为 HashMap 或者 HashSet 的 Key 时不会出错，并且保证了每时每刻都是相同的 hashcode，也不需要每次都再重新计算 hashcode，极大的提高了效率。

    试想，如果 String 类是可变的，那么当字符串被修改后，必定要重新计算 hashcode，如果此时正好作为 HashMap 的 Key 值，那么在 HashMap 的 Bucket 的中位置也会受到影响，而且由于每次修改字符串都会重新计算 hashcode，在效率方面也会大打折扣。

3)
    




## 三. 什么是 String 池？
## 四. 如何判断两个 String 是否相等？