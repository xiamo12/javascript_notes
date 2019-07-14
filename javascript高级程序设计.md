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

### 9.1.2  能力检测，不是浏览器检测

检测某个或者某几个特性并不能确定浏览器。

如果你知道自己的应用程序需要使用哪些特定的浏览器特性，最好是一次性检测这些特性，不要分开检测：

```javascript
var hasPlugins = !!(navigator.plugins && navigator.plugins.length);//能力检测，检测是否有netscape风格的插件
```

## 9.2  怪癖检测

怪癖检测的目标是检测浏览器有什么特殊行为。能力检测是检测浏览器支持什么行为，怪癖检测是检测浏览器有什么缺陷。

IE8及其更早的版本存在一个bug：如果某个实例属性与[enumerable]被标记为false的某个原型属性同名，那么该实例属性将不能被for-in循环枚举；

safari3以前的版本会枚举被隐藏的属性；

最好只检测对代码运行有直接影响的“怪癖”，而且最好在脚本开始执行时就检测。

## 9.3  用户代理检测

**用户代理检测**：通过检测`用户代理字符串`来确定实际使用的浏览器类型。

**用户代理字符串**：可以通过JavaScript的navigator.userAgent()方法访问。

**电子欺骗**：浏览器通过在用户代理字符串中加入一下错误的或者误导性的信息，来达到欺骗服务器的目的。

### 9.3.1  用户代理字符串的历史

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

- **Gecko**【FireFox的呈现引擎】

  **Gecko是FireFox的呈现引擎**，第一个采用Gecko的浏览器是Netscape6.0。

```javascript
//Mozilla/Mozilla版本号 （平台; 加密信息; 操作系统或者CPU; 语言; 预先发行版本）
//Gecko/Gecko版本号 应用程序或产品/应用程序或产品版本号。

//示例：windowsXP下的Netscape 6.2.1
Mozilla/5.0 (window; U; windows NT 5.1; en-US; rv:0.9.4) Gecko/20011128 Netscape6/6.2.1
```

- **Webkit**【Safari的呈现引擎】

2003年，Apple公司宣布要发布自己的web浏览器，叫做Safari，Safari的呈现引擎叫做Webkit。webkit是Linux系统中konqueror浏览器的呈现引擎KHTML的一个分支。

webkit刚发布时的用户代理字符串格式：

```
Mozilla/Mozilla版本号 (平台; 加密信息; 操作系统或者CPU; 语言) AppleWebkit/AppleWebkit版本号 (KHTML, like Gecko) Safari/Safari版本号。
```

Safari3.0时新增了version记号，表示safari的实际版本号。只在Safari中有，其它webkit支持的浏览器中没有。

```JavaScript
//Mozilla/Mozilla版本号 (平台; 加密信息; 操作系统或者CPU; 语言) AppleWebkit/AppleWebkit版本号 //(KHTML, like Gecko) version/3.0.3 Safari/Safari版本号。

//示例：webkit内核的Safari浏览器用户代理字符串，由navigator.userAgent()方法获取

Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_3) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.0.3 Safari/605.1.15
```

- **Konqueror**【KHTML】

  konqueror浏览器是基于Linux平台的一款浏览器，它的呈现引擎是KHTML

- **Chrome**【webkit】

使用webkit呈现引擎，但是使用了不同的JavaScript引擎。用户代理字符串格式：

```javascript 
//Mozilla/Mozilla版本 (平台; 加密信息; 操作系统和CPU; 语言) AppleWebkit/AppleWebkit版本号 (KHTML, //like Gecko) Chrome/Chrome版本号 Safari/Safari版本号

//示例：Chrome浏览器的navigator.userAgent()信息：
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36
```

- **Opera**【presto】

  目前的大多数嗅探技术，都只对Mozilla内核和Gecko内核的浏览器感兴趣

```javascript
//目前发布的浏览器中，唯一使用产品名和版本号来彻底识别自身的浏览器。
//Opera8.0之前版本

Opera/版本号 (操作系统或者CPU; 加密类型) [语言]

//Opera8.0发布以后
Opera/版本号 (操作系统或者CPU; 加密类型; 语言) 

//Opera9.0之后出现两种修改用户代理字符串的方式：
//一是将自身标识为另一个浏览器，如FireFox或者IE；
//另一个是将自己伪装成另一个浏览器，如FireFox或者IE；
```

