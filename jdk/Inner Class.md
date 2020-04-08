# 内部类（Inner Class）

#### 前些日子看别的同事之前写的代码，有这么一段代码：

```java
List<Object> paramList = new ArrayList<Object>(){
    {
      add(str);
    }

}
```
#### 这一段代码我很奇怪，怎么跟常规写法不太一样，触及到了我的知识盲区。

#### 后来查资料，发现这段代码实际上就是内部类 + 代码块的一种写法。茅塞顿开。

## 内部类（Inner Class）

### 内部类又可以分四个类别：
#### 1. 成员内部类（Non-static nested classes）
```java
class Param {
  // 成员内部类的写法
  class InnerParam {}
}
```
#### 成员内部类可以看作是一个外部的普通变量，与其他成员变量一样，可以使用 `private`、`protected`、`public` 来修饰。成员内部类可以访问外部类的成员变量和成员方法。

#### 成员内部类不允许包含 *静态方法* 和 *静态变量*，因为在外部类加载的时候并不会加载内部类，而如果在内部类定义 `static` 变量修饰方法或者变量，又要使得在类加载的时候加载到内存中。换句话说，内部类的初始化必须是以外部类初始化为前提的，如果内部类中允许定义 `static` 变量或者方法，就会变成加载了内部类的 `static` 的方法和变量，在使用时却发现外部类还没有初始化。存在冲突。
  

#### 2. 匿名内部类（Anonymous Inner Class）

#### 匿名，顾名思义就是没有名字的类。创建的方式有两种：
```java
// 第一种：作为一个匿名子类。
class Demo1 {
    void show(){
      System.out.println("Class Demo1");
    }
}

class Demo2 {
    Demo1 demo1 = new Demo1(){
        void show(){
          System.out.println("Class Demo1 Anonymous Inner Class");
        }
    }
}

// 第二种：直接作为接口实现。Java 8 中可以使用 lambda 表达式来简化代码。
interface Demo3 {
    void sayHello();
}

class Demo4 {
    public static void main(String[] args){
        Demo3 demo3 = new Demo3(){
            @Override
            void sayHello(){
                System.out.println("Hello");
            }
        };
    }
}

// 以上写法可以简化成：
class Demo4 {
    public static void main(String[] args) {
        Demo3 demo3 = () -> System.out.println("Hello")
    }
}

```
#### 3. 方法内部类
#### 定义在方法内部的类，作用域与方法一致，不能使用外部类的成员变量和方法。
```java
class Outer { 
    void outerMethod() { 
        System.out.println("inside outerMethod"); 
        class Inner { 
            void innerMethod() { 
                System.out.println("inside innerMethod"); 
            } 
        } 
        Inner y = new Inner(); 
        y.innerMethod(); 
    } 
} 
class MethodDemo { 
    public static void main(String[] args) { 
        Outer x = new Outer(); 
        x.outerMethod(); 
    } 
} 
```

> 💡: 在 JDK 1.7 中，方法内部类不能使用方法中的 `final` 变量，但在 JDK 1.8 中，则可以使用了。
#### 4. 静态内部类
#### 静态内部类就像外部类的一个静态成员变量一样，但不能直接访问外部类的 *非静态变量* 和 *非静态方法*。

---
参考：

[Inner class in java](https://www.geeksforgeeks.org/inner-class-java/)

[Anonymous Inner Class in Java](https://www.geeksforgeeks.org/anonymous-inner-class-java/?ref=lbp)

[Nested Classes in Java](https://www.geeksforgeeks.org/nested-classes-java/?ref=lbp)