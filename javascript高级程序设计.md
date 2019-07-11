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



# 第八章：BOM【2019.7.10】

## 8.1   window对象

### 8.1.1  全局作用域

- 全局变量和全局函数都可以看作是window对象的属性和方法。但是直接用`window.property`定义属性的时候，该属性可以用`delete`操作符删除；而全局变量不能用delete删除；

- 尝试访问未声明的变量会抛出一个错误；但是可以通过window对象查询一个未声明的变量是不是存在：

- ```javascript
  var newValue = oldValue; //抛出错误，因为oldValue未定义
  var newValue = window.oldValue; //这是一次属性查询，newValue的值在这里时undefined。
  ```

### 8.1.2  窗口关系及框架

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

### 8.1.3  窗口位置

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

### 8.1.4  窗口大小

- innerWidth，innerHeight，outerWidth，outerHeight。不同浏览器表现不同。

- resizeTo()和resizeBy()：resizeTo(x,y)重置窗口尺寸到指定的值；resizeBy(x,y)接收原窗口和新窗口的差值。

### 8.1.5  导航和打开窗口

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

### 8.1.6  间歇调用setInterval()和超时调用setTimeout()

- 语法：setInterval | setTimeout(  要执行的代码块【函数 | 字符串""】, 毫秒数)；
- 注意⚠️：
  - 第一个参数可以是函数或者字符串。最好使用函数，因为字符串可能会造成性能上的损失。
  - setInterval | setTimeout里面的函数中的this在非严格模式下指向window，严格模式下指向undefined。
  - 返回一个数值ID，可传入clearTimeout()或者clearInterval()方法来取消调用。
  - 很少使用setInterval()。一般认为，使用setTimeout()来代替setInterval()是一种最佳模式，因为setInterval()可能会在一次执行还未结束时就开始下一次的执行，造成混乱。

### 8.1.7  系统对话框

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

## 8.2  location对象

即是window对象的属性，又是document对象属性。`window.location`和`document.location`引用的是同一个`location`对象。

### 8.2.1  查询字符串参数

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

### 8.2.2  位置操作

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

## 8.3  navigator对象

- 所有支持JavaScript的浏览器所共有的；
- navigation对象的一些属性可以用于检测显示网页的浏览器类型

### 8.3.1  检测插件

- **非IE浏览器可以用plugins数组来检测插件类型。plugins数组中的每个插件都含有以下四个属性：**
  - name：插件的名字

  将name与给定的插件名字进行比较，检测plugins中是否有该插件。

  ```javascript
  function hasPlugins(name){
  			name = name.toLowerCase();
  			for(var i=0; i < navigator.plugins.length; i++){
  				if (navigator.plugins[i].name.toLowerCase().indexOf(name) > -1) {
  					return true;
  				}
  			}
  			return false;
  		}
  ```

  - description：插件的描述
  - filename：插件的文件名
  - length：插件所处的MIME类型数量。

- **检测IE浏览器插件的唯一方法是专有的ActiveXObject类型。**

  ```javascript
  function hasIEPlugins(name){
    try{
      new ActiveXObject(name);
      return true;
    }catch(er){
      return false;
    }
  }
  ```

  

- 以上两种方法差别太大，因此典型做法是针对每个插件分别设计检测函数。

- ```javascript
  //示例检测所有浏览器中的Flash插件
  function hasFlash(){
    var result = hasPlugins("Flash");
    if(!result){
      result = hasIEPlugins("ShockwaveFlash.ShockwaveFlash");
    }
    return result;
  }
  ```

### 8.3.2  注册处理程序

FireFox增加了navigator的registerContentHandler()方法以及registerProtocolHandler()。

- registerContentHandler()方法：该方法接收三个参数，第一个参数是要处理的MIME类型；第二个是可以处理该MIME类型的页面的URL，第三个是应用程序名称；
- registerProtocolHandler()：该方法接收三个参数：要处理的协议、处理该协议的页面的URL、应用程序名称。

## 8.4  screen对象

设计移动设备的屏幕大小时情况有点不一样。IOS设备返回768*1024，运行安卓设备时会调用screen.width和screen.height的值。

## 8.5  history对象

history对象保存用户的上网记录。有go()、back()、forward()、length()方法。其中length()方法可以检测当前界面是不是用户打开的第一个界面（用户是不是一开始就打开了你的页面）

- go()方法：在用户的历史记录中任意跳转，可向前也可以向后。

  - 接受一个整数参数，表示跳转的页面数。

  ```javascript
  history.go(-1)//表示后退一页，与浏览器的"后退"按钮效果相同
  history.go(2)//表示前进两页，与连按两次浏览器的"前进"按钮效果相同。
  ```

  - 接收一个字符串，跳转到离包含这个字符串的历史记录中最近的那一页。

  ```javascript
  history.go("zhihu.com") //跳转到历史记录中包含字符串"zhihu.com"的最近的位置，可能朝前，也可能后退。
  ```

- back()方法：后退，相当于浏览器的“后退”按钮

- ```
  history.back()
  ```