- **IOS和Android**

移动操作系统IOS和Android的默认浏览器都是基于Webkit。

```javascript
//IOS用户代理字符串

Mozilla/5.0 (平台; 加密类型; 操作系统或者CPU like Mac OS X; 语言)
AppleWebkit/AppleWebkit版本号 (KHTML, like Gecko) Version/浏览器版本号 Mobile/移动版本号 safari/safari版本号

//示例
Mozilla/5.0 (iPbone; U; CPU iPbone OS 3_0 like Mac OS X; en-us)
AppleWebkit/528.18 (KHTML, like Gecko) Version/4.0 Mobile/7A341 safari/safari528.16

```

### 9.3.2  用户代理字符串检测技术

根据**呈现引擎**和**最低限度的版本**来检测特定的浏览器。

- **识别呈现引擎**

  五大呈现引擎：IE（IE）、Gecko（FireFox）、Webkit（Chrome和Safari以及IOS、Android移动平台）、Presto（Opera）、KHTML（基于Linux平台的konquero浏览器内核r）

  引擎识别的最佳顺序：Opera- ->WebKit - ->KHTML- ->Gecko- ->IE

  **Opera：**

  - 7.6以及更高版本：Opera的version()方法：window.opera.version();
  - 7.6及其之前的版本：直接检测用户代理字符串

  **Webkit:**

  - AppleWebKit字符串是wbekit内核的浏览器独有的，用正则表达式检测该字符串是否存在，如果存在就保存AppleWebKit后面的版本号；

  **KHTML：**

  - 正则表达式检测KHTML字符串和konqueror版本

  **Gecko：**

  - Gecko内核的浏览器，用户代理字符串中的版本号介于"vr:"与")"之间，因此检测这之间的版本号。

  **IE：**

  - IE浏览器的版本号位于字符串MISE后面、一个分号的前面。

- **识别浏览器**

  - Opera内核：Opera浏览器：version()方法：window.opera.version()

  - webkit内核：
    - Safari浏览器：用户代理字符串中同时含有AppleWebKit和Version
    - Chrome浏览器：用户代理字符串中同时含有AppleWebKit和Chrome
  - KHTML内核：konqueror浏览器
  - Gecko内核：含有FireFox字符串的是FireFox火狐浏览器

- **识别平台**

三大主流平台：windows、Mac、Linux。对这三个平台而言，浏览器一般指报告Windows。

确定平台方法：`navigator.platform`要比检测用户代理字符串更有用。

```
var p = navigator.platform;
system.win = p.indexOf("Win") == 0;
system.mac = p.indexOf("Mac") == 0;
system.x11 = (p.indexOf("X11") == 0 || (p.indexOf("Linux") == 0));
```

- **识别windows操作系统**

在windows XP之前，有两个Windows版本，一个针对个人的windows 95、windows 98和Windows ME；一个针对商业的Windows NT，最后因为市场原因Windows NT改名为Windows 2000。这两个版本最后合并成基于Windows NT的Windows XP，随后又基于Windows XP发展出了Windows Vista。

- **识别移动设备**

  IOS系统：识别字符串iPhone、iPod、iPad

  Android系统：识别Android后面的版本号

- **识别游戏系统**

  任天堂wii或者playstation 3或者内置web浏览器都提供了浏览器下载。

  wii：Opera/9.10 (Nintendo Wii; U;  ; 1621; en)

  Playstation3: Mozilla/5.0 (PLAYSTATION 3; 2.00)

### 9.3.3  完整的代码

完整的用户代理字符串检测，包括检测呈现引擎、平台、Windows操作系统、移动设备、游戏系统

### 9.3.4  使用方法

用户代理检测是客户端检测的最后一个方法，优先采用性能检测和怪癖检测。

# 第十章：  DOM【2019.7.11】

DOM（文档对象模型Document Object Model）是HTML和XML的一个API（应用程序编程接口）

## 10.1  节点层次

