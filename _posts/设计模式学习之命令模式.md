---
title: 设计模式学习之命令模式
date: 2017-03-04 14:53:28
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/command-pattern.png
tags:
	- 命令模式
	- 设计模式
categories:
	- 设计模式
keywords:
	- 命令模式
	- 设计模式
---
## 什么是命令模式
**命令模式(Command Design Pattern)**`将“请求”封装成对象，以便使用不同的请求、队列或者日志来参数化其他对象。命令模式也支持可撤销的操作。`

## 将封装带入一个新的境界
在本文中，我们将介绍命令模式。这个模式有多大的本事呢？通过使用该模式我们能将**方法调用（Method Invocation）**进行封装。没错，就是方法调用。我们可以将运算块包装成形。调用这个运算的对象根本就不需要关心这个运算块是如何执行的，它只需要知道如何使用封装运算块的方法即可。通过封装方法调用，我们可以做一些很Smart的事情，比如实现记录日志，操作的回滚等。

## 命令模式类图
![命令模式类图](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/command-01.png)

`Client`客户端负责创建一个`ConcreteCommand`，并设置它的的接收者。

`Invoker`调用者持有一个命令对象，并且在某个时间点调用命令对象的`execute()`方法，请求付诸实行。

`Command`为所有的命令声明了一个接口。调用命令对象的`execute()`方法，就可以让接收者进行相关的动作。这个接口也同时具有一个`undo()`方法用于撤销操作。

`ConcreteCommand`定义了动作和接收者之间的关系。调用者只需要调用`execute()`方法就可以发出请求，然后由`ConcreteCommand`调用接收者的一个或者多个动作。

可以看到，上图中的`execute()`的具体实现已经给出，我们可以看到它这个方法调用了`receiver`接收者的动作方法`action()`，也就是说，正真执行具体动作的是接收者，而不是命令。**使用一句通俗的表示就是一个人`Client`拿起手中的遥控器`Invoker`点了一个播放按钮`ConcreteCommand`，然后电视机`Receiver`就开始播放视频了。**

## 实例分析-智能家居
随着现在互联网技术的快速扩张，从苹果的HomeKit到小米的智能家居我们可以知道，现在互联网企业不仅仅满足虚拟网络的互联了，他们开始关注起物联网来（Internet of Things）。本例不会涉及互联网的任何具体内容，只谈其中的一个功能。就是实现一个对整个智能家居的所有智能设备控制的软件，这里也可以叫它万能遥控器。利用它我们可以开门，可以启动空调，可以关闭电磁炉，可以打开电风扇，可以开灯关灯等等。

系统的结构简单的介绍一下：
![智能家居万能遥控器](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/command-03.png)

图有点老不要紧，我们的解释可以很时尚嘛！我们可以看到，图中的遥控器上面有7个插槽，分别已经对应到了不同的智能设备上面。另外在最底下还有一个**UNDO**的插槽。用于实现操作的撤销。

右下角大家注意到，遥控器点击了立体声（Stereo）系统的关闭功能，可以看到，命令具体执行还是由立体声（Stereo）系统本身来完成的。**那有人就问了，为啥不直接让客户端直接操作立体声系统，客观上来讲，这种可行性是有的。不过呢，封装这个请求有利于统一的管理，便于遥控器功能的拓展。比如某个设备更换了，那么遥控器可以立即更换相应的命令。如果不使用遥控器，就好比凡事都要你自己动身去操作，多麻烦！**

### 命令接口Command
接口只需要定义需要做什么事情就好了，Command接口的事情很简单，就两个：

- `execute()`执行某个请求
- `undo()`撤销某个请求

### 具体命令ConcreteCommand
到了具体命令这里情况稍微有点变化，我们都知道每一个具体的命令都对应着一个具体的智能设备，因此，除了具有上述的两个方法外，具体命令里面应该还需要包含一个智能设备。不然你没法执行`execute()`和`undo()`方法啊。
 
### 接收者Receiver
 接收者就是我们前面多次提到过的智能设备，比如电视机，立体声系统，空调等。它本身包含很多具体的功能，有很多不同的动作可以被执行，其中最基本的无非就是开机和关机。
 
 - `on()`开机
 - `off()`关机

### 调用者Invoker
这个就是遥控器啦，我们从前面的系统设计图上也可以看出，遥控器上设置了很多不同的命令，分别有不同的功能，那么，遥控器上我们需要拥有可以设置命令的地方。另外，设置了命令以后我们需要有撤销键，应该允许用户反悔啊！

- `setCommand()`
- `onButtonPressed()`
- `offButtonPressed()`
- `onButtonPressedUndo()`
- `offButtonPressedUndo()`

### 代码实现
 
Command

``` java
public interface Command {

    public void execute();

    public void undo();
}
```

ConcreteCommand（这里太多了，只举一个例子）

``` java
public class LightOffCommand implements Command {

    Light light;

    public LightOffCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.off();
    }

    @Override
    public void undo() {
        light.on();
    }
}
```

Receiver(太多了，也只举一个例子)

``` java
public class Light {

    private String type = "";

    public Light(String type) {
        this.type = type;
    }

    public void on() {
        System.out.println(type + " Light is on");
    }

    public void off() {
        System.out.println(type + " Light is off");
    }
}
```

Invoker

