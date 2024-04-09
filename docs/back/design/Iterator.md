---
title: 迭代器模式
author: 韭菜
date : 2024-04-09
tag : 设计模式
---

# 迭代器模式（Iterator Pattern）
    迭代器模式又称游标（Cursor）模式，是一种对象行为模式 用于对一个聚合对象进行遍历
    通过引入迭代器模式，将遍历功能从对象中分离出来 ，从而达到，聚合对象值负责存储数据，而遍历数据由迭代
    器完成，更符合"单一职责"的要求

![迭代器模式类图](image\迭代器模式.png)

### 出现的角色

* **TVIterator(Iterator)** 迭代器
 负责定义接口，其中包括但不限于
  - next 指针后移
  - pre 指针前移
  - hasNext 是否存在下一个元素


* **SkyWorthIterator(ConcreteIterator)** 具体的迭代器，包括遍历集合所需要的具体信息 currentIndex表示当前遍历的下标


* **TV(Aggregate)** 需要被遍历的聚合对象 需要有获取迭代器的方法


* **SkyWorthTV(ConcreteAggregate)** 具体的聚合对象
### 优点
* 支持以不同的方式，遍历同一个聚合对象
* 简化了聚合类
* 引入了抽象层， 新增聚合类和迭代器类都很方便，符合开闭原则
### 缺点
* 在增加新的聚合类的时候，需要相应的增加迭代器类，增加了系统的复杂性
* 抽象迭代器的设计难度较大，需要充分考虑到未来的扩展，创建一个考虑全面的抽象迭代器不是一件容易得事
### 应用
java 1.2中 引入了新的聚合框架Collection ，其中就广泛用到了迭代器模式。详情之后会另开一篇文章

### 代码实现

Iterator
```java
public interface TVIterator {

     /*设置频道*/
     void setChannel(int i);
     
     /*返回当前频道*/
     Object currentChannel();
     
     /*下一个频道*/
     void next();
     
     /*上一个频道*/
     void previous();
     
     /*是否是最后一个频道*/
     boolean isLast();
     
     /*是否是第一个频道*/
     boolean isFirst();
}
```

ConcreteIterator实现
```java
public class SkyWorthIterator implements TVIterator{

    private int currentIndex = 0;
    
    //聚合对象，通过tv访问具体的集合信息 ，也可以通过内部类实现
    public SkyWorthTV tv;

    public SkyWorthIterator(SkyWorthTV tv){
        this.tv = tv;
    }

    @Override
    public void setChannel(int i) {
        this.currentIndex = i;
    }

    @Override
    public Object currentChannel() {
        return tv.obj[currentIndex];
    }

    @Override
    public void next() {
        currentIndex ++;
    }

    @Override
    public void previous() {
        currentIndex --;
    }

    @Override
    public boolean isLast() {
        return tv.obj.length-1 == currentIndex;
    }

    @Override
    public boolean isFirst() {
        return currentIndex == 0;
    }

}

```

Aggregate实现
```java
public interface TV {
    /*获取迭代器*/
    TVIterator createIterator();
}
```

ConcreteAggregate实现

```java
public class SkyWorthTV implements TV {
   //集合
    protected Object[] obj = {"sky1", "sky2", "sky3"};

    @Override
    public TVIterator createIterator() {
        return new SkyWorthIterator(this);
    }
}
```

主函数

```java
public class Main {
    public static void main(String[] args) {
        TVIterator tvIterator;
        TV tv = new SkyWorthTV();
        tvIterator = tv.createIterator();
        System.out.println("是第一个吗："+tvIterator.isFirst());
       while(!tvIterator.isLast()){
           System.out.println(tvIterator.currentChannel());
           tvIterator.next();
       }
        System.out.println("是最后一个吗："+tvIterator.isLast());
        System.out.println(tvIterator.currentChannel());
    }
}
```


