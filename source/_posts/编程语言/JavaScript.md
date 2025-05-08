---
title: JavaScript
date: 2025-01-09 14:06:30
tags: 
- 前端
- JS
categories: 学习笔记
excerpt: 一种轻量级、解释型、面向对象的编程语言. 作为前端三件套之一以及TS的基础, JS语言细节十分难嚼. 本文在「料理的加护」下, 尽可能将JS处理得更加可口一些)
mathjax: true
index_img: /img/料理的加护.jpg
---
> JavaScript是一种轻量级、解释型、面向对象的编程语言. 作为前端三件套之一以及TS的基础, JS语言细节十分难嚼. 本文在「**料理的加护**」下, 尽可能将JS处理得更加**可口**一些)
<img src="/img/料理的加护.jpg" width = "35%">

# 创建JS代码块
## 变量
### `var` or `let`?
`var`先于`let`的产生, 后者是现代版本的JS中新的关键字.

使用`var`, 可以对一个先前已被声明且初始化的变量重新声明, 这不会带来报错, 代码依旧可以工作. 但是`let`并不适用.
```js
myName = "Chris";

function logName() {
  console.log(myName);
}

logName(); //输出"Chris"

var myName = "Aniya";
logName(); //输出"Aniya"
```

除此之外, 可以使用`var`前后声明相同的变量, 这并不会报错:
```js
var myName = "Chris";
var myName = "Bob";
```

而`let`只能声明一次:
```js
let myName = "Chris";
myName = "Bob";
```

因此, 在代码编写中应尽量**多使用**`let`而非`var`, 这可以帮助我们排除无意中重新命名相同变量而导致的错误.


### 变量命名的规则
与C语言类似, 建议以 **字母、数字、下划线** 组成的标识符来命名变量.
- 不可用`_`开头, 因为可能被JS设计为特殊的含义;
- 不可用数字开头, 否则引发错误;
- 大小写敏感;
- 建议采用 **小写驼峰命名法** ,即小写整个命名的第一个字母然后大写剩下单词的首字符;
- 避免使用保留字, 比如`var`,`let`,`for`等.

### 变量类型

```js
let myAge = 20 ;// 数字
let dolphinGoodbye = "So long and thanks for all the fish"; // 字符串
let test = 6 < 3; //boolean

//数组类型
let myNameArray = ["Chris", "Bob", "Jim"];
let myNumberArray = [10, 15, 40];

//对象类型
let dog = { name: "Spot", breed: "Dalmatian" };

```
> 对象类型的访问与结构体相似, `dog.name;`

在上面的几种变量类型中, 我们都采用`let`关键字声明变量, 这体现了JS是一种 **动态类型语言** ,即无需指定变量包含的数据类型.

同时, 这也意味着我们可以像`python`一样对同一个变量先后赋值不同类型的值:
```js
let myNumber = "500";
typeof myNumber;
// 输出 'string'

myNumber = 500; 
typeof myNumber;
//输出'number'
```


## 函数
- $\underline{提升}$ 解释器在执行代码之前，似乎将函数、变量、类或导入的声明移动到其作用域的顶部的过程.
```js
exampleFunction();

function exampleFunction() {
  console.log("函数内");
  console.log(x);
}
```
> 由于 **提升** 的存在, 上述的函数调用不会出错.


### 默认参数
在编写函数时, 可以通过在参数名称后添加`=`, 再指定默认值, 这样当调用函数时, 如果没有传入该参数, 则使用默认值。
```js
function greeding(name = "my friend") {
    console.log(`Hello, ${name}!`);
}
hello(); //Hello, my friend!
hello("world"); //Hello, world!
```

<br>

事件处理函数的默认接受值是`event`:
```html
<button>
onclick
</button>

<p>
nothing here
</p>

<script>
btn = document.querySelector("button")
para = document.querySelector("p")

btn.onclick = click;

function click(string){
  console.log("clicked!")
  para.textContent = string;
}
</script>
```
1. 上述的`btn`在点击之后调用函数`click`, 该函数需要一个参数`string`, 由于`btn.onclick = click;`的绑定方式, 我们无法指定传参的值, 因此点击之后的`para`的内容显示为: `[object PointerEvent]`;
2. `textContent`是属性而非方法, 因此采用赋值实现;


```js
btn.onclick = function click(string) {
  console.log("clicked!")
  para.textContent = "You have clicked the button!";
}
```
> 将上述的绑定方式如此改写, 可以在`btn`外对`string`进行赋值, 然后点击按钮可以传入指定参数供后续处理.



### 箭头函数
在了解箭头函数的作用之前, 需要先介绍 $\underline{匿名函数}$:
```js
function myFunction() {
  alert("你好");
}

// 匿名函数
(function () {
  alert("你好");
});
```
> 正如其名, 匿名函数没有函数名, 不能被调用, 但可以作为参数传入其他函数中.

如果我们希望在监听某个事件发生时调用简单的函数来处理, 则可以通过调用上述的匿名函数实现:
```js
function logKey(event) {
  console.log(`You pressed "${event.key}".`);
}

textBox.addEventListener("keydown", logKey);
```
这部分代码通过监听html元素的keydown事件, 调用函数输出按下的键盘按键. 我们可以通过匿名函数来简化书写:
```js
textBox.addEventListener("keydown", function (event) {
  console.log(`You pressed "${event.key}".`);
});
```
>只需传入函数体, 而不需要函数名, 就可以实现监听事件并调用函数的功能.

而**箭头函数**则是在此情况下更简洁的函数定义方式:
```js
textBox.addEventListener("keydown", (event) => {
  console.log(`You pressed "${event.key}".`);
});

//如果函数只接受一个参数, 也可以省略参数周围的括号
textBox.addEventListener("keydown", event => {
  console.log(`You pressed "${event.key}".`);
});
```