``` java
public class RemoteControl {

    Command[] onCommands;
    Command[] offCommands;

    public RemoteControl() {
        onCommands = new Command[4];
        offCommands = new Command[4];

        Command noCommand = new NoCommand();

        for (int i = 0; i < 4; i++) {
            onCommands[i] = noCommand;
            offCommands[i] = noCommand;
        }
    }

    public void setCommand(int slot, Command onCommand, Command offCommand) {
        onCommands[slot] = onCommand;
        offCommands[slot] = offCommand;
    }

    public void onButtonPressed(int slot) {
        onCommands[slot].execute();
    }

    public void offButtonPressed(int slot) {
        offCommands[slot].execute();
    }

    public void onButtonPressedUndo(int slot) {
        onCommands[slot].undo();
    }

    public void offButtonPressedUndo(int slot) {
        offCommands[slot].undo();
    }
}
```

测试一下

``` java
public class RemoteControlTest {

    public static void main(String[] args) {

        RemoteControl remoteControl = new RemoteControl();

        Light livingRoomLight = new Light("Living Room");
        Light kitchenLight = new Light("Kitchen");
        Stereo stereo = new Stereo();

        LightOnCommand livingRoomLightOn = new LightOnCommand(livingRoomLight);
        LightOffCommand livingRoomLightOff = new LightOffCommand(livingRoomLight);

        LightOnCommand kitchenLightOn = new LightOnCommand(kitchenLight);
        LightOffCommand kitchenLightOff = new LightOffCommand(kitchenLight);

        StereoOnWithCDCommand stereoOnWithCD = new StereoOnWithCDCommand(stereo);
        StereoOffCommand stereoOff = new StereoOffCommand(stereo);

        //plugin the commands
        remoteControl.setCommand(0, livingRoomLightOn, livingRoomLightOff);
        remoteControl.setCommand(1, kitchenLightOn, kitchenLightOff);
        remoteControl.setCommand(2, stereoOnWithCD, stereoOff);

        //start
        remoteControl.onButtonPressed(0);
        remoteControl.onButtonPressed(1);
        remoteControl.onButtonPressed(2);

        //stop
        remoteControl.offButtonPressed(0);
        remoteControl.offButtonPressed(1);
        remoteControl.offButtonPressed(2);

        remoteControl.offButtonPressedUndo(2);
    }
}

```

测试结果：

``` bash
Living Room Light is on
Kitchen Light is on
Stereo is on
CD is playing...
Volume is 11
Living Room Light is off
Kitchen Light is off
Stereo is off
Stereo is on
CD is playing...
Volume is 11
```

可以看出，系统工作良好，上面的代码我已经全部同步到Github上面去了，有需要的同学可以去我的Github仓库自行查看下载。

GitHub地址：[https://github.com/QinJiangbo/DesignPatterns/tree/master/src/com/qinjiangbo/command](https://github.com/QinJiangbo/DesignPatterns/tree/master/src/com/qinjiangbo/command)

## 命令模式的更多用途
命令模式除了可以实现上述的功能以外，其实大有作为，它还可以被应用在队列请求和日志请求上。

### 队列请求
想像有一个工作队列：你在某一端添加命令，然后另一端是线程。线程进行以下操作：从队列中取出一个请求，这里我们不讨论具体是哪种队列啦，不管是FIFO还是LIFO都OK。取出这个请求以后调用它的`execute()`方法，执行完毕以后将这个请求销毁。然后继续取出下一个请求，在执行相同的`execute()`方法，再销毁...

需要注意的是，工作队列类和执行的请求之间是完全解耦的，前一个请求是数据库请求，下一个请求就有可能是Http网络请求。但是不管是什么请求，只要它有`execute()`就成！这也是一个很经典的说法，就是`不管你是不是鸭子，只要你会咕咕叫，我就认为你是鸭子`。

### 日志请求
我们都知道日志系统不仅仅只是用来记录服务器到底发生了什么错误，它还有一个重大的作用就是实现系统的备份还原。具体怎么做呢，当我们执行命令的时候，我们将命令请求记录在磁盘上面，不管是以序列化的方式还是以文件存储的方式都可以。最好推荐以日志文件的方式。这些记录是什么呢，是我们每一次操作时的动作以及这个动作携带的数据参数。

这么做有一个好处，就是对于一些大型的数据系统，可能一条命令需要更新很多条记录，如果每一次都备份数据的话那简直不敢想象这个系统会卡成什么样子。但是记录动作的话情况会变得简单很多。**一旦系统发生宕机，我们便可以立即启动备份机制。将备份点（CheckPoint）之后的动作从头到尾依次执行一遍，那样所有的数据就全部回来啦。**

当然啦，定期备份数据也是非常重要的。这里我们只是讨论如何日志系统如何实现数据恢复的操作。

## 总结
命令模式是一个非常有趣的设计模式，它将请求与请求的调用者解耦，使得我们无需关心到底请求是如何被执行的，我们只需要关注它的执行结果就行了。另外命令模式还将请求封装成了一个统一的形式（都有一个共同的接口），使得我们在面向命令编写代码的时候能够简化代码。提高代码的可读性和可维护性。和命令模式比较像的一个模式是模板方法模式，它是将好多方法封装到一个公共的方法里面，是的客户端无需知道具体方法里面细节是什么就可以方便地完成自己所需的功能。


设计模式GitHub仓库地址：[https://github.com/QinJiangbo/DesignPatterns](https://github.com/QinJiangbo/DesignPatterns)