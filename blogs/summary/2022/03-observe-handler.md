---
title: 观察者模式与事件处理
date: 2022-09-11
tags:
 - 设计模式
categories:
 - 学习总结
---

## 观察者模式简介

观察者模式用于建立对象之间一对多的依赖关系，当一个对象状态发生改变，其他对象得到通知并做出响应。在观察者模式中，我们将状态发生改变的对象称为观察目标，将被通知的对象称为观察者。

![image-20220911210912102](http://image.xiaobailx.top/images/202209112109175.png)

观察者模式中包括四大核心要素：观察目标、观察者、具体目标、具体观察者。

- 观察目标：也称主题，可以是接口、抽象类或具体类，定义和实现了观察者集合和对观察者添加、移除方法，声明了抽象通知方法。
- 观察者：也称抽象观察者，可以为接口或抽象类，声明了状态更新方法。
- 具体目标：观察目标的子类，定义了目标包含状态，实现了观察目标通知方法的抽象业务逻辑。
- 具体观察者：观察者的实现类，包含了具体目标的引用，实现了观察者定义的状态更新方法。

以下这段代码是对观察者模式结构的简要说明：

- 观察目标——Subject

```java
public abstract class Subject {

    protected List<Observer> observerList;

    Subject(){
        observerList = new ArrayList<>();
    }

    protected void  addObserver(Observer observer){
        observerList.add(observer);
    }

    protected void removeObserver(Observer observer){
        if (!observerList.isEmpty()){
            observerList.remove(observer);
        }
    }

    public abstract void notifyObservers();
}
```

- 观察者——Observer

```java
public interface Observer {
    void update();
}
```


- 具体目标——ConcreteSubject

```java
public class ConcreteSubject extends Subject{
    private String subjectState;

    public String getSubjectState() {
        return subjectState;
    }

    public void setSubjectState(String subjectState) {
        this.subjectState = subjectState;
    }

    @Override
    public void notifyObservers(){
        for (Observer obj:observerList){
            obj.update();
        }
    }
}
```

- 具体观察者——ConcreteObserver


```java
public class ConcreteObserver implements Observer{
    @Override
    public void update() {
        System.out.println(this.toString()+"状态更新了!");
    }
}
```

- 代码测试——TestObserver

```java
public class TestObserver {
    public static void main(String[] args) {
        Subject subject = new ConcreteSubject();
        for (int i = 0; i < 3; i++) {
            subject.addObserver(new ConcreteObserver());
        }
        subject.notifyObservers();
    }
}
```



## 模式应用实例

需求：联机对战游戏中，多个玩家同时加入一个战队。当战队中的某一队员收到敌人攻击时将通知其他队友并做出响应。

- 观察目标——GameCenter

```java
/**
 * 指挥中心
 * */
public class GameCenter {
    //指挥中心名称
    private String centerName;
    //队员
    List<GamePlayer> playerList;

    public GameCenter(){
        playerList = new ArrayList<>();
    }

    public GameCenter(String groupName){
        this.centerName = groupName;
        playerList = new ArrayList<>();
    }
    //通知
    public void notifyPlayers(String name){
        System.out.println(centerName+"紧急通知,盟友"+name+"遭受敌人攻击,请火速支援");
        for (GamePlayer player: playerList) {
            if (!player.getName().equals(name)){
                player.help();
            }
        }
    };

    public void addPlayer(GamePlayer player){
        System.out.println(player.getName()+"加入战队");
        playerList.add(player);
    }

    public void removePlayer(GamePlayer player){
        System.out.println(player.getName()+"退出战队");
        if (!playerList.isEmpty()) {
            playerList.remove(player);
        }
    }

    public String getCenterName() {
        return centerName;
    }

    public void setCenterName(String centerName) {
        this.centerName = centerName;
    }
}
```

- 观察者——GamePlayer

```java
/**
 * 游戏玩家
 * */
public class GamePlayer {
    //队员名称
    private String name;

    public GamePlayer(String name) {
        this.name = name;
    }
    //被攻击
    public void beAttacked(GameCenter center){
        System.out.println(name+"遭受攻击,请求支援请求支援!");
        center.notifyPlayers(name);
    };
    //支援
    public void help(){
        System.out.println(name+"收到!火速前往支援!");
    };

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

```

- 客户端——TestObserverSub

```java
public class TestObserverSub {
    public static void main(String[] args) {
        GameCenter center = new GameCenter("西游战队");
        GamePlayer wuKong = new GamePlayer("齐天大圣-孙悟空");
        GamePlayer baJie = new GamePlayer("天蓬元帅-猪八戒");
        GamePlayer wuJin = new GamePlayer("卷帘大将-沙悟净");

        center.addPlayer(wuKong);
        center.addPlayer(baJie);
        center.addPlayer(wuJin);

        wuJin.beAttacked(center);
    }
}
```

输出：

![image-20220712150957109](http://image.xiaobailx.top/image-20220712150957109.png)



## Java 事件处理

Java 的事件处理模型采用了基于观察者模式的委派事件模型（DEM），即一个组件所引发的事件委派给事件处理对象负责。

在 DEM 模型中，目标角色负责发布事件，而观察者可以向目标订阅其所感兴趣的事件。我们将事件的发布者称为事件源，订阅者称为事件监听器，过程中通过事件对象来传递事件相关信息。事件源、事件监听器、事件对象构成了 Java 事件处理模型的三要素。事件源充当观察目标，事件监听器充当观察者，事件在目标和观察者之间传递数据。

以下代码是 DEM 的简化模型：

- 事件对象——Event

```java
/**
 * 自定义事件对象
 * */
public class Event {

    private String eventName;

    public Event() {
    }

    public Event(String eventName) {
        this.eventName = eventName;
    }

    public String getEventName() {
        return eventName;
    }

    public void setEventName(String eventName) {
        this.eventName = eventName;
    }

}
```

- 观察者事件监听器——EventListener

```java

/**
 * 自定义事件监听器
 * */
public interface EventListener {

    void onEvent(Event event);
}

```

- 观察目标事件源——EventSource

```java
/**
 * 自定义事件源
 * */
public class EventSource {
    //事件源名称
    private String sourceName;
    //事件
    private Event event;
    //事件监听器
    private EventListener listener;
    //用于触发事件
    public void fireEvent(Event event){
        this.event = event;
        listener.onEvent(event);
    }

    public EventSource() {
    }

    public EventSource(String sourceName) {
        this.sourceName = sourceName;
    }

    public EventSource(EventListener listener) {
        this.listener = listener;
    }

    public String getSourceName() {
        return sourceName;
    }

    public void setSourceName(String sourceName) {
        this.sourceName = sourceName;
    }

    public EventListener getListener() {
        return listener;
    }

    public void setListener(EventListener listener) {
        this.listener = listener;
    }

    public Event getEvent() {
        return event;
    }

    public void setEvent(Event event) {
        this.event = event;
    }
}
```

- 客户端——TestDemo

```java

public class TestDemo {

    public static void main(String[] args) {
        //创建一个事件源
        EventSource source = new EventSource("SOURCE");
        //设置监听器
        source.setListener(new EventListener() {
            @Override
            public void onEvent(Event event) {
                System.out.println(event.getEventName()+"被触发!");
            }
        });
        //触发一个点击事件
        source.fireEvent(new Event("clickEvent"));

    }

}
```

![image-20220712152934167](http://image.xiaobailx.top/image-20220712152934167.png)



参考书籍：《Java设计模式》/ 刘伟 编著 | 清华大学出版社
