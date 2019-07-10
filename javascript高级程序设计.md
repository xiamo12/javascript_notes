[TOC]

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



# 第八章：BOM

## 8.1 window对象

### 8.1.1全局作用域

- 全局变量和全局函数都可以看作是window对象的属性和方法。但是直接用`window.property`定义属性的时候，该属性可以用`delete`操作符删除；而全局变量不能用delete删除；

- 尝试访问未声明的变量会抛出一个错误；但是可以通过window对象查询一个未声明的变量是不是存在：

- ```javascript
  var newValue = oldValue; //抛出错误，因为oldValue未定义
  var newValue = window.oldValue; //这是一次属性查询，newValue的值在这里时undefined。
  ```

### 8.1.2窗口关系及框架

- 如果页面包含框架frame，那么每个框架都包含有自己的window对象，并且保存在frames集合中。可以通过frames[i]或者框架名称来访问每个相应的window对象。每个window对象有一个name属性，其中包含框架名称。
  - **top对象**：最好使用top而不是window，来引用这些框架。**用`top.frames[0]`代替`window.frames[0]`**

```html
<html>
  <head></head>
  <frameset>
      <frame src="" name="topFrame"></frame>
      <frameset>
          <frame src="" name="leftFrame"></frame>
          <frame src="" name="rightFrame"></frame>
      </frameset>
  </frameset>
</html>
```

top对象始终指向最外层的框架，也就是浏览器窗口。

- **parent对象**：与top对象相对的另一个window对象，表示当前框架的直接上层框架（父框架）。在没有框架的情况下，top=parent=window。
- **self对象**：始终指向window，引用它是为了与top对象和parent对象对应起来。
- top、parent、self对象都是window对象的属性，可以用window.top、window.parent 等形式来访问。

### 8.1.3窗口位置

- IE、Opera、Safari、Chrome浏览器支持screenLeft、screenTop属性，表示窗口相对于屏幕左边和上边的位置

- FireFox提供了screenX、screenY属性提供相同的位置；Safari、Chrome也支持该属性；

- Opera也支持screenX、screenY属性，但是Opera的该属性与screenLeft、screenTop并不对应，因此最好不要在Opera中使用。

- moveTo()方法：移动到某个坐标位置，接收x，y坐标值；

  ```javascript
  window.moveTo(0,0)//移动到屏幕左上角
  ```

- moveBy()方法：移动某段距离，接收水平和垂直方向上移动的像素值；

```javascript
window.moveBy(100,100)//将窗口向右移动100像素，向下移动100像素。
```

### 8.1.4窗口大小

- innerWidth，innerHeight，outerWidth，outerHeight。不同浏览器表现不同。

- resizeTo()和resizeBy()：resizeTo(x,y)重置窗口尺寸到指定的值；resizeBy(x,y)接收原窗口和新窗口的差值。

### 8.1.5导航和打开窗口

- Window.open(url, target, "", 新页面是否取代浏览器历史记录中的该页面的布尔值)

```javascript
window.open("www.zhihu.com", "_blank", "width=200,height=200,resizable=yes", true)
```

- `window.close()`：关闭用`window.open()`方法打开的页面：

```javascript
var wrox = window.open("www.zhihu.com", "_blank", "width=200,height=200,resizable=yes", true);
wrox.close()
```

- 新创建的window对象有一个`opener`属性，指向打开它的原始窗口对象。

```javascript
var wrox = window.open("www.zhihu.com", "_blank", "width=200,height=200,resizable=yes", true);
console.log(wrox.opener) //window
```

- 可以将`opener`的值设置成`null`，切断原始页面与新标签页之间的通信。

```javascript
var wrox = window.open("www.zhihu.com", "_blank", "width=200,height=200,resizable=yes", true);
wrox.opener = null;
```

- 弹出窗口屏蔽程序：

  - 浏览器内置的屏蔽程序：执行window.open()方法会返回null

  - 浏览器的扩展程序或其它程序屏蔽弹出窗口：执行window.open()方法会抛出一个错误。

  - 检测方法：

    ```javascript
    var blocked = false;
    try{
      var wroxWin = window.open("https://www.zhihu.com","_blank");
      if(wroxWin == null){
        blocked = true;
      }
    }catch(ex){
      blocked = true;
    }
    
    if(blocked){
      alert("the popup was blocked!");
    }
    ```

### 8.1.6间歇调用setInterval()和超时调用setTimeout()

- 语法：setInterval | setTimeout(  要执行的代码块【函数 | 字符串""】, 毫秒数)；
- 注意⚠️：
  - 第一个参数可以是函数或者字符串。最好使用函数，因为字符串可能会造成性能上的损失。
  - setInterval | setTimeout里面的函数中的this在非严格模式下指向window，严格模式下指向undefined。
  - 返回一个数值ID，可传入clearTimeout()或者clearInterval()方法来取消调用。
  - 很少使用setInterval()。一般认为，使用setTimeout()来代替setInterval()是一种最佳模式，因为setInterval()可能会在一次执行还未结束时就开始下一次的执行，造成混乱。

### 8.1.7系统对话框

显示这些对话框的时候会停止代码的执行，只有关闭之后代码才能进一步执行。外观与css样式无关，由操作系统或者浏览器决定。

- alert()

- confirm()

  点击ok则返回true，点击cancel则返回false。根据用户操作执行下一步代码。

  ```javascript
  if(confirm("Are you sure?")){
    alert("you are sure to do this, welcome!");
  }else{
    alert("I'm sorry you are not sure.")
  }
  ```

- prompt()文本提示框

  ```javascript
  prompt("我是文本框输入的提示","我是默认值")
  ```

- chrome复选框，从第二个弹出框开始出现一个复选框，点击勾选即可阻止后续弹出的对话框;

- Window.print()：异步显示打印对话框；（safari可行）

- window.find()：异步显示查找对话框（Safari不能显示）

## 8.2 location对象

即是window对象的属性，又是document对象属性。`window.location`和`document.location`引用的是同一个`location`对象。

### 8.2.1查询字符串参数

*decodeURIComponent()方法可解码被编码过的字符串（search中的name和value值）

```javascript
"https://www.baidu.com:8080/index.php?tn=49029047_oem_dg&ch=33#mediaid118"
"https:" //protocol协议名

"www.baidu.com:8080" 
//www.baidu.com：服务器名称hostName
//8080：端口号port
//hostName+port组成host

"index.php" //路径pathName
"?tn=49029047_oem_dg&ch=33" //查询部分search，以？开头
"#mediaid118" //返回url中的hash，如果url不包含散列，则返回空字符串

```

### 8.2.2位置操作

- location.assign()：为其传递一个URL

```javascript
location.href = "https://www.zhihu.com";
window.location = "https://www.zhihu.com";
//两者等效于下面这条语句

location.assign("https://www.zhihu.com");
```

- location的replace(newURL)方法。用新页面代替就页面，且禁用后退按钮。

- ```javascript
  setTimeout(function(){
    location.replace("https://www.zhihu.com");
  },1000)
  //表示1秒之后页面重新定向到https://www.zhihu.com，"后退"按钮将处于禁用状态。不输入完整url将无法返回前一个界面。
  ```

- reload()方法：重新加载当前界面。不传入参数就有可能从历史缓存里用最有效的方式重新加载；传入true会从服务器端开始加载。

- ```javascript
  location.reload();  //可能从缓存中加载
  location.reload(true); //从服务器端加载
  ```

  