如果只包含一行的`return`,则可以忽略`{}`和`return`关键字:
```js
const originals = [1, 2, 3];

const doubled = originals.map(item => item * 2);

console.log(doubled); // [2, 4, 6]
```
> `item => item * 2`等价于:
```js
function doubleItem(item) {
  return item * 2;
}
```

#### 一个实例
```html
<input id="textBox" type="text" />
<div id="output"></div>
```
```js
const textBox = document.querySelector("#textBox");
const output = document.querySelector("#output");

textBox.addEventListener("keydown", (event) => {
  output.textContent = `You pressed "${event.key}".`;
});
```
通过监听输入框的keydown事件, 输出按下的键盘按键.

### 函数作用域和冲突
$\underline{作用域}$ 指当前的执行上下文, 在其中的值和表达式可以被访问. 
- 全局作用域: 脚本模式运行所有代码的默认作用域;
- 模块作用域: 模块模式中运行代码的作用域;
- 函数作用域: 由函数创建的作用域

和C语言相似, 在函数外部`let`定义的变量, 以及`const`定义的常量可以在函数内部访问.

如果HTML调用了多个外部JS文件, 其中具有相同的函数名, 那么只能访问的第一个函数, 第二个函数将被忽略:
```html
<!-- Excerpt from my HTML -->
<script src="first.js"></script>
<script src="second.js"></script>
<script>
  greeting();
</script>
```
> 如果两个JS文件都定义了`greeting`函数, 则只有第一个文件中的函数才会被调用.

## 数据类型
### 数字和操作符
大部分与C语言相同, 概括需要注意的差异:
- JS当中只有一种数字类型 -- `number`, 对于整型或者浮点数的初始化得到的量, 由`typeof`均得到`number`;
- 算术运算符: 求幂为`**`;
- 常量无法使用自增或自减,~~好像也是C语言的 忘了~~
- `===`表示严格等于, `!==`表示不等于;
> 同时存在`==`和`!=`来判断是否相等, 但是它们只是测试 **值** 是否相等, 会忽略数据类型的差异; 而上述的比较会同时比较数据类型. 因此推荐使用`===`和`!==`来避免类型不一致的错误.

### 字符串
创建字符串
```js
let myString = "A string";
const constString = myString;
console.log(constString);
//A string
``` 
可以使用单引号,双引号和**反引号**来包裹字符串, 但是必须确保字符串的开头和结尾使用相同的字符:
```js
const single = '单引号';
const double = "双引号";
const backtick = `反引号`;
```

反引号包裹的字符串称为$\underline{模板字符串}$, 大多数情况下,它与其他两种字符串类似, 但是具有特殊的属性:
- 可以嵌入 **JavaScript**;
- 可以声明**多行**的模板字面量.

#### 字符串的拼接
字符串的拼接有两种方法, 我们先介绍上述提到的模板字符串中的 $\underline{串联法}$:
```js
const name = "克里斯";
const greeting = `你好，${name}`;
console.log(greeting); // "你好，克里斯"
```
> 在模板字面量中用`${}`包装JS的变量或者表达式.


```js
const one = "你好，";
const two = "请问最近如何？";
const joined = `${one}${two}`;
console.log(joined); // "你好，请问最近如何？"
```
> 连接2个变量.

```js
const song = "青花瓷";
const score = 9;
const highestScore = 10;
const output = `我喜欢歌曲《${song}》。我给它打了 ${
  (score / highestScore) * 100
} 分。`;
console.log(output); // "我喜欢歌曲《青花瓷》。我给它打了 90 分。"
```
> 在模板字面量的`${}`内部包含表达式.

<br>
除此之外,对于普通的字符串(使用单引号或者双引号得到的字符串), 我们可以使用`+`直接连接:
```js
const greeting = "你好";
const name = "克里斯";
console.log(greeting + "，" + name); // "你好，克里斯"
```

#### 多行字符串
模板字符串会**保留**源代码中的换行符，因此可以编写跨越多行的字符串:
```js
const newline = `终于有一天，
你知道了必须做的事情，而且开始……`;
console.log(newline);

/*
终于有一天，
你知道了必须做的事情，而且开始……
*/
```

如果希望用普通的字符串得到等效的输出, 必须在字符串中包含`\n`,而非直接跨行:
```js
const newline = "终于有一天，\n你知道了必须做的事情，而且开始……";
console.log(newline);

/*
终于有一天，
你知道了必须做的事情，而且开始……
*/
```

#### 显示引号
1. $\underline{转义}$  通过在符号前加上反斜杠`\`, 可以转义字符串中的特殊字符,包括字符串中的引号:
```js
const bigmouth = 'I\'ve got no right to take my place…';
```
2. 换用其他字符: 在字面量内用不同于包裹字符串的引号:
```js
const goodQuotes1 = 'She said "I think so!"';
const goodQuotes2 = `She said "I'm not going in there!"`;
```


#### 常用方法
对于字符串对象实例,其常用的方法:
- `.length`: 获取字符串的长度;
- `[]`: 返回字符串中对应索引的字符, 索引同样从`0`开始;
- `.indexOf("")`: 查找子字符串
  - **input**: 希望查找的子字符串;
  - **output**: 子字符串开始的下标(如果不存在则返回`-1`);
- `.slice(indedxStart, indexEnd)`: 截取字符串
  - **input**: 起始下标, 结束下标(不包含该下标). 如果不存在结束下标则提取之后剩余的全部字符;
  - **output**: 截取的子字符串;

更多的`slice`知识:
1. $\underline{标准化负值}$ 如果索引是个负数, 取`index+str.length`进行标准化;
2. 如果`indexStart`大于`str.length`, 返回空字符串;
3. 如果标准化负值之后, `indexStart`大于`indexEnd`, 也返回空字符串;
 
- `.toLowerCase()` & `.toUpperCase()`: 转换字符串中的所有字符为小写或大写;
- `.replace(original, new)`: 替换字符串中`original`子字符串为`new`;
> 此时不会直接改变原字符串的值, 而是返回一个修改之后的字符串. 因此, 如果想要将原来的值替换, 需要用上述方法得到的值去赋值原来的字符串.

#### Cases
利用 **indexOf** 和 **slice** 方法, 获取新字符串:
- **input**: `"str3"`三位长字符串+`"..."`(无关字符串)+`";"`+`strLast`(剩余字符串);
- **output**: `"str3"+";"+strLast`

```js
var stations = ['MAN675847583748sjt567654;Manchester Piccadilly',
                'GNF576746573fhdg4737dh4;Greenfield',
                'LIV5hg65hd737456236dch46dg4;Liverpool Lime Street',
                'SYB4f65hf75f736463;Stalybridge',
                'HUD5767ghtyfyr4536dh45dg45dg3;Huddersfield'];

