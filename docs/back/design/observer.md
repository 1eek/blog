---
publish: true
date : 2024-04-09 16:04:00
tag : 设计模式
---
<!-- markdownlint-disable-file -->


# 观察者模式

    又称为 发布/订阅模型 属于对象行为型模式

## 模式动机

- 当一个对象的状态或行为发生变化时，可能会导致其他对象的状态或行为也发生改变，他们之间将产生联动
- 发生改变的对象称为观察目标，被通知的对象的称为观察者
- 一个观察目标对应多个观察者
- 需要用户自定义何时执行notify操作

## 优点

- 可以实现表示层和逻辑层的分离
- 支持广播通信，简化了一对多系统设计的难度
- 符合开闭原则，增加新的观察者无需修改原有代码

## 缺点

- 所有的观察者都通知到会花费**很多时间**
- 如果存在**循环依赖**会导致系统崩溃
- 观察者不知道 **目标发生变化的原因**，只知道发生了变化

## 代码实现

案例背景：
某在线股票系统需要提供以下功能：当股票购买者所购买的某只股票价格为5元时，系统
将自动发送通知（包括新价格）给购买该股票的股民

![观察者模式](.\image\观察者模式.png)

Subject 实现，发布者 也就是被观察者

```java
import java.util.ArrayList;

public  class Subject {
    /*观察者*/
    protected ArrayList<Investor> investors = new ArrayList<Investor>();

    /*新增观察者*/
    public void attach(Investor investor){
        investors.add(investor);
    }
    /*删除观察者*/
    public void detach(Investor investor){
        investors.remove(investor);
    }
    /*通知所有观察者*/
    public void notifyInvestor(){
        investors.forEach(Investor::update);
    }
}
```

Stock 发布者的具体实现类

```java
public class Stock extends Subject {
    private String stockName;
    private Double Price;

    public Double getPrice() {
        return Price;
    }

    public void setPrice(Double price) {
        Price = price;
        
        /*这段逻辑也可以写到其他地方
        *
        * 这里就说明了 何时进行通知，需要自定义，从而可以实现不同情况进行不同通知
        */
        
        if(price==5){
            //通知所有观察者
            System.out.println(stockName+"价格达到5元，通知所有观察者");
            notifyInvestor();
        }else{
            System.out.println("当前股票价格"+price);
        }
    }

    public String getStockName() {
        return stockName;
    }

    public void setStockName(String stockName) {
        this.stockName = stockName;
    }
}
```

Investor 观察者/订阅者

```java
public interface Investor {

    /*对于每个观察者，当收到通知后，执行不同的逻辑*/
    void update();
}
```

两个实现类 相当于两个股民

```java
public class ConcreteInvestor2 implements Investor{
    @Override
    public void update() {
        System.out.println("股民二收到股票报价:继续观望");
    }
}

public class ConcreteInvestor implements Investor {

    @Override
    public void update() {
        System.out.println("股民一收到股票报价：将股票抛出");
    }
}
```

测试类/主类

```java
public class Main {
    public static void main(String[] args) {
        Stock stock = new Stock();
        stock.setStockName("飞天茅台");
        stock.attach(new ConcreteInvestor());
        stock.attach(new ConcreteInvestor2());
        
        //模拟股票价格从0-5
        for(int i =0;i<6;i++){
           stock.setPrice((double) i);
       }
       
    }
}
```