- forward()方法：前进，相当于浏览器的“前进”按钮。

- ```
  history.forward()
  ```

- Length()方法：保存所有的历史记录数量。对于加载到窗口、标签页、框架中的第一个页面而言，history.length的值为0.

# 第九章：客户端检测

不到万不得已，不要使用客户端检测。能用通用方法就先用通用方法。先设计最佳方案，再根据特定的浏览器进行技术增强。

## 9.1能力检测【能力检测有哪两个注意事项】

能力检测又称为特性检测。能力检测的目标不是检测浏览器类型，而是识别浏览器的能力。例如IE5.0之前的版本不支持document.getElementById()方法，可以使用非标准的document.all()方法来完成相应功能。于是就需要进行能力检测：

```javascript
function getElement(id){
  if(docuemnt.getElementById){
    return docuemnt.getElementById(id);
  }else if{
    return document.all[id];
  }else{
    throw new Error("No way to retrieve element!");
  }
}
```

注意两点：

- 最先检测要达成目的经常用到的特性。最先检测常用的特性可以保证代码最优化，多数情况下可以避免测试多个条件。
- 必须测试实际要用到的特性。a特性存在不代表b特性一定存在，注意关联关系。

### 9.1.1  更可靠的能力检测

在可能的情况下，尽量使用typeof来进行能力检测。

在浏览器环境下测试任何对象的某个特性是否存在时，要使用这个函数：

```javascript
function isHostMethod(object, property){
  var t = typeof object[property];
  return t == "function" || (!!(t == "object" && object[property])) || t == "unkown";
}
```

### 9.1.2 能力检测，不是浏览器检测

检测某个或者某几个特性并不能确定浏览器。

如果你知道自己的应用程序需要使用哪些特定的浏览器特性，最好是一次性检测这些特性，不要分开检测：

```javascript
var hasPlugins = !!(navigator.plugins && navigator.plugins.length);//能力检测，检测是否有netscape风格的插件
```

## 9.2怪癖检测

怪癖检测的目标是检测浏览器有什么特殊行为。能力检测是检测浏览器支持什么行为，怪癖检测是检测浏览器有什么缺陷。

IE8及其更早的版本存在一个bug：如果某个实例属性与[enumerable]被标记为false的某个原型属性同名，那么该实例属性将不能被for-in循环枚举；

safari3以前的版本会枚举被隐藏的属性；

最好只检测对代码运行有直接影响的“怪癖”，而且最好在脚本开始执行时就检测。

## 9.3用户代理检测

**用户代理检测**：通过检测`用户代理字符串`来确定实际使用的浏览器类型。

**用户代理字符串**：可以通过JavaScript的navigator.userAgent()方法访问。

**电子欺骗**：浏览器通过在用户代理字符串中加入一下错误的或者误导性的信息，来达到欺骗服务器的目的。

### 9.3.1. 用户代理字符串的历史

- **早期的浏览器：**

第一款web浏览器mosaic，用户代理字符串格式为：mosaic/0.9

第一款Netscape浏览器Mozilla，用户代理字符串格式为：Mozilla/版本号 [语言] (平台; 加密信息)

其中加密信息包括U(128位加密)、I  (40位加密)和N（不加密）

```
Mozilla/2.02 [Fr] (WinNT; I)
```

- **Netscape3.0和IE3.0**

Netscape3.0格式：Mozilla/版本号 （平台; 加密类型 [; 操作系统或者cpu说明]）

当时的Netscape3.0浏览器是主流浏览器，因此IE3.0在用户代理字符串中加入了欺骗信息Mozilla，将自己伪装成Netscape3.0

- **Gecko**

  **Gecko是FireFox的呈现引擎**，第一个采用Gecko的浏览器是Netscape6.0。

```javascript
//Mozilla/Mozilla版本号 （平台; 加密信息; 操作系统或者CPU; 语言; 预先发行版本）
//Gecko/Gecko版本号 应用程序或产品/应用程序或产品版本号。

//示例：windowsXP下的Netscape 6.2.1
Mozilla/5.0 (window; U; windows NT 5.1; en-US; rv:0.9.4) Gecko/20011128 Netscape6/6.2.1
```

- **Webkit**

2003年，Apple公司宣布要发布自己的web浏览器，叫做Safari，Safari的呈现引擎叫做Webkit。webkit是Linux系统中konqueror浏览器的呈现引擎KHTML的一个分支。

webkit刚发布时的用户代理字符串格式：

```
Mozilla/Mozilla版本号 (平台; 加密信息; 操作系统或者CPU; 语言) AppleWebkit/AppleWebkit版本号 (KHTML, like Gecko) Safari/Safari版本号。
```

Safari3.0时新增了version记号，表示safari的实际版本号。只在Safari中有，其它webkit支持的浏览器中没有。

```
Mozilla/Mozilla版本号 (平台; 加密信息; 操作系统或者CPU; 语言) AppleWebkit/AppleWebkit版本号 (KHTML, like Gecko) version/3.0.3 Safari/Safari版本号。
```