for(var i = 0; i < stations.length; i++){
    var input = stations[i];
    var str3 = input.slice(0,3);
    var strLast = input.slice(input.indexOf(";")+1); //indexOf获取;位置
    var output = str3 + ";" + strLast;
}
```

---

通过 **indexOf** 根据子字符串筛选字符串数组:
- **input**: 可能包含 *Christmas* 的字符串数组;
- **output**: 包含 *Christmas* 的字符串数组;

```js
var list = document.querySelector('.output ul');
list.innerHTML = '';
var greetings = ['Happy Birthday!',
                 'Merry Christmas my love',
                 'A happy Christmas to all the family',
                 'You\'re all I want for Christmas',
                 'Get well soon'];

for(var i = 0; i < greetings.length; i++) {
  var input = greetings[i];
  if(greetings[i].indexOf('Christmas') !== -1) {
    var result = input;
    var listItem = document.createElement('li');
    listItem.textContent = result;
    list.appendChild(listItem);
  }
}
```

### 数字与字符串
#### 相互转换
非常神奇, 在JS当中, 数字和字符串可以直接通过函数`Number()`和`String()`进行转换, 与C语言不同.
```js
const myString = "123";
const myNum = Number(myString);
console.log(typeof myNum);
// number
console.log(myNum);
// 123
```

```js
const myNum2 = 123;
const myString2 = String(myNum2);
console.log(typeof myString2);
// string
console.log(myString2);
// "123"
```
> 对于浮点数同样成立.

#### 前后拼接
使用`+`将字符串类型和数字类型的变量or常量直接拼接, 得到的是以空格相隔的字符串:
```js
const name = "Front ";
const number = 242;
const combine = name + number;

console.log(combine); //Front 242

console.log(typeof(combine));  //string
```

### 数组
1. 存储任意类型元素--字符串，数字，对象，变量，**另一个数组**;
2. 可以 **混合** 元素类型:
```js
let random = ["tree", 795, [0, 1, 2]];
```
3. 像访问字符串一样, 利用索引访问数组元素;
4. $\underline{多维数组}$ 包含数组的数组结构称为~

#### split()
- 作用: 将一个字符串根据给定的字符分隔为字符串数组;
```js
let myData = "Manchester,London,Liverpool,Birmingham,Leeds,Carlisle";
let myArray = myData.split(",");
console.log(myArray);
 // ["Manchester", "London", "Liverpool", "Birmingham", "Leeds", "Carlisle"]
```

#### join()
`split`的反向操作, 给出分隔符号, 将数组的字符串拼接成一个字符串:
```js
let myNewString = myArray.join(",");
myNewString;
```

#### toString()
与`join`方法相似, 但是无法自定义分隔符, 默认为`,`:
```js
let dogNames = ["Rocket", "Flash", "Bella", "Slugger"];
dogNames.toString(); //Rocket,Flash,Bella,Slugger
```

#### push & pop
`push()`方法可以将1或多个元素添加到数组的 **末尾**:
1. 将会直接改写原来的数组,不需要重新赋值;
2. 该方法具有返回值, 且返回的是更新之后的数组长度(包含元素的个数);
```js
let myArray = [1, 2, 3];
let newLength = myArray.push(4, 5,"string");
console.log(myArray); // [1, 2, 3, 4, 5, "string"]
console.log(newLength); // 6
```

使用`.pop()`从数组中删除最后一个元素:
```js
myArray.pop(); //"string"
console.log(myArray); // [1, 2, 3, 4, 5]
```
1. 方法调用返回值就是删除的元素本身;
2. 直接对原始数组操作并赋值, 不需要另外的赋值操作;

> shift & unshift:
> 在功能上分别与`push`和`pop`相同, 但是作用于数组的开始位置.

## 条件语句
JS的条件语句与C语言十分相似, 在此仅给出官方文档的一些例子:
### 天气预报
```html
<label for="weather">选择今天的天气：</label
><select id="weather">
  <option value="">--作出选择--</option>
  <option value="sunny">晴天</option>
  <option value="rainy">雨天</option>
  <option value="snowing">雪天</option>
  <option value="overcast">阴天</option>
</select>

<p></p>
```
> `lable`当中的`for`标签与`select`标签的`id`属性对应, 用于关联两个标签.

```js
const select = document.querySelector("select");
const para = document.querySelector("p");

select.addEventListener("change", setWeather);

