# 面向对象

## 1. 特征

* 封装
  * 一个文件必须是一种类型
  * 内部类
* 继承
  * 单继承，多实现
* 多态
  * override

## 2. SOLID原则 + LOD + CRP

| 缩写 | 全称 | 中文 |
| :--- | :--- | :--- |
| S | The Single Responsibility Principle | 单一责任原则 |
| O | The Open Closed Principle | 开放封闭原则 |
| L | Liskov Substitution Principle | 里氏替换原则 |
| I | The Interface Segregation Principle | 接口分离原则 |
| D | The Dependency Inversion Principle | 依赖倒置原则 |
|  | Law of Demeter | 迪米特（最少知道）原则 |
|  | Composite Reuse Principle | 合成/复合原则 CRP |

* 不要做太多事情，一个类就干一件事情
* 打开扩展，关闭修改
* 父子替换
* 拆分为鸭子类型，典型`ApplicationContext`, `ConfigurableApplicationContext`
* 依赖于接口，高层，不要具体的，更进一步，就是依赖注入。



## 3. Design Pattern\(Gof 23\)

### 3.1 创建型

| 名称 | 描述 |
| :--- | :--- |
| 单例 SingleTon |  |
| 原型 Prototype |  |
| 简单工厂 Simple Factory |  |
| 工厂模式 Factory  |  |
| 抽象工厂 Abstract Factory |  |
| 建造者 Builder |  |

### 3.2 结构型

| 名称 | 描述 |
| :--- | :--- |
| 代理 |  |
| 适配器 |  |
| 桥接 |  |
| 装饰 |  |
| 外观 |  |
|  享元 |  |
| 组合 |  |

### 3.3 行为型

| 名称 | 描述 |
| :--- | :--- |
| 模板方法 |  |
| 策略 |  |
| 命令 |  |
| 责任链 |  |
| 状态 |  |
| 观察者 |  |
| 中介者 |  |
| 迭代器 |  |
| 访问者 |  |
| 备忘录 |  |
| 解释器 |  |



## 4. UML

### 4.1 UML的种类

* 用例图
* 活动图
* 时序图
* 类图

### 4.2 UML工具

* StartUML
* UMLet



## Reference

{% embed url="https://www.cnblogs.com/wuyuegb2312/p/7011708.html" %}



