---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:08:34 pm","date-modified":"2023-04-29-Sat, 8:35:59 pm","permalink":"/programming/language/java-basic/","dgPassFrontmatter":true}
---


[开发百宝箱 | Java 全栈知识体系 (pdai.tech)](https://pdai.tech/md/resource/tools.html#代码托管)

# 开发环境

goole jdk download

jdk：java development kit

java SE：java standard edition

IntelliJ：IDE

## Mac 多 Jdk 控制台

<https://juejin.cn/post/6981370843968307208>

# 基础概念

## Function

```java
void sendEmail (email) {
    
}

String functionName () {
    
}
```

## Main 函数

所有 java 程序的入口，所有的逻辑都是放在 main 函数内部

```java
class Main {
    public static void main() {
        ...
    }
}
```

## 类

所有的属性和方法都应该有一个修饰符

```java
public class Main {
    public void main() {
        ...
    }
}
```

System Class

## Package

在 package 下新建类，RUN 配置输入 Package 查找到相应的主类

packge 用来组织类

![image-20220406143902652](/img/user/programming/language/java-basic/image-20220406143902652.png)

![image-20220406144123542](/img/user/programming/language/java-basic/image-20220406144123542.png)

## 编译

java code 经过 compile 生成字节码

```
javac HelloWorld.java
```

生成 HelloWorld.class 文件

java 运行时： JVM，java virtual machine

![image-20220406121753993](/img/user/programming/language/java-basic/image-20220406121753993.png)

![image-20220406122041748](/img/user/programming/language/java-basic/image-20220406122041748.png)

在 IDEA 中点击运行，IDEA 自动帮忙我们执行了编译，然后运行

# 语法基础

## 变量

```java
String str = "abc";
```

类型 + 变量名

## 注释

## 保留字/关键字

C 族语言都有的，不多说

## 原始数据类型 Primitive

| Type    | Bytes | Range       |
| ------- | ----- | ----------- |
| byte    | 1     | [-128, 127] |
| short   | 2     | [-32K, 32K] |
| int     | 4     | [-2B, 2B]   |
| long    | 8     |             |
| float   | 4     |             |
| double  | 8     |             |
| char    | 2     | A,B,C...    |
| boolean | 1     | true/false  |

### Int

```java
package com.quickstart;

public class HelloWorld {

    public static void main (String[] args) {
        int age = 30;
        System.out.println(age);
    }
}
```

### Long

```java
    public static void main (String[] args) {
        // 默认是整型，因此需要给大数加上l后缀，大小写均可，一般是用大写的
        long viewsCounts1 = 3_123_456_789l;
        long viewsCounts2 = 3_123_456_789L;
    }
```

### Float

```java
    public static void main (String[] args) {
        // 默认是双精度整型，因此需要给大数加上F后缀，大小写均可
        float price = 10.99F;
    }
```

### Char

```java
    public static void main (String[] args) {
        // char 只能用于存储单个字符，并且总是以单引号包裹
        char singleChar = 'A';
        // error 需要转换成 string 类型
        char multiChar = "ABCD";
    }
```

## 引用类型 Refernce

### 基础示例

```java
package com.quickstart;

import java.util.Date;

public class HelloWorld {

    public static void main (String[] args) {
        Date utilDate = new Date();
        // Wed Apr 06 14:44:40 CST 2022
        System.out.println(utilDate);
    }
}
```

### String

总是使用双引号，单引号会被认为是 char 类型

```java
package com.quickstart;

public class HelloWorld {

    public static void main (String[] args) {
        // 字面量形式声明即可
        String str = "HelloWorld";
        // warn: new String is redundant
        String str2 = new String("HelloWorld");

        String message = str + "!!";
        System.out.println(message.endsWith("!!")); // true
    }
}

```

加号一样可以用于拼接字符串，转义字符与 JS 一致

虽然 String 是引用类型，但是 String 的方法和 JS 一样都不会改变原字符串，而是会返回一个新的字符串。因为 String 在 java 是 immutable 的

通过 char 数组声明字符串

```java
char[] fruits = {'A', 'B', 'C'};
System.out.println(fruits);
```

因为数组的内存地址是连续的，所以理论来上来说和 String 是差不多的？ 可能是占位 0 的个人不一样

### Array

```java
    public static void main (String[] args) {
        // length
        int[] nums = new int[5];
        nums[0] = 1;
        nums[1] = 2;

        System.out.println(nums); // [I@1b2c6ec2
    }
```

JS 恶臭的地方开始出现了，函数喜欢耦合性的重载

```java
package com.quickstart; 

import java.util.Arrays;

public class HelloWorld {

    public static void main (String[] args) {
        int[] nums = new int[5];
        nums[0] = 1;
        nums[1] = 2;

        System.out.println(nums); // [I@1b2c6ec2
        System.out.println(Arrays.toString(nums)); // [1, 2, 0, 0, 0]
    }
}
```

 int array 所有 item 被初始化为 0，boolean array 所有 item 被初始化为 false

java 的数组长度是固定的，一旦创建就没有办法添加或者删除元素

```java
package com.quickstart;

import java.util.Arrays;

public class HelloWorld {

    public static void main (String[] args) {
        int[] nums = {1,2,3,4,5};

        System.out.println(nums); // [I@1b2c6ec2
        System.out.println(Arrays.toString(nums)); // [1, 2, 0, 0, 0]
    }
}
```

字面量形式为什么是花括号？ 有点恶臭，字面量形式本质上只是一个语法糖

```java
// Array initializer is not allowed here
System.out.println({1,2,3,4,5})
```

多维数据

```java
package com.quickstart;

import java.util.Arrays;

public class HelloWorld {

    public static void main (String[] args) {
        int[][] matrix23 = new int[2][3];

        int[][] matrix22 = {
                {1,2},
                {3,4}
        };

        matrix23[0][0] = 1;
        // [[I@1b2c6ec2, [I@4edde6e5]
        System.out.println(Arrays.toString(matrix23));
        // [[1, 0, 0], [0, 0, 0]]
        System.out.println(Arrays.deepToString(matrix23));
    }
}
```

## 原始类型和引用类型的区别

### 值传递

```java
    public static void main (String[] args) {
        byte x = 1;
        byte y = x;
        x = 2;
        System.out.println(y); // 1
    }
```

### 引用传递

```java
package com.quickstart;

import java.awt.*;

public class HelloWorld {

    public static void main (String[] args) {
        Point point1 = new Point(1,1);
        Point point2 = point1;
        point1.x  = 2;
        System.out.println(point2.x);
    }
}
```

原始类型不包含任何方法，不可以在原始类型上直接调用

其实 JS 也是的？ 只不过装箱和拆箱的过程是引擎帮我们操作了

### 内存分配

也是和 JS 一样

## 常量 Immutable

```java
    public static void main (String[] args) {
        final float PI = 3.14F;

        PI = 1; // error can not assign
    }
```

# 类型转换

## 兼容类型 Compatible

### 隐式转换 Implicit Casting

 byte > short > int > long > float > doubleC

```java
    public static void main (String[] args) {
        short x = 1;
        int y = x + 2;
        System.out.println(y); // 3
    }
```

short 2 个字节，int 4 个字节，java 自动做了类型转换，转换链不换造成 data lost

隐式转换 implicit casting implicit conversion

### 显式转换 Explicit Casting

```java
    public static void main (String[] args) {
        double x = 1.1;
        // 显式转换，声明 int，丢失 0.1
        int y = (int)x + 2;
        System.out.println(y); // 3
    }
```

显式转换只能发生在兼容类型之间，我们无法转换 String 通过运算得到 int

```java
    public static void main (String[] args) {
        String x = "1.1";
        int y = (int)x + 2; // error
        System.out.println(y);
    }
```

### 类型断言

java 不需要类型断言，兼容类型的类型转换同时起到了类型断言的作用，不光是类型，真实的值也会被改变

```java
(short)(expression)
```

## 包装类型.类型转换函数

```java
    public static void main (String[] args) {
        String x = "1.1";
        int y = Integer.parseInt(x) + 2;
        System.out.println(y); // 3
    }
```

# 运算符

相对于 js 的主要特点就是不会有奇奇怪怪的自动转换类型？

## 算术运算符

### 除法

```java
    public static void main (String[] args) {
        int result = 10 / 3;
        System.out.println(result); // 3
    }
```

int 类型运算只能得到 int 类型

```java
package com.quickstart;

public class HelloWorld {

    public static void main (String[] args) {
        double result = (double)10 / (double)3;
        System.out.println(result); // 3.3333333333333335
    }
}
```

```java
package com.quickstart;

public class HelloWorld {

    public static void main (String[] args) {
        double result = 10 / 3;
        System.out.println(result); // 3.0
    }
}
```

## 一元运算符

## 逻辑运算符 Logical Operators

同样不会有类型转换，逻辑运算符只能作用于 boolean 类型

所以 JS 的 xx && func() 条件执行，以及 xx || defaultValue ，默认值都无法使用了

## 比较运算符 Comparison Operators

只有同类型的能比较？兼容类型之间能比较

不会发生类型转换？非兼容类型之间无法比较，也不会有非兼容类型的类型转换

没有全等？没有全等

字符串可以使用 == 进行比较，判断是否相等，

## Ternary Operator

# 流程控制语句

## If Statements

if Statements 大括号内存在类似于块级作用域的概念，和 JS 类似。也是必须要加大括号才可以声明变量

如果 clause 内部不需要声明局部变量，可以省略大括号，也是和 JS 类似的。

## Switch Statements

## For Loop

## While Loop

## Do While Loop

## For Each Loop

java 没有 forof 循环，但是 for 循环直接承担了类似的功能，此时称之为 for each loop

```java
package com.quickstart;

public class HelloWorld {

    public static void main (String[] args) {
        String[] fruits = {"Apple", "Mango", "Orange"};

        for (String fruit : fruits) {
            System.out.println(fruit);
        }
    }
}

```

# 常用 Package

java 没有 BOM 的概念，但是其实就是一些运行时提供的默认函数，可以供我们调用

## Out

console.log => System.out.println();

snippets sout

this method will auto call the toString method of parameter

## NumberFormat

抽象类，工厂方法

### getCurrency

### getPercentInstance

```java
package com.quickstart;

import java.text.NumberFormat;

public class HelloWorld {

    public static void main (String[] args) {
        NumberFormat currency = NumberFormat.getPercentInstance();
        String restult = currency.format(123456789);
        System.out.println(restult);
    }
}
```

## Math

## Scanner

```java
package com.quickstart;

import java.util.Scanner;

public class HelloWorld {

    public static void main (String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.print("Name: ");
        String name = scanner.nextLine();
        System.out.println("your ara" + name);
    }
}
```

# 构建

## Jar

java archive 打包成命令行程序

# 类型系统

通过 var 关键字声明的变量，可以获得类型推断，就不用每次都反复写类型了

# 类 Class

通过 package.class.method 访问，如果是在类的内部，package 和 class 都可以省略

java 也有 this ，可以在变量名重复的时候引用到 class field or class method

this 也是可以省略的

## 定义类

java 每个类都会放在不同的文件中，JS 也有类似的 ESLint 规则

```java
package com.quickstart;

public class TextBox {
    String text = "";

    public void setText(String text) {
        this.text = text;
    }

    public void clear() {
        text = "";
    }
}
```

如果没有赋予默认值，字段会被初始化为 null

NullPointerException java 最常见的异常

## 使用类创建实例

```java
package com.quickstart;

public class HelloWorld {

    public static void main (String[] args) {
        var textBox1 = new TextBox();

        textBox1.setText("var keyword is totally different to js");
        System.out.println(textBox1.text.toUpperCase());
    }
}
```

至少同一个包下的类，可以直接引用

## 封装 Encapsulation

bundle the data and methods that operate on the data in a single unit

```java
package com.quickstart;

public class HelloWorld {

    public static void main (String[] args) {
        int baseSalary = 50_000;
        int extraHours = 10;
        int hourlyRate = 20;

        var wage = calculateWage(baseSalary, extraHours, hourlyRate);
        System.out.println(wage);
    }

    public static int calculateWage(
            int baseSalary,
            int extraHour,
            int hourlyRate
    ) {
        return baseSalary + (extraHour * hourlyRate);
    }
}
```

新建一个 empolyee class

```java
package com.quickstart;

public class Employee {
    public int baseSalary = 0;
    public int hourlyRate = 0;

    public int calculateWage(int extraHours) {
        return baseSalary + (extraHours * hourlyRate);
    }
}
```

```java
package com.quickstart;

public class HelloWorld {

    public static void main (String[] args) {

        var employee = new Employee( );
        employee.baseSalary = 50_000;
        employee.hourlyRate = 20;

        var wage = employee.calculateWage(10);
        System.out.println(wage);
    }

}
```

### 通过 Setter 封装数据验证逻辑

上面的代码有一个问题，就是没有数据验证的逻辑

```java
package com.quickstart;

public class Employee {
    private int baseSalary = 0;
    public int hourlyRate = 0;

    public void setBaseSalary(int baseSalary) {
        if (baseSalary <= 0) {
            throw new IllegalArgumentException("baseSalary can not be negetive");
        }
        this.baseSalary = baseSalary;
    }

    public int getBaseSalary() {
        return baseSalary;
    }

    public int calculateWage(int extraHours) {
        return baseSalary + (extraHours * hourlyRate);
    }
}
```

![image-20220419160557389](/img/user/programming/language/java-basic/image-20220419160557389.png)

![image-20220419160717412](/img/user/programming/language/java-basic/image-20220419160717412.png)

## 抽象 Abstracion

Reduce complexity by hiding unnecessary details

## Reduce 耦合 Coupling

耦合就是依赖，只要一个类依赖了另一个类就存在耦合，所以一个类的方法如果是没有必要暴露的都应该通过 private 修饰一下

私有方法可以保证修改的时候只需要修改 class 内部即可

减少耦合的可能性

## Constructor

### Concept

类似于 JS，当没有显式 constructor 时，JVM 会为我们自动创建一个 constructor，and then initialize class flied to 0 \ false \ null etc.

some difference to js , in java , a constructor function must named the same as the class name

```java
public class Employee {
    private int baseSalary = 0;
    private int hourlyRate;

    public Employee(int baseSalary, int hourlyRate) {
        setBaseSalary(baseSalary);
        setHourlyRate(hourlyRate);
    }
}
```

### Constructor Overloading

```java
public class Employee {
    private int baseSalary = 0;
    private int hourlyRate;

    public Employee(int baseSalary, int hourlyRate) {
        setBaseSalary(baseSalary);
        setHourlyRate(hourlyRate);
    }
    public Employee(int baseSalary) {
//        setBaseSalary(baseSalary);
//        setHourlyRate(0);
        this(baseSalary, 0);
    }
}
```

## Method Overload

create different implementations of a method width different parameters.

unfortunately java can not set a default value right after the parameters. so the only way to one or more parameters optional is by overloading a method

it's often better to overload a method if you're dealing width completely different types of parameter

```java
    public int calculateWage(int extraHours) {
        return getBaseSalary() + (extraHours * getHourlyRate());
    }
    public int calculateWage() {
        return calculateWage(0);
    }
```

in java you can use a overload method in overload implementation

> is better to extract a common method to implement method overload
>
> from above all, we can tell JS don't has method overload. Although, JS can simulate method overload by closure and arguments.

## Static Keyword

a class can has two types of member, instance member, static member

To enable java runtime to directly call main method without having to create a new object. so the main method is always declared as static

# 继承 Inheritance

## The Object Class

every class that we declare directly or indirectly inherits from the object class.

### object.hashCode()

return the value base on the address of this object in memory: `hascode = hash(object.address)`

we can use the hashCode to check object is equal or not

> object.equals use hashCode to compare object

## Constructor

use super keyword to reference parent class

super() method can only be invoke in the first line of sub class constructor

## Access Modifier

public private protected are the same as js.

but int java have the concept package, if a class filed is protected it can be access by the sub class in another package, so in java protected is deprecate to use

if we do not use any access modifier, in default we will get package private , which mean this filed is public anywhere inside this package but private for other package

we should not use default modifier just as protected, it is hard to distinguish the package concept. just use public and private

## Overriding Methods

## Upcasting and Downcasting

Upcasting: casting an object to one of its super types

Downcasting: casting an object ot one of its sub types

```java
public class HelloWorld {

    public static void main (String[] args) {
        var control = new UIControl(true);
        var textBox = new TextBox();

        show(control);
        // textBox upcasting to control class
        // in compile we get a control class, but in runtime overriding method will be invoked
        show(textBox); // "defaultText"

    }

    public static void show(UIControl control) {
        System.out.println(control);

        // exception so we muse ensure is textBox in runtime
        var textBox = (TextBox)control;
        textBox.setText("Hello world");
        System.out.println(textBox);
    }
}
```

## Comparing Object

a little bit confused

```java

public class Point {
    private int x = 0;
    private int y = 0;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    // <C-CR> auto override, but I think override equals and hashcode is a little confused
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Point point = (Point) o;
        return x == point.x && y == point.y;
    }

    @Override
    public int hashCode() {
        return Objects.hash(x, y);
    }
}
```

## 多态 Polymorphism

poly = many

morph = form

allow objects to take difference form

use `ctrl/cmd + <CR>` to auto override

## Abstract Class and Methods

some abstract concept should not initialize

also we can add abstract keyboard in front of a methods that will force every sub class to implement this method

```java
public abstract class UIControl {
    public abstract void render();
}

// Slider must must either be declared abstract or implement abstract method 'render()' in 'UIControl'
public class Slider extends UIControl {}
```

## Final Class and Methods

when we declare a final class we can't extends it anymore

we can't extends String class because it is declared as final

similarly we can't override a final method

## Depp Inheritance Hierarchies

Don't create deep inheritance Hierarchies

# Interfaces

Interfaces which are one of the most powerful yet least understood construct in java.

the meaning of interface has change since java8

> but it is similar with typescript

## Programing Against Interfaces

![](/img/user/programming/language/java-basic/2022-05-26-11-08-26.png)

we don't want class a to directly dependent or coupled to class b

![](/img/user/programming/language/java-basic/2022-05-26-11-09-24.png)

to minimize the impact of changing class b , we put an interface in between class a and b to decoupled them

now change the code in class b , class a will not be affected, because it knows nothing about b

we code our class to work with interfaces, not concrete implementation

## Interface Name Prefix

TaxCalculator

CanTaxCalculator

xxable: Dragable、resizable

## Implement Interface with Override

```java
public interface TaxCalculator {
    double calculateTax();
}

public class TaxCalculator2018 implements TaxCalculator{
    private double taxableIncome;

    public TaxCalculator2018(double taxableIncome) {
        this.taxableIncome = taxableIncome;
    }

    // Override annotation can also use in interface
    // when we delete calculateTax method in our interface we will get a compile error
    @Override
    public  double calculateTax() {
        return taxableIncome * 0.3;
    }
}
```

## Dependency Injection

Our Classes should not instantiate their dependencies

instantiate a class and use a class are two different concern, this is what we call the separation of concerns

> in my oricanon server I should replace class type with interface in service dependency , and then let dependencies implement that interface.
>
> actually, in typescript a class is also has the function of interface, so as long as the shape of your class is the same, there is no need to wrap the class with a interface to decoupled

### Constructor Injection

```java
public class TaxReport {
    private TaxCalculator calculator;

    public TaxReport(TaxCalculator calculator) {
        this.calculator = calculator;
    }

    public void show() {
        var tax = calculator.calculateTax();
        System.out.println(tax);
    }
}

public class HelloWorld {
    public static void main (String[] args) {
        TaxCalculator2018 calculator = new TaxCalculator2018(1000);
        var report = new TaxReport(calculator);
    }
}
```

in this implementation TaxReport class known nothing about TaxCalculator2018, it only known is the TaxCalculator interface

when we change the implementation of TaxCalculator2018, TaxReport will no recompile, because it does't couple with the concrete implementation of TaxCalculator2018. This is loose coupling in action.

### Setter Injection

```java
public class TaxReport {

    private TaxCalculator calculator;

    public TaxReport(TaxCalculator calculator) {
        this.calculator = calculator;
    }

    public void show() {
        var tax = calculator.calculateTax();
        System.out.println(tax);
    }
    // a setter for us to change dependency
    public void setCalculator(TaxCalculator calculator) {
        this.calculator = calculator;
    }

}

public class HelloWorld {

    public static void main (String[] args) {
        TaxCalculator2018 calculator = new TaxCalculator2018(1000);
        var report = new TaxReport(calculator);
        report.show();

        // change the dependency dynamically throughout the lifetime of our application
        report.setCalculator(new TaxCalculator2019(1000));
        report.show();
    }
}
```

### Method Dependency Injection

```java
public class TaxReport {
    public void show(TaxCalculator calculator) {
        var tax = calculator.calculateTax();
        System.out.println(tax);
    }
}

public class HelloWorld {

    public static void main (String[] args) {
        TaxCalculator2018 calculator = new TaxCalculator2018(1000);
        var report = new TaxReport();
        report.show(calculator);
        report.show(new TaxCalculator2019(1000));
    }
}
```

## Interface Segregation Principle

divide big interfaces into smaller ones

throughout DI, we get rid of coupling with class, but we still couple with interface, when we change the interface, all the class implement it must be recompile.

so, keep the interface light and straight, so that we will not change it frequently

## Unit Test Against Interface

In unit test, we can create a skeleton class that just has the interface implementation. With that we do not have to interact with real database, server etc.

## When to Create an Interface for a Class

you might want to decouple a class from it's dependencies, and this gives you a number of benefits

1. easily replace implementation from one to another
2. unit test
3. the others can expand your applications against the interface without concrete implementation

## Some Bad Feature After Java8

> ts doesn't has these bad feature

### Declare Fields in Interface

constant has no place in interfaces, constant is an implementation detail should not declare in a interface.

```java
public interface Resizable {
    // don't declare constant in interface
    double size = 10;
    void resize();
    double test();
}
```

### Declare Static Method in Interface

```java
public interface Resizable {
    double size = 10;
    void resize();
    // don't contain implementation in interface
    static double test() {
        return 0;
    }
}
```

interface don't care about how. All reuseable implementation just encapsulate with class

### Declare Private Method in Interface

# FAQ

## Class Inheritance Vs Interface Implementation

what's the deference ? concrete implementation

![](/img/user/programming/language/java-basic/2022-05-26-11-21-09.png)

### Multiple Inheritance

C++ has this feature, but java doesn't support it

![](/img/user/programming/language/java-basic/2022-05-26-10-55-58.png)

which toString implementation should user inherit ? it's ambiguous

the critical part is a class has concrete implementation but a interface not.

if two parent class declare the same method with the same signature, it is ambiguous to tell which implementation will be inherit.

when two interface declare the same method with the same signature, that two method technically is the same method.

if two method has difference signature, that will both work in class extends and interface, it can be treat as method overloading.

Still, there is one situation that multiple interface will come up and error. that is when two method have the same signature except for return type. just like an error overloading, JVM will come up an error.

> further more think about private keyword. <https://developer.mozilla.org/zh-CN/docs/Glossary/Signature/Function>

### Interfaces Vs Abstract Classes

with some bad feature be include in java, a lot of people treat interface as an multiple inheritance solution in java. It is horrible.

![](/img/user/programming/language/java-basic/2022-05-26-20-12-07.png)

# Exceptions

## Concept

## Type of Exceptions

### Checked

get checked at compile time

### Unchecked / Runtime Exception

runtime exceptions

+ NullPointerException
+ ArithmeticException: divide a value by zero
+ IllegalArgumentException
+ IndexOutOfBoundsException
+ IllegalStateException: which gets thrown if we tried to call a method, but the underlying object is not in the right state.

> enum about object state

### Error

indicate an error external out our application:

+ stack overflow
+ out of memory

### Error Class Hierarchy

Throwable class: define common part of all error, like an error message and the stacktrace

Error class: external errors

Exception class: parent of checked and unchecked exception

RuntimeException class: unchecked exception

![](/img/user/programming/language/java-basic/2022-05-27-15-46-47.png)

## Handling Exceptions

### Try-catch

```java
public class ExceptionDemo {
    public static void show() {
        try{
            var reader =  new FileReader("file.txt");
            System.out.println("File Opened");
        }
        catch (FileNotFoundException ex) {
            System.out.println("File not exist");
        }
    }
}
```

## Custom Exceptions

## Chaining Exceptions