DOM可以将HTML或者XMl描述成一个由多层节点构成的结构。

文档节点是每个文档的根结点。html元素通常作为HTML文档中文档元素的唯一子节点，称为文档元素。

HTML文档元素始终是<html>，XML文档中任何元素都可以成为文档元素。

html元素通过元素节点来表示

特性（attribute）通过特性节点来表示

文档类型通过文档类型节点来表示

注释通过注释节点来表示。

### 10.1.1  Node类型

- `nodeType`: 节点类型：
  - 元素节点nodeType = 1
  - 属性节点nodeType = 2
  - 文本节点nodeType = 3
  - 注释节点nodeType = 8
  - 文档节点nodeType = 9
- `nodeName`：节点名称
  - 元素的nodeName是元素标签名。

- `nodeValue`：节点值
  - 元素的nodeValue = null
  - 属性的nodeValue = 属性值

- `childNodes`：每个节点都有一个childNodes属性，其中保存着一个NodeList对象。`NodeList`是一个类数组对象，可以用方括号访问它的属性，但是它并不是array的实例。访问保存在`NodeList`中的子节点，可以通过方括号[]，也可以通过item()方法。

- `NodeList`有一个`length`属性`someNode.childNodes.length`，这个属性是查询`someNode`的子节点数量时，查询那一瞬间的子节点数量。NodeList是基于DOM结构执行动态查询的结果。

  ```javascript
  var firstNode = someNode.childNodes[0]
  //or
  var firstNode = someNode.childNodes.item(0)
  ```

- NodeList有length属性，可以通过方括号访问属性，但是它是类数组对象。要将它转换成数组对象，针对IE和非IE浏览器有不同的方法：

  ```javascript
  function convertToArray(){
    var array = null;
    try{
      array = Array.prototype.slice.call(nodes, 0);
    }catch(er){
      array = [];
      for(i = 0; i < nodes.length; i++){
        array.push(nodes[i])
      }
    }
    return array;
  }
  ```

- 节点之间的关系：

  - 父节点`parentNode`：包含在NodeList列表里的所有子节点都有同一个parentNode。每个节点的父节点是唯一的，所以是单数；
  - 子节点`childNodes`：子节点可以有很多个，所以是复数形式。childNodes包含一个NodeList对象，该NodeList属性可以用方括号和item()方法访问，但是访问的时候是针对childNodes进行代码的编写。
  - 兄弟节点`previousNode`和`nextSibling`
    - `previousNode`：当前节点的前一个节点。如果当前节点是第一个节点，那么它的previousNode为null
    - `nextSibling`：当前节点的下一个节点。如果当前节点是第一个节点，那么它的nextSibling为null
  - 第一个子节点`firstChild`和最后一个子节点`lastChild`：
    - someNode.firstChild === someNode.childNodes[0];
    - someNode.lastChild === someNode.childNodes[someNode.childNodes.length-1];

  - hasChildNodes()方法：节点包含一个或者多个子节点时返回true；
  - ownerDocument属性：所有节点都拥有的最后一个属性。该属性表示指向整个文档的文档节点。任何节点都只属于它所在的文档

- 操作节点

  - 以下四种操作方法是在父节点parentNode的基础上进行的操作：**不是所有节点都有子节点，所以并不是所有节点都有这四个方法：**
    - `appendChild(newNode)`方法：在childNodes的末尾添加一个子节点。添加这个子节点后，childNodes、parent、lastChild都会动态地发生变化。这个方法返回新添加的节点。

    ```javascript
    var newNode = someNode.appendChild("p")//表示在节点someNode下的子节点列表的末尾，添加了节点P
    ```

    如果将childNodes中已有的节点用appendChild()方法添加，那么就会改变原有节点的位置，原有节点变成lastChild

    - `insertBefore(newNode，oldNode)`方法：在某个节点`oldNode`之前插入新节点`newNode`。这个新节点是旧节点的previousSibling。如果旧节点oldNode=null，那么就相当于这个节点插入之后，它的后面没有节点——>也就是说，此时insertBefore(newNode, null)方法等同于appendChild(newNode)方法。
    - `replaceChild(newNode, oldNode)`：节点替换。新节点替换了旧节点的位置，被替换的节点从节点树上删除了，但是还是属于这个文档，只是在这个文档中没有它的位置了。
    - `removeChild(node)`：该方法接受一个参数，即要被移除的节点。节点被移除之后依然跪文档所有，只不过在文档中没有了自己的位置。

  - 所有子节点都拥有的两个方法：
    - `cloneNode(boolean值)`：谁调用它，它就复制谁，得到一个调用这个方法的节点的副本。

      这个方法接受一个布尔值参数，这个参数为true的时候执行**<u>深复制</u>**，复制节点及其子节点树；

      值为false时执行**<u>浅复制</u>**，只复制当前节点本身。

      返回的副本没有指定父节点，是一个“孤儿”。可以通过节点操作将它添加到指定的父节点下。

    - `normalize()`方法：处理文本节点。如果是空文本节点就删除它；如果是两个相邻的文本节点就合并成一个文本节点。

