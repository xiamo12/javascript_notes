# 第六章：面向对象的程序设计

## 6.1理解对象

面向对象的的语言有一个特征：都有一个类的概念。我们可以通过类，创建具有相同属性和方法的对象。

ECMAScript-262中对象的定义：一组无序的属性集合，属性值可以是基本值，对象或者函数。

### 6.1.1属性类型

- 数据属性
  - 属性特征
    - [[configurable]]可配置。把它设置为false之后就无法再次设置为true了。
    - [[enumerable]]可枚举。propertyIsEnumerable()检测对象属性是否可枚举。
    - [[value]]属性值
    - [[writable]]属性是否可写。
- 访问器属性
  - 属性特征
    - [[configurable]]
    - [[enumerable]]
    - [[get]]读取属性时调用的函数
    - [[set]]写入属性时调用的函数
  - 属性特征可以用Object.defineProperty(obj, property, {})来定义
  - 用Object.defineProperty(obj, property, {})创建的对象属性，布尔值类型的特征值需要指定，不指定的话默认为false。正常声明的变量拥有的属性没有这个限制。

### 6.1.2定义多个属性

- Object.defineProperties(obj, {})

### 6.1.3读取属性的特征

- Object.getPropertyDescriptor(obj, property)，返回一个对象，该对象拥有属性类型对应的四个属性特征。