function setWeather() {
  const choice = select.value;

  switch (choice) {
    case "sunny":
      para.textContent = "阳光明媚。穿上短裤吧！去海滩，或公园，吃个冰淇淋。";
      break;
    case "rainy":
      para.textContent = "外面下着雨；带上雨衣和雨伞，不要在外面呆太久。";
      break;
    case "snowing":
      para.textContent =
        "大雪纷飞，天寒地冻！最好呆在家里喝杯热巧克力，或者去堆个雪人。";
      break;
    case "overcast":
      para.textContent =
        "虽然没有下雨，但天空灰蒙蒙的，随时都可能变天，所以要带一件雨衣以防万一。";
      break;
    default:
      para.textContent = "";
  }
}
```
> 1. 通过`querySelector`方法获取`select`和`p`标签;
> 2. 然后为`select`标签添加事件监听器, 当内容改变时触发 **change** 事件, 同时调用`setWeather`函数;
> 3. 进而通过 **switch** 语句处理不同天气的情况, 并设置相应的文字内容;
> 在线网页示例:[simple-switch](https://mdn.github.io/learning-area/javascript/building-blocks/simple-switch.html)


## 事件介绍
什么是$\underline{事件}$? 
- 用户选择、点击或者光标悬停在某一元素;
- 用户在键盘中按下某个按键;
- 网页结束加载;
- ...

$\underline{事件处理器}$ 为了响应事件, 我们需要编写一份JS代码块用于在事件发生之后运行. 这样的代码块称之为~.

### 处理点击事件
以点击事件为例, 介绍html与js如何进行事件处理的交互:
```html
<button> 改变颜色 </button>
```
```js
const btn = document.querySelector("button");

function random(number){
  return Math.floor(Math.random()*(number+1));
  
}

btn.addEventListener("click", ()=>{
  const rndCol = `rgb(${random(255)},${random(255)},${random(255)})`;
  document.body.style.backgroundColor = rndCol;
})
```
1. `Math.random()`方法生成一个介于[0,1)之间的随机数;
2. `*(number+1)`之后利用向下取整的方法`Math.floor()`将其转换为整数, 范围为[0,number];
> 假如输入的number为`4`, 则`random(4)`的结果可能为`0`, `1`, `2`, `3`, `4`中的一个;
> 假设输入的number为`3.6`, 则输出的结果还是0~4中的整数.
3. ``rndCol = `rgb(${random(255)},${random(255)},${random(255)})`` 采用的是在$\underline{模板字符串}$内部使用`${}`调用函数变量的方法.

### addEventListener()
`adEventListener`方法已经在之前的例子中出现过, 现在具体介绍其作用和语法.

通过`EventTarget.adddEventListener()`的方法, 将指定的监听器注册到对象上, 具体的语法如下:
```js
addEventListener(type, listener);
addEventListener(type, listener, options);
addEventListener(type, listener, useCapture);
```
- `type`: 事件类型, 如`click`, `mouseover`, `mouseout`, `keydown`, `keyup`等;
- `listener`: 事件处理函数, 该函数将在事件发生时被调用;
  - 包括 **回调函数** 以及 实现了 **EventListener 接口的对象**;
- `options`: 可选参数, 用于配置事件监听器的行为;
> 可以为单个事件添加多个事件监听器.


#### listener
$\underline{回调函数}$ 简单来说, ~指的是当某个事件发生时被调用的一段代码.
- 是一个函数, 但是只有等到特定的事件发生时才会执行.

实现了 EventListener 接口的对象:
- **特点**: 以对象作为listener, 对象中具有名为`handleEvent()`的方法;
- **作用**: 
  - 将事件处理封装到一个对象当中, 可以更好地组织代码;
  - 便于在对象中保存更多的状态信息;
```js
const listenerObject = {
    count: 0,
    handleEvent(event) {
        this.count++;
        console.log(`事件类型是：${event.type}，已触发 ${this.count} 次`);
    }
};

const button = document.querySelector('button');
button.addEventListener('click', listenerObject);
```

#### options
一个指定有关 listener 属性的可选参数对象.
##### Capture
- 含义:
  - 一个布尔值，表示 listener 会在该类型的事件捕获阶段传播到该 EventTarget 时触发;
  - 默认为false, 表示只有在冒泡阶段才触发.

- 区别:
  - `capture`和`useCapture`实际上指的都是 **监听器是否在捕获阶段触发** 的布尔值.
> 捕获阶段: 从最外层的元素开始, 逐层向内捕获事件, 直到事件到达目标元素.
  - 后来DOM的规范更新时引入了`options`参数, 此后`capture`取代了`useCapture`的作用.
  - 如果`addEventListener`的第三个参数不指定对象, 只有布尔值, 那么默认是在设置`useCapture`