### 10.1.2  document类型

- document节点特征：

  - nodeType = 9
  - nodeName = "#document";
  - nodeValue = null
  - parentNode值为null
  - ownerDocument值为null
  - 其子节点可能为一个documentType（最多一个）、Element（最多一个）、comment（注释）、processingInstruction
  - document可以用于表示HTML页面或者其它基于XML的文档，不过最常用的还是用来表示HTML页面。它是HTMLDocument类型的一个对象实例，可以取得页面信息、操作页面外观和低层结构。

- 文档子节点

  - documentElemen属性t：指向html元素

    ```javascript
    var html = document.documentElement //取得对元素<html>的引用
    ```

    在<html><head></head></head><body></body></html>结构中，

    document.documentElement 与 document.firstChild 、document.childNodes[0]指向同一个元素，即<html>。

  - body属性：指向<body>标签。

  - doctype属性：通过document.doctype来访问文档类型声明documengType。不同浏览器里的doctype具有不同表现。

    - IE8以及更早版本：将doctype解析为注释；
    - safari、chrome、opera：将doctype正常解析，但是不会当作document.chindNodes的子节点
    - IE9+和FireFox：将doctype正常解析，当作document.chindNodes的第一个子节点

  - 当html元素之外有注释的时候:

    ```html
    <!--第一行注释-->
    <html>
      <head>
        
      </head>
      <body>
        
      </body>
    </html>
    <!--第二行注释-->
    ```

    不同浏览器里有不同的表现：

    - IE8以及更早的版本、Safari3.1以及更高级的版本、Chrome、Opera浏览器：为第一行注释创建节点，第一行注释成为document.childNodes的第一个子节点；忽略第二行注释；
    - IE9+版本：将第一行注释创建为子节点，也为最后一行注释创建为子节点。
    - Safari3.1以及更早的版本、FireFox：忽略这两条注释。

- 文档信息

  与文档信息有关的属性`title`、`URL`、`domain`、`referrer`。

  - title：标题信息，即显示在浏览器窗口的标题栏或者标签页上的标题。可通过该属性获取和修改【即可读写】

  - URL：页面完整的URL【即显示在地址栏中的URL信息】

  - domain：只包含页面的域名。与URL属性关联。可以设置，设置的域名需要是包含在URL中。

  ```javascript
  //假设原始域名为"www.wrox.com"
  document.domain = "wrox.com" //成功
  document.domain = "nczoneline.net" //出错
  ```

  **处于跨域安全限制，来自不同子域的页面不能通过JavaScript通信。这时候可以将每个子域的domain属性改为相同的值，这些页面就可以访问对方包含的JavaScript对象了。**

  浏览器对domain属性还有一个限制：可以由松散loose状态改为紧绷tight状态，反之则不可以。

  ```javascript
  //假设原来的document.domain = "p2p.wrox.com"
  document.domain = "wrox.com" //1⃣️成功
  document.domain = "p2p.wrox.com" //2⃣️出错。也就是可以减少，不能增加。
  ```

  

  - Referrer: 保存着所有链接到当前页面的那个页面的URL。没有来源的情况下可能该值会为空字符串。

  这些信息都保存在http请求的头部，只是我们通过这些属性，可以在JavaScript中访问到它们。

