# String

> Tips：代码参考 JDK8

## 一. 什么是 String 的不可变性？

1) **创建一个新的 String 对象**。
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
    **String 池**
    
    这是一个在 Java 堆里的特殊区域，当一个字符串被创建的时候，如果在 String 池里不存在，则创建一个新的字符串并且存储到 String 池；如果 String 池里存在，那么这个对象的 reference 都指向同一个堆地址。

    ```java
    String str1 = "abcd";
    String str2 = "abcd";
    ```

    ![java-string-pool](https://github.com/ineyko/java-notes/raw/master/images/java-string-pool.jpeg)

    此处的 `str1` 和 `str2` 虽说是两个不同的 reference，但都指向了同一块堆内存的地址。也就是说：

    ```java
    System.out.println(str1 == str2);
    ```

    答案是 true。

    回到正题，在这样的情况下，如果 String 类是可变的，那么修改这个字符串的值，会导致其他同样指向该对象的 reference 都将受到改变。

2)
    **String 类中有一个对象**

    ```java
    private final int hashcode;
    ```

    hashcode 在 Java 中被大量使用，那么 String 的不可变性保证了在使用 String 类作为 HashMap 或者 HashSet 的 Key 时不会出错，并且保证了每时每刻都是相同的 hashcode，也不需要每次都再重新计算 hashcode，极大的提高了效率。

    试想，如果 String 类是可变的，那么当字符串被修改后，必定要重新计算 hashcode，如果此时正好作为 HashMap 的 Key 值，那么在 HashMap 的 Bucket 的中位置也会受到影响，而且由于每次修改字符串都会重新计算 hashcode，在效率方面也会大打折扣。

3)
    **不可变类型天然线程安全**

    由于 String 对象是不可变的，那么可以随意在多线程环境下使用，线程间使用 String 不需要执行同步操作。


## 三. 什么是 String 池？

1)
    下图展示了 String 池是如何存在于 Java 堆内存中以及使用两种不同的方式创建 String 对象所发生的现象。

    ![String-Pool](https://github.com/ineyko/java-notes/raw/master/images/String-Pool-Java1-450x249.png)

2)
    创建了多少对象？

    ```java
    String abc = new String("abc");
    ```
    这行代码，首先有个 new 关键字，那么就需要在 Java 堆里创建一个 abc 对象，同时去查看 String 池中是否存在 abc 字符串，如果 abc 字符串不存在，则新增一个；如果存在，则不再新增，也就是总共创建了2个对象或者1个对象。

3) 
    **String 池的优缺点**

    String 池有效的避免了相同字符串的重复创建，节省了空间以及节省了创建相同字符串的时间；但凡事都有利弊，String 池中的字符串可能会对应很多个 reference，这也就增加了查找 reference 的时间。


参考：

[why-string-is-immutable-in-java](https://www.programcreek.com/2013/04/why-string-is-immutable-in-java/)

[what-is-java-string-pool](https://www.journaldev.com/797/what-is-java-string-pool)