> 可以先查看[事件传播的阶段](#事件传播的阶段)来辅助理解不同的阶段.

##### Once
- 含义:
  - 一个布尔值，表示 listener 在添加之后最多只调用一次;
  - 默认为false, 表示可以多次调用.
- e.g.
```js
child.addEventListener('click', () => {
    console.log('子元素 - 目标阶段');
},{once: true});
```
> `once`属性被设置为`true`, 当调用一次之后事件监听器会被自动清除. 因此只有第一次的点击才会console.

##### Passive
- 含义:
  - 一个布尔值，设置为 true 时，表示 listener 永远不会调用 preventDefault();

- 作用:
  - 明确不会在`listener`中不会调用`preventDefault()`方法, 即不会阻止浏览器的[默认行为](#默认行为);
  - 此时, 浏览器可以直接渲染默认行为的结果, 无需等待`listener`的执行与默认行为的检查, 从而提高了性能.
- Notice:
  - 如果设置`passive`为`true`, 则`listener`当中不可出现`preventDefault()`方法, 否则会报错.

e.g.
```js
document.addEventListener('wheel',()=>{
	event.preventDefault();
  console.log("scrolling");
},{passive: false});
```
- `wheel`事件的默认行为是滚动页面;
- `event.preventDefault();`表示会阻止鼠标滚动带来的页面滚动;


```js
document.addEventListener('wheel',()=>{
  console.log("scrolling");
},{passive: true});
```
> 明确不会阻止默认行为, 浏览器可以直接渲染页面的滚动效果, 因此提高了显示的效果.

```js
document.addEventListener('wheel',()=>{
  event.preventDefault();
  console.log("scrolling");
},{passive: true});
```
> `passive`的设置与`listener`内部矛盾, 将会报错.

##### Signal
用于有条件地移除事件监听器, 具体使用参见[可被移除的监听器](#可被移除的监听器).

### 事件传播的阶段
1. 捕获阶段 $\underline{capture\space phase}$: 事件从根节点开始向目标节点传播;
> e.g. 点击事件从document开始传播, 经过html,body直到目标元素.
2. 目标阶段阶段 $\underline{target\space phase}$: 事件到达目标元素;
3. 冒泡阶段 $\underline{bubble\space phase}$: 事件从目标元素开始沿着DOM树向上传播.

#### Case
```html
<div id="parent">
  parent
  <div id="child">child</div>
</div>
```
```js
const parent = document.querySelector('#parent');
const child = document.querySelector('#child');

parent.addEventListener('click', () => {
    console.log('父元素 - 冒泡阶段');
});

parent.addEventListener('click', () => {
    console.log('父元素 - 捕获阶段');
}, { capture: true });

child.addEventListener('click', () => {
    console.log('子元素 - 目标阶段');
});
```
上述的`child`被包裹在`parent`内部.
- 当点击`parent`时将会显示:
```bash
"父元素 - 捕获阶段"
"父元素 - 冒泡阶段"
```
> 由于设置了在捕获阶段就触发, 所以先触发了捕获阶段的监听器, 然后再触发冒泡阶段的监听器;

- 当点击`child`时将会显示:
```bash
"父元素 - 捕获阶段"
"子元素 - 目标阶段"
"父元素 - 冒泡阶段"
```
> `child`是整个事件流的目标元素, 所以触发时机介于二者之间.

#### Notice
1. 如果将`div`换成`button`, 则点击`child`时可能只会显示 **目标** 阶段的输出.
> 这是因为, 不同浏览器对于`button`元素的默认行为不同, 可能默认阻止了捕获阶段和冒泡阶段

2.  `event.stopPropagation();`加入该~~咒语~~代码可以在此停止事件的传播, 比如可以在上述的捕获阶段监听器加入该代码:
```js
parent.addEventListener('click', () => {
    console.log('父元素 - 捕获阶段');
    event.stopPropagation();
}, { capture: true });
```
> 此时, 点击`parent`时, 只会触发捕获阶段的监听器, 不会触发冒泡阶段的监听器.

### 可被移除的监听器
```html
<table id="outside">
  <tr>
    <td id="t1">one</td>
  </tr>
  <tr>
    <td id="t2">two</td>
  </tr>
</table>
```
```js
// 为 table 添加可被移除的事件监听器
const controller = new AbortController();
const el = document.getElementById("outside");
el.addEventListener("click", modifyText, { signal: controller.signal });

// 改变 t2 内容的函数
function modifyText() {
  const t2 = document.getElementById("t2");
  if (t2.firstChild.nodeValue === "three") {
    t2.firstChild.nodeValue = "two";
  } else {
    t2.firstChild.nodeValue = "three";
    controller.abort(); // 当值变为 "three" 后，移除监听器
  }
}
```
> - `AbortController`是一个构造函数, 用于创建一个可被移除的事件监听器的控制器;
> - `signal`属性是一个`AbortSignal`对象, 用于控制监听器的移除;
> - `controller.abort()`方法用于移除监听器;
> - 当`t2`的内容变为"three"时, 移除监听器, 使得`modifyText`函数不再执行. 此后, 点击`t2`不会触发`modifyText`函数.

具体的**构造步骤:**
1. 创建一个`AbortController`实例: `const controller = new AbortController();`
2. 在事件监听器内的参数中添加`signal: controller.signal`选项;
3. 在需要移除监听器的地方调用`controller.abort()`方法;

---

我们也可以直接使用`removeEventListener()`方法来移除事件监听器:
```js
removeEventListener(type, listener);
removeEventListener(type, listener, options);
removeEventListener(type, listener, useCapture);
```


- Notices:
  - 如果同一个对象上存在2个事件监听器, 且仅在`useCapture`参数存在差异, 那么需要先后2次调用`removeEventListener()`方法才能完全移除其事件监听器;
  - 如果无法匹配当前注册的事件监听器, 那么该函数将不会起任何作用;
  - `type`,`listener`参数必须完全匹配才能移除事件监听器;
  - 对于`options`参数:
    - 字段相同: 一定可以移除;
    - 字段不同: 需要与默认值false匹配才可以移除.
```js
element.addEventListener("mousedown", handleMouseDown, { passive: true });

element.removeEventListener("mousedown", handleMouseDown, { passive: true }); // 成功
element.removeEventListener("mousedown", handleMouseDown, { capture: false }); // 成功
element.removeEventListener("mousedown", handleMouseDown, { capture: true }); // 失败
element.removeEventListener("mousedown", handleMouseDown, { passive: false }); // 成功
element.removeEventListener("mousedown", handleMouseDown, false); // 成功
element.removeEventListener("mousedown", handleMouseDown, true); // 失败
```

---
**添加与移除**的结合使用:
```js
const body = document.querySelector("body");
const clickTarget = document.getElementById("click-target");
const mouseOverTarget = document.getElementById("mouse-over-target");

let toggle = false;
function makeBackgroundYellow() {
  body.style.backgroundColor = toggle ? "white" : "yellow";

  toggle = !toggle;
}

clickTarget.addEventListener("click", makeBackgroundYellow, false);

mouseOverTarget.addEventListener("mouseover", () => {
  clickTarget.removeEventListener("click", makeBackgroundYellow, false);
});
```


### 使用匿名函数
在上述`html`例子下:
```js
// 改变 t2 内容的函数
function modifyText(new_text) {
  const t2 = document.getElementById("t2");
  t2.firstChild.nodeValue = new_text;
}

// 用匿名函数为 table 添加事件监听器
const el = document.getElementById("outside");
el.addEventListener(
  "click",
  function () {
    modifyText("four");
  },
  false,
);
```
通过匿名函数封装代码, 将参数传入函数`modifyText`, 使得函数可以被调用.

### 使用箭头函数
```js
// 改变 t2 内容的函数
function modifyText(new_text) {
  var t2 = document.getElementById("t2");
  t2.firstChild.nodeValue = new_text;
}

// 用箭头函数为 table 添加事件监听器
const el = document.getElementById("outside");
el.addEventListener(
  "click",
  () => {
    modifyText("four");
  },
  false,
);
```
通过`=>{}`形式的箭头函数简化代码书写.

---
#### 比较匿名与箭头
匿名函数与箭头函数在此处的应用基本相同, 但是在`this`的指向上有所不同:
- 匿名函数与其他普通的JS函数:`this`指向调用它的对象之作用域(如果没有直接调用关系, 默认为全局对象, 且严格模式下为`undefined`);
```js
function sayHello() {
  console.log(this); // 在非严格模式下，this 指向 window
}
sayHello();
```

- 箭头函数的`this`继承自外部作用域, 即调用该方法的对象.
```js
const obj = {
  name: "ZJU",
  greet: function () {
    console.log(this.name); // this 指向 obj
  },
};
obj.greet(); // 输出：ZJU
```

- e.g.
```js
const obj = {
  name: "ZJU",
  getNameWithAnonymous: function () {
    return function () {
      console.log(this.name);
    };
  },
  getNameWithArrow: function () {
    return () => {
      console.log(this.name);
    };
  },
};

const anonymousFn = obj.getNameWithAnonymous();
anonymousFn(); // 输出：undefined

const arrowFn = obj.getNameWithArrow();
arrowFn(); // 输出：ZJU
```
> 进一步完善.

### 事件对象
$\underline{事件对象}$ 在事件处理函数的内部, 以固定指定名称出现的参数, 例如`event`,`e`,`evt`. 它被自动传递给事件处理函数，以提供额外的功能和信息。

`e.target`始终是对 **事件刚刚发生的元素** 的引用

## 表达式和运算符
### new()
$\underline{new}$ 用来创建对象实例的一个关键字. 
- 作用: **调用** 一个 构造函数, 并返回一个由该构造函数创建的对象实例.
#### 语法
```js
new constructor
new constructor()
new constructor(arg1)
new constructor(arg1, arg2)
new constructor(arg1, arg2, /* …, */ argN)
```
1. 如果没有指定参数, 默认为在不带参数的情况下调用构造函数. 即`new foo` 等价于 `new foo()`;
2. 构造函数内部的`this`将被绑定到新建的对象实例上;

- e.g. 
```js
function Car(color, brand) {
  this.color = color;   // 将 color 赋值给新对象
  this.brand = brand;   // 将 brand 赋值给新对象
}

const myCar = new Car("red", "Toyota");

console.log(myCar.color); // 输出 "red"
console.log(myCar.brand); // 输出 "Toyota"
```

使用`new()`的**步骤**:
 1. 定义构造函数;
 2. 使用`new()`并传入构造函数的参数;
 3. 将返回的对象实例赋值给一个变量;

#### 新增属性
- 为已经定义的对象实例直接新增属性, 但是不会影响其他相同类型的对象和构造函数本身:
```js
car1.color = "black" //为car1新增color属性
```
<br>

- 添加共享属性到构造函数中的`prototype`:
```js
function Car() {}
car1 = new Car();
car2 = new Car();

console.log(car1.color); // undefined

Car.prototype.color = "原色";
console.log(car1.color); // '原色'

car1.color = "黑色";
console.log(car1.color); // '黑色'

console.log(Object.getPrototypeOf(car1).color); // '原色'
console.log(Object.getPrototypeOf(car2).color); // '原色'
console.log(car1.color); // '黑色'
console.log(car2.color); // '原色'
```
> - 此处的构造函数名为`Car`, 因此通过`Car.prototype`可以访问到构造函数的原型对象;
> - `getPrototypeOf` 表示获取对象的**原型对象**, 因此此处均为最初定义的 **原色**.

#### new.target
函数通过`new.target`属性可以判断是否通过`new`关键字调用, 即构造.
- 如果函数是正常调用, 则返回`undefined`;
- 如果函数是通过`new`调用, 返回被调用的构造函数.

- e.g.
```js
function Car(color) {
  if (!new.target) {
    // 以函数的形式被调用。
    return `${color}车`;
  }
  // 通过 new 被调用。
  this.color = color;
}

const a = Car("红"); // a 是“红车”
const b = new Car("红"); // b 是 `Car { color: "红" }`
```

#### 对象类型与实例
$\underline{对象类型}$ 通过构造函数可以创建一个对象类型:
```js
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;
}
```

$\underline{对象实例}$ 通过使用`new()`方法, 由对象类型构造一个对象实例:
```js
const myCar = new Car("鹰牌", "Talon TSi", 1993);
```

#### 类与new
在JS当中, 类 **必须** 通过`new`调用.
> 可以优先阅读[类相关的知识](#类)

- e.g. 
```js
class Animal {
  //构造函数
  constructor(name) {
    this.name = name;
  }
  //实例方法
  greet() {
    console.log(`你好，我的名字是${this.name}`);
  }
}
```
对于上述的类, 必须使用如下的调用方式:
```js
const animal = new Animal("Dog"); // 正常
```
而下面这样类似于普通函数的调用方式会抛出错误:
```js
Animal("Cat"); // TypeError:  Class constructor Animal cannot be invoked without 'new'
```
<br>

在使用正确方法得到类的实例对象之后, 可以用访问属性的方式来调用实例方法:
```js
animal.greet(); // 输出 "你好，我的名字是Dog"
```

---

下面给出与普通函数的区别:
```js
function Car(model) {
  this.model = model;
}

const car = new Car("Toyota"); // 正常
Car("Honda"); // 不抛出错误，但 this 会指向全局对象.
const anotherCar = Car("cat"); //此时全局对象下的model值为 "cat", 覆盖了上一行的定义.
```
总结:
- 以构造函数形式呈现的普通函数, 可以被直接调用, 但是此时内部的参数赋值给了全局对象;
- 如果以new方法构造得到对象实例, 依旧正常.

---

# 补充
## 默认行为
$\underline{默认行为}$ 是指浏览器在某些事件发生时，自动执行的内置操作, 是浏览器的“默认反应”.

- 比如存在以下的默认行为:
  - 滚动事件：触摸屏上滑动手指，页面会滚动;
  - 拖拽文件到浏览器：浏览器会尝试加载文件;
  - 点击链接 `(<a href="...">)`：跳转到指定的 URL;

$\underline{阻止默认行为}$ 使用 `event.preventDefault()` 方法可以阻止事件的默认行为.

- e.g: 阻止链接跳转
```js
document.querySelector('a').addEventListener('click', function(event) {
    event.preventDefault(); // 阻止点击链接时的默认行为
    console.log('链接被点击，但没有跳转');
});
```

- 作用:
  - 通过阻止默认行为, 可以实现自定义逻辑.

## this

- `this`可以视作函数的一个隐参数, 是在函数被执行时创建的绑定;
- `this` 指向的是**当前函数的调用者**，而不是函数内部定义的变量.

<br>

- e.g.
```js
const obj = {
    a: "a in the obj",
    b: "b in the obj",
    f: function() {
        const b = "b in the function"; // 函数作用域
        console.log(this.b); // 访问 this.b
    }
};

const b = "b outside of the func";

obj.f();
```
> 此处的`f`

### 函数上下文中的this
- `this`参数的值取决于函数**如何**被调用, 而不是函数如何被定义.
```js
// 对象可以作为第一个参数传递给 'call' 或 'apply'，
// 并且 'this' 将被绑定到它。
const obj = { a: "Custom" };

// 使用 var 声明的变量成为 'globalThis' 的属性。
var a = "Global";

function whatsThis() {
  return this.a; // 'this' 取决于函数如何被调用
}

whatsThis(); // 'Global'; 在非严格模式下，'this' 参数默认为 'globalThis'
obj.whatsThis = whatsThis;
obj.whatsThis(); // 'Custom'; 'this' 参数被绑定到 obj
```
1. 同样是调用函数`whatsThis()`, 但是`this`参数被绑定到不同的对象上, 导致返回值不同;
2. 在非严格模式下, `this`参数默认指向`globalThis`, 即全局对象;
3. 对于典型函数, `this`指向函数访问的对象;

- e.g. 
```js
const obj = {
    b: "b in the obj",
    f: function() {
        const b = "b in the function"; // 函数作用域
        console.log(this.b); // 访问 this.b
    }
};

const b = "b outside of the func";

obj.f();
```
> 此处`f`作为`obj`对象的方法被调用, 因此普通函数的`this`指向`obj`.

- e.g. 直接调用的普通函数`this`指向全局:
```js
const obj = {
    a: "a in the obj",
    f: function() {
        const funcA = function () { return this.a }; // 普通函数，this 由调用方式决定
        console.log(funcA()); // 访问 this.a
    }
};

var a = "a in the global";
obj.f(); // "a in the global"
```
> - 此处的`funcA`并没有类似于作为对象的属性调用(`obj.funcA()`), 因此其`this`指向全局作用域(`window`), 输出`undefined`, 而是直接调用的形式, 因此其`this`指向全局作用域.





### 对this传值
使用`call()`以及`apply()`方法可以将`this`绑定到其他对象上.
#### call()
- 形式: `func.call(thisArg, arg1, arg2, ...)`
- e.g:
```js
function add(c, d) {
  return this.a + this.b + c + d;
}

const o = { a: 1, b: 3 };

// 第一个参数被绑定到隐式的 'this' 参数；
// 剩余的参数被绑定到命名参数。
add.call(o, 5, 7); // 16
```

#### apply()
- 形式: `func.apply(thisArg, [argsArray])`
- e.g:
```js
function add(c, d) {
  return this.a + this.b + c + d;
}

const o = { a: 1, b: 3 };

// 第一个参数被绑定到隐式的 'this' 参数；
// 第二个参数是一个数组，其成员被绑定到命名参数。
add.apply(o, [10, 20]); // 34
```

#### bind()
- 形式: `f.bind(someObject)`;
- **作用**: 
  - 创建一个新的函数(需要重新赋值), 具有与`f`相同的函数体和作用域;
  - 新函数的`this`被 **永久地** 绑定到`someObject`, 不随调用方式的变化而变化.
- **限制**: 
  - `bind`无法多次生效. 即对函数f`bind`得到的g, 无法继续用`bind`得到期望的h;
- e.g. 多次`bind`:
```js
function f() {
  return this.a;
}

const g = f.bind({ b: "azerty" });
console.log(g()); // undefined

const h = g.bind({ a: "yoo" }); // bind 只能生效一次！
console.log(h()); // undefined

const o = { a: 37, f, g, h };
console.log(o.a, o.f(), o.g(), o.h()); // 37 37 undefined undefined
```
> - 由于`bind`只能对一个原始函数作用, 因此由f得到的g无法继续由`bind`绑定`this`得到期望的h, 此处h的`this`依旧是`{b: "azerty"}`, 因此在输出对象`a`时显示`undefined`;
> - `o.f()`的调用是普通函数的调用, 因此其`this`继承自对象`o`, 输出`37`;

- e.g. 对象
```js
function f() {
  return this.a + " " + this.c;
}

const g = f.bind({ b: "azerty" , c:"ccc"});
console.log(g()); // "undefined ccc"

const h = g.bind({ a: "yoo" }); // bind 只能生效一次！
console.log(h()); // "undefined ccc"

const o = { a: 37, f, g, h };
console.log(o.a, o.f(), o.g(), o.h()); // 37 37 azerty azerty
```
> - `bind`绑定的`this`是永久覆盖, 而非简单叠加;
> - 由于`bind`绑定的`this`不随者调用方式的变化而变化, 因此即使处于对象`o`当中, `g`,`h`依旧不会输出`o`中的`a`.

### 箭头函数中的this


使用 call()、apply() 或 bind() 调用箭头函数时，传入的 this 值会被忽略，但其他参数仍然会正常传递。


普通函数:
```js
const a = "a in the global";
const foo = function () {return this.a};

const obj = {
 a: "a in the obj",
 f: foo
};

console.log(obj.f()); // "a in the obj"
```

`call()`、 `apply()`、 `bind()` 无法改变箭头函数的`this`(但是call与apply的其他参数可以正常传递:
```js
const foo = ()=> this.a;

const obj = {
 a: "a in the obj",
 f: foo.bind({a:"a in the bind"}) // 显式绑定 this 到 obj, 但是无法生效
};

console.log(obj.f()); // undefined

```
> 换成普通函数则输出`a in the obj`.


- 全局作用域
```js
var a = "a in the global";
const foo1 = () => this.a;

const obj = {
	a: "a in the obj",
	f: ()=> a
};

console.log(obj.f());
```

## 作用域
$\underline{作用域}$ 指当前的执行上下文, 在其中的值和表达式可以被访问. 
- 全局作用域: 脚本模式运行所有代码的默认作用域;
- 模块作用域: 模块模式中运行代码的作用域;
- 函数作用域: 由函数创建的作用域
- 块级作用域: 由`let`或`const`声明的变量的作用域.(对于`var`无效);

```js
{
  var x = 1;
}
console.log(x); // 1

{
  const x = 1;
}
console.log(x); // undefined
```

Notices:
- 对象本身并不会创建作用域, 只是一个键值对的集合;
- 箭头函数也不会创建自己的作用域, 而是 **继承** 外层作用域中的`this`;

### 变量与作用域
- `var`在全局作用域中声明时会成为 **全局对象** (`window`或`global`)的属性;
- `let`和`const`即使在全局作用域中声明, 也不会成为全局对象的属性;
```js
var a = "1";
let b = "2";

window.a; // "1"
window.b; // undefined
```
> 因此, 建议在全局作用域中不要使用`var`声明变量, 而使用`let`或`const`声明变量. 从而避免导致意外的覆盖和冲突.


### 函数与作用域
#### 普通函数
普通函数和匿名函数的作用域继承自其定义时的作用域.
```js
const obj = {
    a: "a in the obj",
    insideObj: {
        g: function() {
            return this.a; // 普通函数，this 动态绑定到 insideObj
        }
    },
    f: function() {
        return this.a; // 普通函数，this 动态绑定到 obj
    }
};

console.log(obj.f());        // "a in the obj"
console.log(obj.insideObj.g()); // undefined，因为 insideObj 中没有 a
```

#### 箭头函数
e.g. **箭头函数继承外层作用域**:
```js
var a = "a in the global";

const obj = {
    a: "a in the obj",
    insideObj: {
        g: () => this.a
    },
    f: () => this.a
};

console.log(obj.f());        // "a in the global"
console.log(obj.insideObj.g()); //"a in the global"
```
由于对象不会创建作用域, 因此此处的箭头函数的`this`继承了外层作用域(window)的`this`, 且`var`创建的变量存在于全局作用域中.

## 语法糖
$\underline{语法糖}$ 一种让代码更简洁、更易读的语法形式.
- 本质上没有增加语言的功能, 而是对已有功能的 **包装** 或者优化;
- **可读性提升**: ~~让代码更填~~ 使得代码更加容易理解和书写;
- **底层实现**: 实质上依旧用基础的语法实现.

### 类
类 `class` 是 ES6 引入的语法糖, 它提供了面向对象编程的简洁语法. 本质上是对原型继承`prototype`的封装.

使用`class`的写法:
```js
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    console.log(`Hello, my name is ${this.name}`);
  }
}

const person = new Person("Alice");
person.greet(); // 输出：Hello, my name is Alice
```

等价的原型写法:
```js
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function() {
  console.log(`Hello, my name is ${this.name}`);
};

const person = new Person("Alice");
person.greet(); // 输出：Hello, my name is Alice
```

### 箭头函数
箭头函数简化了函数定义的书写, 其本质上依旧是一个普通函数, 因此也是语法糖的一种.

- e.g.
```js
// 使用箭头函数
const add = (a, b) => a + b;

// 等价的普通函数
const add = function add(a, b) {
  return a + b;
}
```

### 结构赋值
$\underline{结构赋值}$ 手动提取**对象**属性的语法糖.

- 使用结构赋值:
```js
const person = {name:"Zhuo", gender:"male"};

const {name, gender} = person;
```

- 等价的原型写法:
```js
const person = {name:"Zhuo", gender:"male"};

const name = person.name;
const gender = person.gender;
```

#### 赋值规则
结构赋值时, 基于 **属性名匹配** 而非顺序. 
因此, 对象结构的`{}`内部属性必须和 **对象的属性名** 相对应.

**错误**的示例:
```js
const person = { name: "Alice", age: 25 };
const { a, b } = person;

console.log(a); // 输出：undefined
console.log(b); // 输出：undefined
```

**重命名属性**的写法:
```js
const person = { name: "Alice", age: 25 };
const { name: a, age: b } = person;

console.log(a); // 输出：Alice
console.log(b); // 输出：25
```

**手动赋值**: 对于结构对象中不存在的属性, 可以采取普通赋值的方式与结构赋值相结合:
```js
const person = { name: "Alice" };
const { name, age = 30 } = person;

console.log(name); // 输出：Alice
console.log(age);  // 输出：30 （因为 person 中没有 age 属性，所以使用了默认值）
```
#### 数组的结构赋值
上述讨论的结构赋值都是对 **对象** 的结构赋值, 对于数组同样可以结构赋值, 且赋值规则与对象相反—— **基于顺序**赋值:
```js
const arr = ["Alice", 25];
const [a, b] = arr;

console.log(a); // 输出：Alice
console.log(b); // 输出：25
```