- 查找元素

  - getElementById()，传入一个字符串，表示元素的id值。如果页面中有多个元素使用同一个id值，那么获取到第一个id对应的元素。
    - IE7以及更早版本的一些怪异行为：
      - 忽略大小写。myDiv和mydiv都可以返回同一个元素。
      - 如果页面中有表单元素【input、textarea、button、select等】的name属性值与我们要查找的id值相同，那么就可能获取到这个表单元素。
  - getElementsByTagName()，传入一个字符串参数，表示标签名。
    - 返回一个数组，数组包含获取到的元素节点列表。在HTML中，返回的是一个HTMLCollection对象，这是一个动态集合，与childNodes包含的NodeList列表类似。
    - 可以用方括号、item()方法、namedItem()方法访问列表项。item()传入表示第几项的数字；namedItem()传入表示name属性值的字符串参数。
    - 如果使用的是[]语法，那么传入的是数字则后台调用item()方法；传入的是字符串则后台调用namedItem()方法。
    - 星号“*”表示“全部”
    - HTML中，传递给该方法的标签名不区分大小写，以实现最大限度的兼容

  ```javascript
  var allElements = document.getElementsByTagName("*");
  ```

  - getElementsByName()

    只有HTMLDocument才会有的方法，获取给定name值的元素。一般用在表单的单选按钮上。

  ```html
  <html>
    <head>
    </head>
    <body>
      <li><input type="radio" name="color" value="red" id="colorRed"/>Red</li>
      <li><input type="radio" name="color" value="green" id="colorGreen"/>Green</li>
      <li><input type="radio" name="color" value="blue" id="colorBlue"/>Blue</li>
      <script>
        var radios = document.getElementsByName("color");//获取到整个含有name值为color的列表。
      </script>
    </body>
  </html>
  ```

- 特殊集合

  这些特殊集合都是HTMLDocument对象。

  - document.anchors: 含有name属性的`<a>`元素的集合
  - document.links: 含有href属性的`<a>`元素的集合
  - document.forms: 包含文档中所有的`<form>`元素，与document.getElementsByTagName('form')结果相同。
  - document.images: 包含文档中所有的 图片元素`<img>`，与document.getElementsByTagName('img')结果相同。

- DOM一致性检验

  document.implementation.hasFeature()方法，接收两个参数：1⃣️要检测的DOM功能的名称、2⃣️版本

  ```javascript
  var hasXMLDOM = document.implementation.hasFeature("XML", "1.0")
  ```

- 文档写入

  write() 、 writeIn()、open()、close()方法。前两者接收字符串参数，将()里的内容写入文档流；

  - write() 、 writeIn(): 
    - write() 原样写入，writeIn()会在字符串末尾添加一个换行符。
    - 可以使用write() 、 writeIn()动态包含外部的JavaScript资源，注意用字符串引入`<script>`文件的时候，`</script>`要用转义字符转义`/` - - - ->  `<\/script>`
    - 文档加载结束之后再调用这两个方法，会重写整个页面。
  - open()、close()方法：
    - 打开和关闭网页的输出流。【暂时没找到使用方法】
  - 严格型XHTML不支持这四个方法。

  ### 10.1.3  Element类型

  nodeType值为1

  nodeValue值为null

  nodeName值为标签名。标签名也可以用tagName获取到。 在HTML中返回的标签名全部是大写格式。在XML中原样返回。如果不确定脚本回运行在HTML还是XML中，最好都用toLoertCase()先转换成小写在比较。

  ```html
  <html>
    <body>
      <div id="myDiv">
        this is a div
      </div>
      <script>
        var mydiv = document.getElementById("myDiv");
        console.log(mydiv.tagName) //DIV
        console.log(mydiv.tagName) //DIV
      </script>
    </body>
  </html>
  ```

  parentNode可以是Element或者Document

  childNodes可能是element、text、comment、processigInstruction等

- HTML元素

  所有的HTML元素都由HTMLElement类型或者其子类型表示。HTMLElement类型直接继承自Element并且添加了一些属性和方法。

  