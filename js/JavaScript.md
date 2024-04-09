# JavaScript

## 数据类型

JavaScript 提供八种不同的数据类型

|    数据类型    |              含义               |
| :------------: | :-----------------------------: |
|   undefined    |             未定义              |
|      null      |               空                |
|    boolean     |              布尔               |
| string(不可变) |    字符串(可以通过下标访问)     |
|     symbol     | 表示一个独一无二的值（es6新增） |
|     number     |              数字               |
|     bigint     |             大整数              |
|     object     |              对象               |

### 引用类型

`Object,Array,Function`

这两种类型最主要的区别在于声明变量时候内存分配不同，对**基本类型**而言，它的变量值是直接存储在**栈内存**中的

对于**引用类型**而言，声明变量的时候，栈内存存储的是值所在的内存地址，而真正的值存放在**堆内存**中

### 关于string的问题

字符串是基本类型，所以，对程序员来说，

```js
var i=1;
i=2;
var str="a";
str="b";
```

效果是差不多的。

**当字符串通过`[]`来取出其中一个字符的时候，会转换为一个 String 对象，而 String 对象的这个属性是不可写的，对他赋值不会有任何效果。在严格模式下对它的赋值是会抛异常的。**

```js
let str="Hello";
str[0]='J';
console.log(str);//输出Hello

str="Jello";
console.log(str);//输出Jello
```

## 声明变量

通过在变量前面使用关键字 `var`，声明一个变量

```javascript
var name;
```

上面代码的意思是创建一个名为 `name` 的变量。 在 JavaScript 中我们以分号结束语句。 变量名称可以由数字、字母、美元符号 `$` 或者下划线 `_` 组成，但是不能包含空格或者以数字为开头。

当 JavaScript 中的变量被声明的时候，程序内部会给它一个初始值 `undefined`。 当你对一个值为 `undefined` 的变量进行运算操作的时候，算出来的结果将会是 `NaN`，它的意思是 "Not a Number"。 如果你用 `undefined` 变量连接一个字符串，你将得到一个 `undefined` 的 字符串。

```javascript
{
 var a;
 a=a+"1";
}
//输出：'undefined1'
{ 
 var b;
 b=b+1;
}
//输出：NaN
```

### var 和 let

使用 `var` 关键字声明变量的最大问题之一是你可以轻松覆盖变量声明：

```javascript
var camper = "James";
var camper = "David";
console.log(camper);
```

在上面的代码中，`camper` 变量最初声明为 `James`，然后被覆盖为 `David`。 然后控制台显示字符串 `David`。

在小型应用程序中，你可能不会遇到此类问题。 但是随着你的代码库变大，你可能会意外地覆盖一个你不打算覆盖的变量。 由于此行为不会引发错误，因此搜索和修复错误变得更加困难。

ES6 中引入了一个名为 `let` 的关键字，这是对 JavaScript 的一次重大更新，以解决与 `var` 关键字有关的潜在问题。 你将在后面的挑战中了解其他 ES6 特性。

如果将上面代码中的 `var` 替换为 `let` ，则会导致错误：

```javascript
let camper = "James";
let camper = "David";
```

所以不像 `var`，当你使用 `let` 时，同名的变量只能声明一次。

**使用 `var` 关键字声明变量时，它是全局声明的，如果在函数内部声明则是局部声明的。**

var是**函数作用域**，let是**块作用域**。

**在函数中声明了var，整个函数内都是有效的，比如说在for循环内定义的一个var变量，实际上其在for循环以外也是可以访问的。**

**而let由于是块作用域，所以如果在块作用域内定义的变量，比如说在for循环内，在其外面是不可被访问的，所以for循环推荐用let**

>默认全局变量（比如最外部定义的变量，比如alert函数，实际上可以windows.alert）绑定在windows对象下,这也是js唯一的全局作用域。但不同的js文件使用相同全局变量时，会产生冲突，所以把自己的代码所需全局变量放在自己定义的作用域中，这也是很多框架的写法，比如jQuery，使用时候，jQuery.xxx。为简便用$符号代替了。

如何定义自己定义的作用域呢?其实就是自己创建对象。

```js
var balance={};
balacne.name="balance";
balance.add=function(a,b){
    return a+b;
}
```

### const

关键字 `let` 并不是声明变量的唯一新方法。 在 ES6 中，你还可以使用 `const` 关键字声明变量。`const` 具有 `let` 的所有出色功能，另外还有一个额外的好处，即使用 `const` 声明的变量是只读的。 它们是一个常量值，这意味着一旦一个变量被赋值为 `const`，它就不能被重新赋值：

```javascript
const FAV_PET = "Cats";
FAV_PET = "Dogs";
```

由于重新分配 `FAV_PET` 的值，控制台将显示错误。你应该始终使用 `const` 关键字命名不想重新分配的变量。 这有助于避免给一个常量进行额外的再次赋值。**注意：** 通常，开发者会用大写字母作为常量标识符，用小写字母或者驼峰命名作为变量（对象或数组）标识符。 你将在后面的挑战中了解有关对象、数组以及不可变和可变值的更多信息。 同样在后面的挑战中，你将看到大写、小写或驼峰式变量标识符的示例。

**const保证的不是变量的值不动，而是变量指向的那个内存地址所保存的数据不能改变**。对于基本数据类型，值就保存在变量指向的那个内存地址，所以用const定义的基本数据类型的值是不能改变了，而对于数组和对象，它们属于引用数据类型，变量保存的只是一个指向实际数据的指针，而这个指针指向的内存地址才是真正保存数据的值的地方，所以const只能保证这个指针不能变，但不能保证他所指向的这个内存地址里面的值不能变。

### 单双引号

JavaScript 中的字符串可以使用开始和结束都是同类型的单引号或双引号表示。 与其他一些编程语言不同的是，单引号和双引号的功能在 JavaScript 中是相同的。

```javascript
const doubleQuoteStr = "This is a string"; 
const singleQuoteStr = 'This is also a string';
```

当你需要在一个字符串中使用多个引号的时候，你可以使用单引号包裹双引号或者相反。 常见的场景比如在字符串中包含对话的句子需要用引号包裹。

```javascript
const conversation = 'Finn exclaims to Jake, "Algebraic!"';
```

 另外比如在一个包含有 `<a>` 标签的字符串中，标签的属性值需要用引号包裹。然而，如果你需要在其中使用外面的引号，这就成为一个问题。 记住，一个字符串在**开头和结尾处有相同的引号**。如果在中间使用了相同的引号，字符串会提前中止并抛出错误。

```javascript
const goodStr = 'Jake asks Finn, "Hey, let\'s go on an adventure?"'; 
const badStr = 'Finn responds, "Let's go!"';
```

在这里 `badStr` 会产生一个错误。

灵活使用引号组合，从而无需转义字符

```javascript
const myStr = '<a href="http://www.example.com" target="_blank">Link</a>';
```

### 转义字符

| 代码 |  输出  |
| :--: | :----: |
| `\'` | 单引号 |
| `\"` | 双引号 |
| `\\` | 反斜杠 |
| `\n` | 换行符 |
| `\t` | 制表符 |
| `\r` |  回车  |
| `\b` | 退格符 |
| `\f` | 换页符 |

*请注意，反斜线本身必须被转义，才能显示为反斜线。*

### 数组

以左方括号开始定义一个数组，以右方括号结束，里面每个元素之间用逗号隔开

```javascript
const sandwich = ["peanut butter", "jelly", "bread"];
//数组也可同时放其他类型值
const myArray = ["dddd",656];

//多维数组
const myArray = [["hh",2,3],["pp",5,6]];
```

与字符串不同，数组的**条目**是 可变的 并且可以自由更改，即使数组是用 `const` 声明的。

```javascript
const ourArray = [50, 40, 30];
ourArray[0] = 15;
```

`ourArray` 值为 `[15, 40, 30]`。

### 数组声明问题

下面两组代码返回两种不同的值，请看

```js
let newArray = [];
let row = [];
for (let i = 0; i < m; i++) {
   // 添加第 m 行到 newArray
   for (let j = 0; j < n; j++) {
       // 将 n 个 0 推入当前行以创建列
       row.push(0);
   }
   // 将当前行（已有 n 个 0）推送到数组
   newArray.push(row);
   for (let j = 0; j < n; j++) {//指向原有数组！！！！！！！！！！！！！！！！！！！
        // 将 n 个 0 移出
        row.shift();
    }
}
```

```json
[ [], [], [] ]
```

```js
let newArray = [];
let row = [];
for (let i = 0; i < m; i++) {
  // 添加第 m 行到 newArray
  row=[];//指向空数组！！！！！！！！！！！！！！！！！！！
  for (let j = 0; j < n; j++) {
    // 将 n 个 0 推入当前行以创建列
    row.push(0);
  }
  // 将当前行（已有 n 个 0）推送到数组
  newArray.push(row);
}
return newArray;
```

```json
[ [ 0, 0 ], [ 0, 0 ], [ 0, 0 ] ]
```



### 函数

在 JavaScript 中，我们可以把代码的重复部分抽取出来，放到一个函数 （functions）中。

```js
function functionName() {
  console.log("Hello World");
}
```

你可以通过函数名加上后面的小括号来调用（invoke）这个函数，就像这样： `functionName();` 每次调用函数时，它都会在控制台上打印消息 `Hello World`。 每次调用函数时，大括号之间的所有代码都将被执行。

#### 参数

函数的参数 （parameters）在函数调用中充当传入函数的输入占位符（也叫形参）。 函数调用时，参数可以为一个或多个。 调用函数时输入（或传递 "passed"）的实际值被称为参数（arguments）。

这是带有两个参数的函数，`param1` 和 `param2`：

```javascript
function testFun(param1, param2) {
  console.log(param1, param2);
}
```

然后我们可以调用 `testFun`，就像这样： `testFun("Hello", "World");`。 我们传入了两个字符串参数， `Hello` 和 `World`。 在函数中，`param1` 等于字符串 `Hello` 以及 `param2` 等于字符串 `World`。 请注意，`testFun` 函数可以多次调用，每次调用时传递的参数会决定参数的实际值。

我们可以通过函数的参数（arguments）把值传入函数， 也可以使用 `return` 语句把数据从一个函数中传出来。

```js
function plusThree(num) {
  return num + 3;
}

const answer = plusThree(5);
//plusThree 带有一个参数（argument）num，并返回（return）一个等于 num + 3 的值。
```

`answer` 的值为 `8`。

### 匿名函数/箭头函数

在 JavaScript 里，我们会经常遇到不需要给函数命名的情况，尤其是在需要将**一个函数作为参数传给另外一个函数**的时候。 这时，我们会创建匿名函数。 因为这些函数不会在其他地方复用，所以我们不需要给它们命名。

这种情况下，我们通常会使用以下语法：

```js
const myFunc = function() {
  const myVar = "value";
  return myVar;
}
```

ES6 提供了其他写匿名函数的方式的语法糖。 你可以使用**箭头函数**：

```js
const myFunc = () => {
  const myVar = "value";
  return myVar;
}
```

当**不需要函数体，只返回一个值的时候**，箭头函数允许你省略 `return` 关键字和外面的大括号。 这样就可以将一个简单的函数简化成一个单行语句。

```js
const myFunc = () => "value";
```

**当箭头函数要返回对象的时候，为了区分于代码块，要用 () 将对象包裹起来**

```js
var f = (id,name) => ({id: id, name: name});
f(6,2);  // {id: 6, name: 2}
```

#### 匿名函数参数/箭头函数参数

和一般的函数一样，你也可以给箭头函数传递参数。

```js
const doubler = (item) => item * 2;
doubler(4);
```

`doubler(4)` 将返回 `8`。

如果箭头函数只有一个参数，则可以省略参数外面的括号。

```js
const doubler = item => item * 2;
```

可以给箭头函数传递多个参数。

```js
const multiplier = (item, multi) => item * multi;
multiplier(4, 2);
```

`multiplier(4, 2)` 将返回 `8`

###  默认参数

**ES6 里允许给函数传入默认参数**，来构建更加灵活的函数。

```js
const greeting = (name = "Anonymous") => "Hello " + name;

console.log(greeting("John"));
console.log(greeting());
```

控制台将显示字符串 `Hello John` 和 `Hello Anonymous`。

默认参数会在参数没有被指定（值为 undefined）的时候起作用。 在上面的例子中，参数 `name` 会在没有得到新的值的时候，默认使用值 `Anonymous`。 你还可以给多个参数赋予默认值。

### 可变参数

ES6 推出了用于函数参数的 **rest 操作符**帮助我们创建更加灵活的函数。 rest 操作符可以用于创建有一个变量来接受多个参数的函数。 这些参数被储存在一个可以在函数内部读取的数组中。

```js
function howMany(...args) {
  return "You have passed " + args.length + " arguments.";
}
console.log(howMany(0, 1, 2));
console.log(howMany("string", null, [1, 2, 3], { }));

function howMany(a,b,...args) {//args接收除a,b之外的所有参数，这也是和arguments不同的地方
  return "You have passed " + args.length + " arguments.";
}
```

控制台将显示字符串 `You have passed 3 arguments.` 和 `You have passed 4 arguments.`。

rest 参数使我们不需要使用 `arguments` 对象，const允许我们对传递给函数 `howMany` 的参数数组使用数组方法。

```javascript
const sum = (...args) => { //匿名函数和可变参数实例
 let total = 0;
 for (let i = 0; i < args.length; i++) {
  total += args[i];
 }
 return total;
}
```

**可变参数无法用默认参数初始化**

### arguments

`arguments`是一个js给定的关键字，**用于标识函数接收到的所有参数**。是一个类数组对象

由于js是一种弱类型的语言，没有重载机制，当我们重写函数时，会将原来的函数直接覆盖，这里我们能利用arguments，来判断传入的实参类型与数量进行不同的操作，然后返回不同的数值。

```js
function add() {
    var len = arguments.length,
        sum = 0;
    for(;len--;){
        sum += arguments[len];
    }
    return sum;
}
 
console.log( add(1,2,3) );   //6
console.log( add(1,3) );     //4
console.log( add(1,2,3,5,6,2,7) );   //26
```

### 展开运算符 ...

> 官方https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Spread_syntax
>
> 人话https://zhuanlan.zhihu.com/p/394698999

- 展开操作符是三个点（...）
- 它可以应用到可迭代的对象上，例如数组或字符串
- 它把可迭代对象展开为其内部的各个元素
- 可以在函数调用时使用展开语法

ES6 引入了展开操作符，可以展开数组以及需要多个参数或元素的表达式。

下面的 ES5 代码使用了 `apply()` 来计算数组的最大值：

```js
var arr = [6, 89, 3, 45];
var maximus = Math.max.apply(null, arr);
```

`maximus` 的值为 `89`。

我们必须使用 `Math.max.apply(null, arr)`，因为 `Math.max(arr)` 返回 `NaN`。 `Math.max()` 函数中需要传入的是一系列由**逗号分隔的参数**，而不是一个数组。 展开操作符可以提升代码的可读性，使代码易于维护。

```js
const arr = [6, 89, 3, 45];
const maximus = Math.max(...arr);
```

`maximus` 的值应该是 `89`。

`...arr` 返回一个解压缩的数组。 换句话说，它展开了数组。 然而，展开操作符**只能够在函数的参数中或者数组中使用**。 例如：

```js
const spreaded = [...arr];
```

下面的代码将不能运行：

```js
const spreaded = ...arr;
```

#### 展开运算之复制

我们可以用展开运算符来复制数组：

```js
let thisArray = [true, true, undefined, false, null];
let thatArray = [...thisArray];
```

`thatArray` 等于 `[true, true, undefined, false, null]`。 `thisArray` 保持不变， `thatArray` 包含与 `thisArray` 相同的元素。

#### 展开运算之合并（插入）

展开语法（spread）的另一个重要用途是合并数组，或者将某个数组的所有元素插入到另一个数组的任意位置。 我们也可以使用 ES5 的语法连接两个数组，但只能让它们首尾相接。 而展开语法可以让这样的操作变得极其简单：

```js
let thisArray = ['sage', 'rosemary', 'parsley', 'thyme'];

let thatArray = ['basil', 'cilantro', ...thisArray, 'coriander'];
```

```json
thatArray 会有值 ['basil', 'cilantro', 'sage', 'rosemary', 'parsley', 'thyme', 'coriander']
```

使用展开语法，我们就可以很方便的实现一个用传统方法会写得很复杂且冗长的操作。

### 解构运算

> 解构详解https://blog.csdn.net/Zhang_wang_yun/article/details/129976907

解构赋值是 ES6 引入的新语法，用来从数组和对象中提取值，并优雅地对变量进行赋值。

有如下 ES5 代码：

```js
const user = { name: 'John Doe', age: 34 };

const name = user.name;
const age = user.age;
```

`name` 的值应该是字符串 `John Doe`， `age` 的值应该是数字 `34`。

下面是使用 ES6 解构赋值语句，实现相同效果：

```js
const { name, age } = user;
```

同样，`name` 的值应该是字符串 `John Doe`， `age` 的值应该是数字 `34`。

#### 对象解构

```json
1.赋值运算符 = 左侧的 {} 用于批量声明变量，右侧对象的属性值将被赋值给左侧的变量
2.对象属性的值将被赋值给与属性名相同的变量
3.注意解构的变量名不要和外面的变量名冲突否则报错.
4.对象中找不到与变量名一致的属性时变量值为 undefine
```

**与数组解构不同的是，对象解构不需要严格按照顺序取值，而只要按照变量名去取对应属性名的值，若取不到对应属性名的值，则为undefined 。**

如果在解构对象之前有一个对象的同名变量，在后面进行解构赋值的时候将会报错，提示我们标识符“uname”已经声明。

```js
let uname = 1//同名变量
const user = {
  uname: '张三', 
  age: 18
}
const { uname, age } = user
console.log(uname);//会报uname已声明错误
console.log(age);
```
这个时候可以给解构的值赋予一个新的变量名， 通过在赋值的时候将新的变量名放在冒号后面来解决这个问题。

```js
let uname = 1
const user = {
  uname: '张三', 
  age: 18
}
const { uname: username, age } = user
console.log(username);
console.log(age);
```
还是以上个例子的对象来举例：

```js
const user = { name: 'John Doe', age: 34 };
```

这是指定新的变量名的例子：

```js
const { name: userName, age: userAge } = user;
```

你可以这么理解这段代码：获取 `user.name` 的值，将它赋给一个新的变量 `userName`，等等。 `userName` 的值将是字符串 `John Doe`，`userAge` 的值将是数字 `34`。

#### 对象结构中...运算符

对象结构也可以使用**...运算符**，这会将剩余属性全部解构给一个对象。

```js
const user = {
    uname: '张三',
    age: 18,
    eat(){
       console.log("eat apple");
    }
}
let {uname,age,...person}=user;
console.log(uname);
console.log(age);
console.log(person);
```

```json
张三                    
18                      
{ eat: [Function: eat] }
```

#### 解构嵌套对象

使用与前面的例子中类似的对象：

```js
const user = {
  johnDoe: { 
    age: 34,
    email: 'johnDoe@freeCodeCamp.com'
  }
};
```

这是解构对象的属性值赋值给具有相同名字的变量：

```js
const { johnDoe: { age, email }} = user;
```

这是将对象的属性值赋值给具有不同名字的变量：

```js
const { johnDoe: { age: userAge, email: userEmail }} = user;
```

#### 数组解构

与数组解构不同，数组的扩展运算会将数组里的所有内容分解成一个由逗号分隔的列表。 所以，你不能选择哪个元素来给变量赋值。

```js
1.赋值运算符 = 左侧的 [ ] 用于批量声明变量，右侧数组的单元值将被赋值给左侧的变量
2.变量的顺序对应数组单元值的位置依次进行赋值操作。
3.变量的数量大于单元值数量时，多余的变量将被赋值为 undefine
4.防止有undefined传递单元值的情况，可以设置默认值。

const [a='张三',b='李四',c='王五',d='赵六'] = ['张三','李四','王五'];//也可以使用,,来跳过某个值
console.log(a,b,c,d);//张三 李四 王五 赵六 
```

#### 有用的应用，交换变量值

```js
let a = 8, b = 6;
[a,b]=[b,a];
```

### 箭头函数

> 箭头函数详解 https://www.cnblogs.com/LIXI-/p/16471548.html

在 JavaScript 里，我们会经常遇到不需要给函数命名的情况，尤其是在需要将一个函数作为参数传给另外一个函数的时候。 这时，我们会创建匿名函数。 因为这些函数不会在其他地方复用，所以我们不需要给它们命名。

```js
const myFunc = function() {
  const myVar = "value";
  return myVar;
}
```

ES6 提供了其他写匿名函数的方式的语法糖。 你可以使用**箭头函数**：

```js
const myFunc = () => {
  const myVar = "value";
  return myVar;
}
```

当不需要函数体，只返回一个值的时候，箭头函数允许你省略 `return` 关键字和外面的大括号。 这样就可以将一个简单的函数简化成一个单行语句。

```js
const myFunc = () => "value";
```

**当箭头函数要返回对象的时候，为了区分于代码块，要用 () 将对象包裹起来**

```js
var f = (id,name) => ({id: id, name: name});
f(6,2);  // {id: 6, name: 2}
```

#### 箭头函数传参

和一般的函数一样，你也可以给箭头函数传递参数。

```js
const doubler = (item) => item * 2;
doubler(4);
```

`doubler(4)` 将返回 `8`。

如果箭头函数只有一个参数，则可以省略参数外面的括号。

```js
const doubler = item => item * 2;
```

可以给箭头函数传递多个参数。

```js
const multiplier = (item, multi) => item * multi;
multiplier(4, 2);
```

`multiplier(4, 2)` 将返回 `8`。

### 作用域

在 JavaScript 中，作用域涉及到变量的作用范围。 在函数外定义的变量具有 全局 作用域。 这意味着，具有全局作用域的变量可以在代码的任何地方被调用。

**未使用 **`let` 或 `const` 关键字声明的变量会在 `global` 范围内自动创建。 当在代码其他地方无意间定义了一个变量，刚好变量名与全局变量相同，这时会产生意想不到的后果。 你应该总是用 `let` 或 `const` 声明你的变量。

也就是说不用关键字默认是全局作用域，但如果变量重名，会出问题，所以建议使用关键字

#### 局部作用域

在一个函数内声明的变量，以及该函数的参数都具有局部（local）作用域。 这意味着它们只在该函数内可见。

```js
function myTest() {
  const loc = "foo";
  console.log(loc);
}

myTest();
console.log(loc);
```

`myTest()` 函数调用将在控制台中显示字符串 `foo`。 `console.log(loc)` 行（在 `myTest` 函数之外）将抛出错误，因为 `loc` 未在函数之外定义。

一个程序中有可能具有相同名称的局部变量 和全局变量。 在这种情况下，局部变量将会优先于全局变量。

```js
const someVar = "Hat";

function myFun() {
  const someVar = "Head";
  return someVar;
}
```

函数 `myFun` 将会返回字符串 `Head`，因为局部变量的优先级更高。

### return

函数一般用 `return` 语句来返回值，但这不是必须的。 在函数没有 `return` 语句的情况下，当你调用它时，该函数会执行内部代码，返回的值是 `undefined`。

```js
let sum = 0;

function addSum(num) {
  sum = sum + num;
}

addSum(3);
```

`addSum` 是一个没有 `return` 语句的函数。 该函数将更改全局变量 `sum`，函数的返回值为 `undefined`。

### `==`和`=== `

严格相等运算符（`===`）是相对相等操作符（`==`）的另一种比较操作符。 与相等操作符转换数据两类型不同，严格相等运算符不会做类型转换。

如果比较的值类型不同，那么在严格相等运算符比较下它们是不相等的，会返回 false 。

```js
3 ===  3  // true
3 === '3' // false

3=='3' //true
```

`3 == '3'` 返回 `true` ，因为 JavaScript 执行了从字符串到数字类型的转换。 `3 === '3'` 返回 `false`，因为类型不同，没有进行类型转换。

不相等运算符（`!=`）与相等运算符是相反的。 这意味着不相等并返回 `false` 的地方，用相等运算符会返回 `true`，*反之亦然*。 与相等运算符类似，不相等运算符在比较的时候也会转换值的数据类型。

```js
1 !=  2    // true
1 != "1"   // false，1=="1"
```

严格不相等运算符（`!==`）与全等运算符是相反的。 这意味着严格不相等并返回 `false` 的地方，用严格相等运算符会返回 `true`，*反之亦然*。 严格不相等运算符不会转换值的数据类型。

```js
3 !==  3  // false
3 !== '3' // true
```

### 大于、大于等于

使用大于运算符（`>`）来比较两个数字。 如果大于运算符左边的数字大于右边的数字，将会返回 `true`。 否则，它返回 `false`。

与相等运算符一样，大于运算符在比较的时候，会**转换值的数据类型**。

```js
5   >  3  // true
7   > '3' // true
2   >  3  // false
'1' >  9  // false
```

使用大于等于运算符（`>=`）来比较两个数字的大小。 如果大于等于运算符左边的数字比右边的数字大或者相等，会返回 `true`。 否则，会返回 `false`。

与相等运算符相似，大于等于运算符在比较的时候会**转换值的数据类型**。

```js
6   >=  6  // true
7   >= '3' // true
```

**小于运算符类似，略**

### 模板字符串

> https://www.runoob.com/js/js-string-templates.html

模板字符串是 ES6 的另外一项新的功能。 这是一种可以轻松构建复杂字符串的方法。

模板字符串可以使用多行字符串和字符串插值功能。

模板字符串支持换行,只需代码内换行，显示也就换行

请看以下代码：

```js
const person = {
  name: "Zodiac Hasbro",
  age: 56
};

const greeting = `Hello, my name is ${person.name}!
I am ${person.age} years old.`;

console.log(greeting);
```

控制台将显示字符串 `Hello, my name is Zodiac Hasbro!` 和 `I am 56 years old.`。

这里发生了许多事情。 首先，这个例子使用反引号（\`），而不是引号（`'` 或者 `"`）将字符串括起来。 其次，注意代码和输出中的字符串都是多行的。 不需要在字符串中插入 `\n`。 上面使用的 `${variable}` 语法是一个占位符。 这样一来，你将不再需要使用 `+` 运算符来连接字符串。 当需要在字符串里增加变量的时候，你只需要在变量的外面括上 `${` 和 `}`，并将其放在模板字符串里就可以了。 同样，你可以在字符串中包含其他表达式，例如 `${a + b}`。 这个新的方式使你可以更灵活地创建复杂的字符串。

### for...in 遍历对象

有时候你需要遍历一个对象中的所有键。 你可以使用 for...in 循环来做这件事。 for...in 循环是这样的：

**也可以用于遍历数组，不过取出来的值是数组下标**

```javascript
const refrigerator = {
  'milk': 1,
  'eggs': 12,
};

for (const food in refrigerator) {
  console.log(food, refrigerator[food]);
}
```

以上代码记录 `milk 1` 和 `eggs 12`，每个键值对单独为一行。

我们在循环头中定义了变量 `food` ，这个变量被设置为每次迭代上对象的每个键值，将每个食物的名称打印到控制台。

**注意：**对象中的**键是无序的**，这与数组不同。 因此，一个对象中**某个属性的位置，或者说它出现的相对顺序，在引用或访问该属性时是不确定的。**

### iterable

遍历`Array`可以采用下标循环，遍历`Map`和`Set`就无法使用下标。为了统一集合类型，ES6标准引入了新的`iterable`类型，`Array`、`Map`和`Set`都属于`iterable`类型。

具有`iterable`类型的集合可以通过新的`for ... of`循环来遍历。遍历`Array`可以采用下标循环，遍历`Map`和`Set`就无法使用下标。为了统一集合类型，ES6标准引入了新的`iterable`类型，**`Array`、`Map`和`Set`都属于`iterable`类型**。

**具有`iterable`类型的集合可以通过新的`for ... of`循环来遍历。**

用`for ... of`循环遍历集合（数组、map、Set），用法如下：

```js
var a = ['A', 'B', 'C'];
var s = new Set(['A', 'B', 'C']);
var m = new Map([[1, 'x'], [2, 'y'], [3, 'z']]);
for (var x of a) { // 遍历Array
    console.log(x);
}
for (var x of s) { // 遍历Set
    console.log(x);
}
for (var x of m) { // 遍历Map
    console.log(x[0] + '=' + x[1]);
}
```

#### for of 和 for in

`for ... in`循环由于历史遗留问题，它遍历的实际上是对象的属性名称。一个`Array`数组实际上也是一个对象，它的每个元素的索引被视为一个属性。

当我们手动给`Array`对象添加了额外的属性后，`for ... in`循环将带来意想不到的意外效果：

```js
var a = ['A', 'B', 'C'];
a.name = 'Hello';
for (var x in a) {
    console.log(x); // '0', '1', '2', 'name'
}
```

`for ... in`循环将把`name`包括在内，但`Array`的`length`属性却不包括在内。

`for ... of`循环则完全修复了这些问题，它只循环集合本身的元素：

```js
var a = ['A', 'B', 'C'];
a.name = 'Hello';
for (var x of a) {
    console.log(x); // 'A', 'B', 'C'
}
```

这就是为什么要引入新的`for ... of`循环。

然而，更好的方式是直接使用`iterable`内置的`forEach`方法，它接收一个函数，每次迭代就自动回调该函数。以`Array`为例：

```js
'use strict';
var a = ['A', 'B', 'C'];
a.forEach(function (element, index, array) {
    // element: 指向当前元素的值
    // index: 指向当前索引
    // array: 指向Array对象本身
    console.log(element + ', index = ' + index);
});
```

**`Set`与`Array`类似，但`Set`没有索引，因此回调函数的前两个参数都是元素本身**：

```js
var s = new Set(['A', 'B', 'C']);
s.forEach(function (element, sameElement, set) {
    console.log(element);
});
```

**`Map`的回调函数参数依次为`value`、`key`和`map`本身：**

```js
var m = new Map([[1, 'x'], [2, 'y'], [3, 'z']]);
m.forEach(function (value, key, map) {
    console.log(value);
});
```

如果对某些参数不感兴趣，由于JavaScript的函数调用不要求参数必须一致，因此可以忽略它们。例如，只需要获得`Array`的`element`：

```js
var a = ['A', 'B', 'C'];
a.forEach(function (element) {
    console.log(element);
});
```

### instanceof

凡是通过构造函数创建出的新对象，这个对象都叫做这个构造函数的实例。 JavaScript 提供了一种很简便的方法来验证这个事实，那就是通过 `instanceof` 操作符。 `instanceof` 允许你将对象与构造函数之间进行比较，根据对象是否由这个构造函数创建的返回 `true` 或者 `false`。 以下是一个示例：

```js
let Bird = function(name, color) {
  this.name = name;
  this.color = color;
  this.numLegs = 2;
}

let crow = new Bird("Alexis", "black");

crow instanceof Bird;
```

`instanceof` 方法会返回 `true`。

如果一个对象不是使用构造函数创建的，那么 `instanceof` 将会验证这个对象不是构造函数的实例：

```js
let canary = {
  name: "Mildred",
  color: "Yellow",
  numLegs: 2
};

canary instanceof Bird;
```

`instanceof` 方法会返回 `false`。

## 对象

对象和 `arrays` 类似，区别在于数组使用索引来访问和修改数据，而对象中的数据是通过 `properties` 访问的。

对象非常适合用来存储结构化数据，可以表示真实世界中的物体，比如一只猫。

对象（object）本质上是键值对（key-value pair）的集合。 或者说，一系列被映射到唯一标识符的数据就是对象；习惯上，唯一标识符叫做属性（property）或者键（key）；数据叫做值（value）

这里是一个猫对象的样本：

```js
const cat = {
  "name": "Whiskers",
  "legs": 4,
  "tails": 1,
  "enemies": ["Water", "Dogs"]
};
```

在此示例中，所有属性都存储为字符串，例如 `name`、`legs` 和 `tails`。 然而，你也可以使用数字作为属性。 你甚至可以**省略单字字符串(即属性字符串无空格)属性中的引号**，如下所示：

```js
const anotherObject = {
  make: "Ford",
  5: "five",
  "model": "focus"
};
```

然而，如果你的对象有**非字符串属性**的话，JavaScript 会**自动将它们转为字符串**。

### 访问对象

访问对象属性有两种方式：点号表示法（`.`）和方括号表示法（`[]`）。

如果我们已经提前知道要访问的属性名，使用点号表示法是最方便的。

这里是一个用点符号（`.`）读取对象属性的示例：

```js
const myObj = {
  prop1: "val1",
  prop2: "val2"
};

const prop1val = myObj.prop1;
const prop2val = myObj.prop2;
```

`prop1val` 的值将为字符串 `val1`，并且`prop2val` 的值将为字符串 `val2`。

访问对象属性的第二种方式是方括号表示法（`[]`）。 **如果你想访问的属性名中包含空格，就必须使用方括号表示法来获取它的属性值**。当然，如果属性名不包含空格，也可以使用方括号表示法。

这是一个使用方括号表示法读取对象属性的例子：

```js
const myObj = {
  "Space Name": "Kirk",
  "More Space": "Spock",
  "NoSpace": "USS Enterprise"
};

myObj["Space Name"];
myObj['More Space'];
myObj["NoSpace"];
```

`myObj["Space Name"]` 将会是字符串 `Kirk`，`myObj['More Space']` 将会是字符串 `Spock`，并且`myObj["NoSpace"]` 将会是字符串 `USS Enterprise`。

**注意，如果属性名中包含空格，就必须使用引号（单引号或双引号）将它们包裹起来。**

### 根据变量访问属性 方括号专属

**对对象上使用方括号表示法，还可以访问对象上作为变量值存储的属性**。 当你需要遍历对象的所有属性，或者根据一个变量的值查找对应的属性值时，这种写法尤其适用。

以下是一个使用变量来访问属性的例子：

```js
const dogs = {
  Fido: "Mutt",
  Hunter: "Doberman",
  Snoopie: "Beagle"
};

const myDog = "Hunter";
const myBreed = dogs[myDog];
console.log(myBreed);
```

字符串 `Doberman` 将会出现在控制台中。

请注意，我们在使用变量名访问属性时，*不要*使用引号引起来，因为我们使用的是 ***值***，而不是 ***属性名***。

### 对象添加属性/方法

你也可以像更改属性一样给 JavaScript 对象添加属性。**方法的添加也是一样的**

这里展示了如何给 `ourDog` 添加一个属性 `bark`：

```js
const ourDog = {
  "name": "Camper",
  "legs": 4,
  "tails": 1,
  "friends": ["everything!"]
};

ourDog.bark = "bow-wow";//或者ourDog["bark"] = "bow-wow";
```

现在，当我们执行 `ourDog.bark` 时，就能得到他的叫声，`bow-wow`。

### 删除对象属性

我们同样可以删除对象的属性，例如：

```js
delete ourDog.bark;
```

例如：

```js
const ourDog = {
  "name": "Camper",
  "legs": 4,
  "tails": 1,
  "friends": ["everything!"],
  "bark": "bow-wow"
};

delete ourDog.bark;//也可以delete ourDog["bark"];
```

### 检查属性是否存在

要检查某个对象是否具有一个属性，你可以使用 `.hasOwnProperty()` 方法。 根据对象是否具有该属性，`someObject.hasOwnProperty(someProperty)` 返回 `true` 或 `false`。

```js
function checkForProperty(object, property) {
  return object.hasOwnProperty(property);
}

checkForProperty({ top: 'hat', bottom: 'pants' }, 'top'); // true
checkForProperty({ top: 'hat', bottom: 'pants' }, 'middle'); // false
```

第一个 `checkForProperty` 函数返回 `true`，第二个返回 `false`。

如果我们想知道一个对象中是否包含某个属性呢？ JavaScript 为我们提供了两种不同的方式来实现这个功能： 一个是通过 `hasOwnProperty()` 方法，另一个是使用 `in` 关键字。 假如我们有一个 `users` 对象，为检查它是否含有 `Alan` 属性，可以这样写：

```js
users.hasOwnProperty('Alan');
'Alan' in users;
```

这两者结果都应该为 `true`。

### 构造函数

构造函数是创建对象的函数。 函数给这个新对象定义属性和行为。 可将它们视为创建的新对象的蓝图。

以下就是一个构造函数的示例：

```js
function Bird() {
  this.name = "Albert";
  this.color = "blue";
  this.numLegs = 2;
}
```

这个构造函数定义了一个 `Bird` 对象，其属性 `name`、`color` 和 `numLegs` 的值分别被设置为 Albert、blue 和 2。 构造函数遵循一些惯例规则：

- **构造函数函数名的首字母大写**，这是为了方便我们区分构造函数（ `constructors`）和其他非构造函数。
- 构造函数使用 `this` 关键字来给它将创建的这个对象设置新的属性。 在构造函数里面，`this` 指向的就是它新创建的这个对象。
- 构造函数定义了属性和行为就可创建对象，而不是像其他函数一样需要设置返回值。

#### 构造函数属性 constructor

在上一个挑战中创建的实例对象 `duck` 和 `beagle` 都有一个特殊的 `constructor` 属性：

```js
let duck = new Bird();
let beagle = new Dog();

console.log(duck.constructor === Bird); 
console.log(beagle.constructor === Dog);
```

这两次 `console.log` 调用都将在控制台中显示 `true`。

需要注意到的是这个 `constructor` 属性**是对创建这个实例的构造函数的一个引用**。 `constructor` 属性的一个好处是可以通过检查这个属性来找出它是一个什么对象。 下面是一个例子，来看看是怎么使用的：

```js
function joinBirdFraternity(candidate) {
  if (candidate.constructor === Bird) {
    return true;
  } else {
    return false;
  }
}
```

**注意：由于 `constructor` 属性可以被重写（在下面两节挑战中将会遇到），所以最好使用`instanceof` 方法来检查对象的类型。**

### 构造函数创建对象

在上一个挑战中，我们用所学到的知识创建了一个 `Bird` 构造函数：

```js
function Bird() {
  this.name = "Albert";
  this.color  = "blue";
  this.numLegs = 2;
}

let blueBird = new Bird();
```

**注意：** 构造函数内的 `this` 总是指被创建的对象。

注意**：通过构造函数创建对象的时候要使用 `new` 操作符。 因为只有这样，JavaScript 才知道要给 `Bird` 这个构造函数创建一个新的实例：`blueBird`。 如果不使用 `new` 操作符来新建对象，那么构造函数里面的 `this` 就无法指向新创建的这个对象实例，从而产生不可预见的错误。** 现在 `blueBird` 这个实例就继承了`Bird` 构造函数的所有属性，如下：

```js
blueBird.name;
blueBird.color;
blueBird.numLegs;
```

由构造函数创建的实例也和其他对象一样，它的属性可以被访问和修改：

```js
blueBird.name = 'Elvira';
blueBird.name;
```

### 构造函数传参

上一个挑战中 `Bird` 和 `Dog` 构造函数运行得不错。 但是，注意到没有：所有通过`Bird` 构造函数创建出来的实例 `Birds` 都自动的取名为 Albert，颜色都是蓝色，还都有两条腿。 如果你想要新创建出来的小鸟们拥有不同的名字和颜色要怎么办呢？ 当然，手动的去修改每一个小鸟实例自己的属性也是可以实现的，只是会增加很多无谓的工作量：

```js
let swan = new Bird();
swan.name = "Carlos";
swan.color = "white";
```

假如你写了一个程序来追踪一个鸟舍里面的几百只甚至几千只不同的小鸟。 你将会花费很多时间去创建所有的小鸟实例并给它们的属性一一修改为不同的值。 为了减轻创建不同 `Bird` 对象的工作量，**你可以给你的 Bird 设置为可以接收参数的构造函数：**

```js
function Bird(name, color) {
  this.name = name;
  this.color = color;
  this.numLegs = 2;
}
```

然后将值通过参数的方式传递给 `Bird` 构造函数来定义每一个唯一的小鸟实例： `let cardinal = new Bird("Bruce", "red");` 这给 `Bird` 的 `name` 和 `color` 属性分别赋值为 `Bruce` 和 `red` 色。 但 `numLegs` 属性仍然设置为 2。 `cardinal` 有以下这些属性：

```js
cardinal.name
cardinal.color
cardinal.numLegs
```

这样一来构造函数就变得很灵活了。 现在可以在创建每个`Bird`实例时直接定义属性，这是 JavaScript 构造函数非常实用的用法之一。 它们根据共同或相似的属性和行为将对象归纳为一组，并能够自动的创建各自实例。

### prototype 原型

#### 原型由来

就像人们从父母那里继承基因一样，对象也可直接从创建它的构造函数那里继承其 `prototype`。 请看下面的例子：`Bird` 构造函数创建了一个 `duck` 对象：

```js
function Bird(name) {
  this.name = name;
}

let duck = new Bird("Donald");
```

`duck` 从 `Bird` 构造函数那里继承了它的 `prototype`。 你可以使用 `isPrototypeOf` 方法来验证他们之间的关系：

```js
Bird.prototype.isPrototypeOf(duck);
```

这将返回 `true`。

#### 添加属性

所有 `Bird` 实例可能会有相同的 `numLegs` 值，所以在每一个 `Bird` 的实例中本质上都有一个重复的变量 `numLegs`。

当只有两个实例时可能并不是什么问题，但想象一下如果有数百万个实例。 这将会产生许许多多重复的变量。

更好的方法是使用 `Bird` 的 `prototype`。 **`prototype` 是一个可以在所有 `Bird` 实例之间共享的对象。 以下是一个在 `Bird prototype` 中添加 `numLegs` 属性的示例**：

```js
Bird.prototype.numLegs = 2;
```

现在所有的 `Bird` 实例都拥有了共同的 `numLegs` 属性值。

```js
console.log(duck.numLegs);
console.log(canary.numLegs);
```

由于所有的实例都可以继承 `prototype` 上的属性，所以可以把 `prototype` 看作是创建对象的 "配方"。 请注意：`duck` 和 `canary` 的 `prototype` 属于 `Bird` 的构造函数，即 Bird 的原型 `Bird.prototype`。

**手动设置一个新对象的原型有一个重要的副作用。 它清除了 `constructor` 属性！** 此属性可以用来检查是哪个构造函数创建了实例，但由于该属性已被覆盖，它现在给出了错误的结果：

```js
duck.constructor === Bird;
duck.constructor === Object;
duck instanceof Bird;
```

按顺序，这些表达式会返回 `false`、`true` 和 `true`。

为了解决这个问题，凡是手动给新对象重新设置过原型对象的，都别忘记在原型对象中定义一个 `constructor` 属性：

```js
Bird.prototype = {
  constructor: Bird,
  numLegs: 2,
  eat: function() {
    console.log("nom nom nom");
  },
  describe: function() {
    console.log("My name is " + this.name); 
  }
};
```

也可以手动直接设置

```js
Bird.prototype.constructor = Bird;//方式一
duck.constructor=Bird;//方式二
```

#### 自身属性和prototype属性

**现在你已经了解了两种属性: 自身属性和 `prototype` 属性。 自身属性是直接在对象上定义的。 而原型属性在 `prototype` 上定义**。

```js
function Bird(name) {
  this.name = name;  //own property
}

Bird.prototype.numLegs = 2; // prototype property

let duck = new Bird("Donald");
```

**这个示例会告诉你如何将 `duck` 的自身属性和 `prototype` 属性分别添加到 `ownProps` 数组和 `prototypeProps` 数组里面：**

```js
let ownProps = [];
let prototypeProps = [];

for (let property in duck) {
  if(duck.hasOwnProperty(property)) {
    ownProps.push(property);
  } else {
    prototypeProps.push(property);
  }
}

console.log(ownProps);
console.log(prototypeProps);
```

`console.log(ownProps)` 将在控制台中显示 `["name"]` ，`console.log(prototypeProps)` 将显示 `["numLegs"]`。

#### 添加多个属性

到目前为止，你已经可以单独给 `prototype` 添加属性了：

```js
Bird.prototype.numLegs = 2;
```

需要添加多个属性的，这未免会显得拖沓。

```js
Bird.prototype.eat = function() {
  console.log("nom nom nom");
}

Bird.prototype.describe = function() {
  console.log("My name is " + this.name);
}
```

**一种更有效的方法就是给对象的 `prototype` 设置为一个已经包含了属性的新对象**。 这样一来，所有属性都可以一次性添加进来：

```js
Bird.prototype = {
  numLegs: 2, 
  eat: function() {
    console.log("nom nom nom");
  },
  describe: function() {
    console.log("My name is " + this.name);
  }
};
```

#### 原型链

JavaScript 中所有的对象（除了少数例外）都有自己的 `prototype`。 而且，对象的 `prototype` 本身也是一个对象。

```js
function Bird(name) {
  this.name = name;
}

typeof Bird.prototype;
```

**正因为 `prototype` 是一个对象，所以 `prototype` 对象也有它自己的 `prototype`！ 这样看来的话，`Bird.prototype` 的 `prototype` 就是 `Object.prototype`：**

```js
Object.prototype.isPrototypeOf(Bird.prototype);
```

这有什么作用呢？ 你可能还记得我们在上一个挑战中学到的 `hasOwnProperty` 方法：

```js
let duck = new Bird("Donald");
duck.hasOwnProperty("name");
```

`hasOwnProperty` 是定义在 `Object.prototype` 上的一个方法，尽管在 `Bird.prototype` 和 `duck`上并没有定义该方法，但是我们依然可以在这两个对象上访问到。 这就是 `prototype` 链的一个例子。 在这个`prototype` 链中，`Bird` 是 `duck` 的 `supertype`，而 `duck` 是 `subtype`。 `Object` 则是 `Bird` 和 `duck` 实例共同的 `supertype`。 `Object` 是 JavaScript 中所有对象的 `supertype`，也就是原型链的最顶层。 因此，所有对象都可以访问 `hasOwnProperty` 方法。

#### 继承

在上一个挑战中，我们创建了一个`Animal` 超类（`supertype`），用来定义所有动物共有的行为：

```js
function Animal() { }
Animal.prototype.eat = function() {
  console.log("nom nom nom");
};
```

在这一节以及下一节挑战中我们将学习如何在 `Bird` 和 `Dog` 中重用 `Animal` 中的方法，而无需重新定义它们。 这里我们会用到构造函数的继承特性。 这一节挑战中我们学习第一步：创建一个超类 `supertype`（或者叫父类）的实例。 你已经学会了一种创建 `Animal` 实例的方法，即使用 `new` 操作符：

```js
let animal = new Animal();
```

此语法用于继承时会存在一些缺点，这些缺点对于当前我们这个挑战来说太复杂了。 相反，我们学习另外一种没有这些缺点的方法来替代 new 操作：

```js
let animal = Object.create(Animal.prototype);
```

`Object.create(obj)` 创建了一个新对象，并指定了 `obj` 作为新对象的 `prototype`。 回忆一下，我们之前说过 `prototype` 就像是创建对象的“配方”。 如果我们把 `animal` 的 `prototype` 设置为与 `Animal` 构造函数的 `prototype` 一样，那么就相当于让 `animal` 这个实例具有与 `Animal` 的其他实例相同的“配方”了。

```js
animal.eat();
animal instanceof Animal;
```

`instanceof` 方法会返回 `true`.

在上一个挑战中，我们学习了从超类（或者叫父类） `Animal` 继承其行为的第一个步骤：创建一个 `Animal` 的新实例。

**这一节挑战我们将学习第二个步骤：给子类型（或者子类）设置 `prototype`。 这样一来，`Bird` 就是 `Animal` 的一个实例了。**

```js
Bird.prototype = Object.create(Animal.prototype);
```

请记住，`prototype` 类似于创建对象的“配方”。 从某种意义上来说，`Bird` 对象的配方包含了 `Animal` 的所有关键“成分”。

```js
let duck = new Bird("Donald");
duck.eat();
```

`duck` 继承了`Animal` 的所有属性，其中包括了 `eat` 方法。

**当一个对象从另一个对象那里继承了其 `prototype` 时，那它也继承了父类的 constructor 属性。**

请看下面的举例：

```js
function Bird() { }
Bird.prototype = Object.create(Animal.prototype);
let duck = new Bird();
duck.constructor
```

但是 `duck` 和其他所有 `Bird` 的实例都应该表明它们是由 `Bird` 创建的，而不是由 `Animal` 创建的。 为此，你可以手动将 `Bird` 的构造函数属性设置为 `Bird` 对象：

```js
Bird.prototype.constructor = Bird;
duck.constructor
```

##### 继承添加方法

从超类构造函数继承其 `prototype` 对象的构造函数，除了继承的方法外，还可以拥有自己的方法。

请看举例：`Bird` 是一个构造函数，它继承了 `Animal` 的 `prototype`：

```js
function Animal() { }
Animal.prototype.eat = function() {
  console.log("nom nom nom");
};
function Bird() { }
Bird.prototype = Object.create(Animal.prototype);
Bird.prototype.constructor = Bird;
```

除了从 `Animal` 构造函数继承的行为之外，还需要给 `Bird` 对象添加它独有的行为。 这里，我们给 `Bird` 对象添加一个 `fly()` 函数。 函数会以一种与其他构造函数相同的方式添加到 `Bird's` 的 `prototype` 中：

```js
Bird.prototype.fly = function() {
  console.log("I'm flying!");
};
```

现在 `Bird` 的实例中就有了 `eat()` 和 `fly()` 这两个方法：

```js
let duck = new Bird();
duck.eat();
duck.fly();
```

`duck.eat()` 将在控制台中显示字符串 `nom nom nom`， `duck.fly()` 将显示字符串 `I'm flying!`。

##### 重写继承方法

在上一个挑战中，我们学习了一个对象可以通过引用另一个对象的 `prototype` 来继承其属性和行为（或方法）：

```js
ChildObject.prototype = Object.create(ParentObject.prototype);
```

然后，`ChildObject` 将自己的方法链接到它的 `prototype`中：

```js
ChildObject.prototype.methodName = function() {...};
```

我们还可以重写继承的方法。 以同样的方式 - 通过使用一个与需要重写的方法相同的方法名，向`ChildObject.prototype` 中添加方法。 请看下面的举例：`Bird` 重写了从 `Animal` 继承来的 `eat()` 方法：

```js
function Animal() { }
Animal.prototype.eat = function() {
  return "nom nom nom";
};
function Bird() { }

Bird.prototype = Object.create(Animal.prototype);

Bird.prototype.eat = function() {
  return "peck peck peck";
};
```

如果你有一个实例：`let duck = new Bird();`，然后你调用了 `duck.eat()`，以下就是 JavaScript 在 `duck` 的 `prototype` 链上寻找方法的过程：

1. `duck` => `eat()` 是定义在这里吗？ 不是。
2. `Bird` => `eat()` 是定义在这里吗？ => 是的。 执行它并停止往上搜索。
3. `Animal` => 这里也定义了 `eat()` 方法，但是 JavaScript 在到达这层原型链之前已停止了搜索。
4. Object => JavaScript 在到达这层原型链之前也已经停止了搜索。

#### 不相关对象之间添加共同行为

正如你所见，行为是可以通过继承来共享的。 然而，在有些情况下，继承不是最好的解决方案。 继承不适用于不相关的对象，比如 `Bird` 和 `Airplane`。 虽然它们都可以飞行，但是 `Bird` 并不是一种 `Airplane`，反之亦然。

对于不相关的对象，更好的方法是使用 mixins。 mixin 允许其他对象使用函数集合。

```js
let flyMixin = function(obj) {
  obj.fly = function() {
    console.log("Flying, wooosh!");
  }
};
```

`flyMixin` 能接受任何对象，并为其提供 `fly` 方法。

```js
let bird = {
  name: "Donald",
  numLegs: 2
};

let plane = {
  model: "777",
  numPassengers: 524
};

flyMixin(bird);
flyMixin(plane);
```

这里的 `flyMixin` 接收了`bird` 和 `plane` 对象，然后将 `fly` 方法分配给了每一个对象。 现在 `bird` 和 `plane` 都可以飞行了：

```js
bird.fly();
plane.fly();
```

控制台将显示字符串 `Flying, wooosh!` 两次，每次 `.fly()` 调用都会显示。

注意观察 mixin 是如何允许相同的 `fly` 方法被不相关的对象 `bird` 和 `plane` 重用的。

#### 立即调用函数表达（IIFE）

JavaScript 中的一个常见模式就是，函数在声明后立刻执行：

```js
(function () {
  console.log("Chirp, chirp!");
})();
```

这是一个匿名函数表达式，立即执行并输出 `Chirp, chirp!`。

请注意，函数没有名称，也不存储在变量中。 函数表达式末尾的两个括号（）会让它被立即执行或调用。 这种模式被叫做立即调用函数表达式（immediately invoked function expression) 或者IIFE。

#### IIFE 创建一个模块

> https://blog.csdn.net/HelloWord176/article/details/119457977 立即执行函数详解

一个立即调用函数表达式（IIFE）通常用于将相关功能分组到单个对象或者是 module 中。 例如，先前的挑战中定义了两个 mixins：

```js
function glideMixin(obj) {
  obj.glide = function() {
    console.log("Gliding on the water");
  };
}
function flyMixin(obj) {
  obj.fly = function() {
    console.log("Flying, wooosh!");
  };
}
```

我们可以将这些 mixins 分成以下模块：

```js
let motionModule = (function () {
  return {
    glideMixin: function(obj) {
      obj.glide = function() {
        console.log("Gliding on the water");
      };
    },
    flyMixin: function(obj) {
      obj.fly = function() {
        console.log("Flying, wooosh!");
      };
    }
  }
})();
```

注意：一个立即调用函数表达式（IIFE）返回了一个 `motionModule` 对象。 返回的这个对象包含了作为对象属性的所有 mixin 行为。 module 模式的优点是，所有的运动相关的行为都可以打包成一个对象，然后由代码的其他部分使用。 下面是一个使用它的例子：

```js
motionModule.glideMixin(duck);
duck.glide();
```

#### 闭包

在上一次挑战中，`bird` 有一个公共属性 `name`。 公共属性的定义就是：它可以在 `bird` 的定义范围之外被访问和更改。

```js
bird.name = "Duffy";
```

因此，代码的任何地方都可以轻松地将 `bird` 的 name 属性更改为任意值。 想想密码和银行账户之类的东西，如果代码库的任何部分都可以轻易改变它们， 那将会引起很多问题。

使属性私有化最简单的方法就是在构造函数中创建变量。 可以将该变量范围限定在构造函数中，而不是全局可用。 这样，属性只能由构造函数中的方法访问和更改。

```js
function Bird() {
  let hatchedEgg = 10;

  this.getHatchedEggCount = function() { 
    return hatchedEgg;
  };
}
let ducky = new Bird();
ducky.getHatchedEggCount();
```

**这里的 `getHatchedEggCount` 是一种特权方法，因为它可以访问私有属性 `hatchedEgg`。 这是因为 `hatchedEgg` 是在与 `getHatchedEggCount` 相同的上下文中声明的。 在 JavaScript 中，函数总是可以访问创建它的上下文。 这就叫做 `closure`（闭包）。**

### class Es6

**为解决之前原型的复杂设置**

类中由四种方法构成：

- 构造方法：定义属性，解决入参问题
- 实例方法：将被挂载到原型对象上
- 访问器方法：拦截某个属性的get和set
- 静态方法：直接挂载到类对象上

```js
class Person {
	// 1 构造方法
	// 对应ES5中来调用new构造函数后，执行的5步操作
	constructor(name, age) {
		this.name = name;
		this.age = age;
		this._address = "上海市";
	}

	// 2 实例方法
	eat() {
		console.log(this.name + ' is eating');
	}

	// 3 访问器方法
	get address() {
		console.log("拦截访问操作");
		return this._address;
	}
	set address(value) {
		console.log("拦截赋值操作");
		this._address = value;
	}

	// 4 静态方法
	static randomPerson() {
		const names = ["sd", "fr", "re", "weee"];
    	const nameIdx = Math.floor(Math.random() * names.length);
   		const name = names[nameIdx];
   		const age = Math.floor(Math.random() * 100);
    	return new Person(name, age);
	}
}
```

创建实例对象

```js
// 创建对象实例
const p = new Person('zs', 18);
console.log(p); // {name: 'zs', age: 18, _address: '上海市'};

// 调用实例方法
p.eat() // 'zs is eating'

// 测试访问器方法
// 注意address属性挂载到是Person原型对象上的
console.log(p.address) // '拦截访问操作'
p.address = '北京市'; // '拦截赋值操作'

// 测试静态方法
// 注意：静态方法的调用方式
const p2 = Person.randomPerson()
```

#### extend

类继承涉及到关键字`extends`，下面演示创建子类Student继承父类Person，涉及到两个关键字extends和super

```js
class Student extends Person {
	// 构造方法
	constructor(name, age, id) {
		// JS引擎在解析子类的时候要求:如果使用了继承extends，那么子类的构造方法中，使用this之前必须调用父类的构造方法
		super(name, age); 
		this.id = id;
	}

	// 实例方法
	// 和父类方法同名，属于方法重写
	eat() {
		// 借助super关键字来调用父类方法
		super.eat();
		console.log('Student' + this.name + ' is eating');
	}
	// 访问器方法 同样可以定义，不再赘述
	// 静态方法
 	static staticMethod() {
    	super.randomPerson(); // 只有在子类静态方法中才可以调用父类的静态方法，子类其他方法不可
    	console.log("student static method");
  }
}
```

## 数据结构 

### 对象嵌套

有时你可能希望将数据存储在一个灵活的数据结构（Data Structure）中。 JavaScript 对象是一种灵活的数据结构。 它可以储存字符串（strings）、数字（numbers）、布尔值（booleans）、数组（arrays）、函数（functions）和对象（objects）以及这些值的任意组合。

这是一个复杂数据结构的示例：

```js
const ourMusic = [
  {
    "artist": "Daft Punk",
    "title": "Homework",
    "release_year": 1997,
    "formats": [ 
      "CD", 
      "Cassette", 
      "LP"
    ],
    "gold": true
  }
];
```

这是一个包含一个对象的数组。 该对象有关于专辑的各种元数据（metadata）。 它也有一个嵌套的 `formats` 数组。 可以将专辑添加到顶级数组来增加更多的专辑记录。 对象将数据以一种键 - 值对的形式保存。 在上面的示例中，`"artist": "Daft Punk"` 有一个键为 `artist` 值为 `Daft Punk` 的属性。

**提示：**数组中有多个 JSON 对象的时候，对象与对象之间要用逗号隔开

### 访问被嵌套的对象

我们可以通过连续使用点号表示法和方括号表示法来访问对象的嵌套属性。

这是一个嵌套对象：

```js
const ourStorage = {
  "desk": {
    "drawer": "stapler"
  },
  "cabinet": {
    "top drawer": { 
      "folder1": "a file",
      "folder2": "secrets"
    },
    "bottom drawer": "soda"
  }
};

ourStorage.cabinet["top drawer"].folder2;
ourStorage.desk.drawer;
```

`ourStorage.cabinet["top drawer"].folder2` 将会是字符串 `secrets`，并且 `ourStorage.desk.drawer` 将会是字符串 `stapler`。

### 嵌套数组

在之前的挑战中，我们学习了在对象中嵌套对象和数组。 与访问嵌套对象类似，数组的方括号可以用来对嵌套数组进行链式访问。

下面是访问嵌套数组的例子：

```js
const ourPets = [
  {
    animalType: "cat",
    names: [
      "Meowzer",
      "Fluffy",
      "Kit-Cat"
    ]
  },
  {
    animalType: "dog",
    names: [
      "Spot",
      "Bowser",
      "Frankie"
    ]
  }
];

ourPets[0].names[1];//也可以ourPets[0]["names"][1];
ourPets[1].names[0];
```

`ourPets[0].names[1]` 应该是字符串 `Fluffy`， 并且 `ourPets[1].names[0]` 应该是字符串 `Spot`。

## 属性

### Array prototype  引用全局 Array() 对象

- `prototype` 是可用于所有 JavaScript 对象的全局构造函数。
- `prototype` 引用全局 Array() 对象。
- `prototype` 构造函数允许您向数组添加新的属性和方法。
- 当构造新属性时，所有数组都将获得此属性及其值。
- 当构造新方法时，所有数组都将获得此方法。

**例如**：创建一个新的数组方法，将数组值转换为大写

```js
Array.prototype.myUcase = function() {
  for (i = 0; i < this.length; i++) {
    this[i] = this[i].toUpperCase();
  }
};
```

创建一个数组，然后调用 myUcase 方法：

```js
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.myUcase();
```

## 常用对象

### Date

```js
let now=new Date();
now.getFullYear();//年
now.getMonth();//月
now.getDate();//日
now.getDay();//星期几
now.getHours();//时
now.getMinutes();//分
now.getSeconds();//秒

now.getTime();//时间戳 全世界统一 为距离 1970 1.1 0:00:00毫秒数
```

转换：

```js
let now= new Date(1472048779952);//时间戳转换
console.log(now) //2016-08-24T14:26:19.952Z
console.log(now.toString());//Wed Aug 24 2016 22:26:19 GMT+0800 (中国标准时间)
```

### Json

将对象序列化成json字符串： JSON.stringify(json对象)；

反序列化：将json字符串反序列化为对象:  JSON.parse(str)

#### parse

```js
JSON.parse(str, reviver);
```

- `str`：要解析的 JSON字符串
- `reviver`：可选的函数 `function(key,value)`，该函数的第一个参数和第二个参数分别代表键值对的键和值，并可以对值进行转换（函数返回值当做处理后的value）

```js
let json = '{"name": ["js", "webpack"], "age": 22, "gridFriend": "ljj"}';
console.log(JSON.parse(json)); 
// {name: Array(2), age: 22, gridFriend: 'ljj'}

// 第二个参数是一个函数，key和value代表每个key/value对
let result = JSON.parse(json, (key, value) => {
    if (key == "age") {
        return `年龄：${value}`;
    }
    return value;
});
console.log(result);
//{name: Array(2), age: '年龄：22', gridFriend: 'ljj'}

```

#### stringify

```js
JSON.stringify(value, replacer, space)
```

value：将要序列化成 一个 JSON 字符串的值

replacer：

- 如果该参数是一个函数，则在序列化过程中，被序列化的值的每个属性都会经过该函数的转换和处理
- 如果该参数是一个**数组**，则**只有包含在这个数组中的属性名才会被序列化**到最终的 JSON 字符串中
- 如果该参数为 null 或者未提供，则对象所有的属性都会被序列化

space：指定缩进用的空白字符串，用于美化输出

- 如果参数是个数字，它代表有多少的空格；上限为10。该值若小于1，则意味着没有空格
- 如果该参数为字符串（当字符串长度超过10个字母，取其前10个字母），该字符串将被作为空格
- 如果该参数没有提供（或者为 null），将没有空格

```js
let obj = {
    name: "jsx",
    age: 22,
    lesson: ["html", "css", "js"],
};
let json = JSON.stringify(obj);
console.log(json);
// {"name":"jsx","age":22,"lesson":["html","css","js"]}

// 第二个参数replacer 为函数时，被序列化的值得属性都会经过该函数转换处理
function replacer(key, value) {
    if (typeof value === "string") {
        return undefined;
    }
    return value;
}
let result = JSON.stringify(obj, replacer);
console.log(result);
// {"age":22,"lesson":[null,null,null]}

// 当replacer参数为数组，数组的值代表将被序列化成 JSON 字符串的属性名
let result1 = JSON.stringify(obj, ["name", "lesson"]);
// 只保留 “name” 和 “lesson” 属性值
console.log(result1);
// {"name":"jsx","lesson":["html","css","js"]}

// 第三个参数spcae,用来控制结果字符串里面的间距
let result2 = JSON.stringify(obj, null, 4);
console.log(result2);
/*{
      "name": "jsx",
      "age": 22,
      "lesson": [
           "html",
           "css",
           "js"
        ]
}*/
```

## BOM对象

Browser Object Model：浏览器对象模型

### [windows](https://www.runoob.com/jsref/obj-window.html)

> https://blog.csdn.net/xiaoyangzhu/article/details/121664579

![](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202310251002324.png)

```js
window.alert(xxx) //弹出提示框

window.innerHeight //内部窗口高度

window.innerWidth //内部窗口宽度

window.outerHeight //外部窗口高度

window.outerWidth //外部窗口宽度
```

上面的图片显示了所有windows对象的方法,代码显示了常用的方法

### [navigator](https://www.runoob.com/jsref/obj-navigator.html) 不建议使用

Navigator 对象包含有关浏览器的信息。

**注意：** 没有应用于 navigator 对象的公开标准，不过所有浏览器都支持该对象。

#### Navigator 对象属性

| 属性                                                         | 说明                                              |
| :----------------------------------------------------------- | :------------------------------------------------ |
| [appCodeName](https://www.runoob.com/jsref/prop-nav-appcodename.html) | 返回浏览器的代码名                                |
| [appName](https://www.runoob.com/jsref/prop-nav-appname.html) | 返回浏览器的名称                                  |
| [appVersion](https://www.runoob.com/jsref/prop-nav-appversion.html) | 返回浏览器的平台和版本信息                        |
| [cookieEnabled](https://www.runoob.com/jsref/prop-nav-cookieenabled.html) | 返回指明浏览器中是否启用 cookie 的布尔值          |
| [platform](https://www.runoob.com/jsref/prop-nav-platform.html) | 返回运行浏览器的操作系统平台                      |
| [userAgent](https://www.runoob.com/jsref/prop-nav-useragent.html) | 返回由客户机发送服务器的user-agent 头部的值       |
| [geolocation](https://www.runoob.com/jsref/prop-nav-geolocation.html) | 返回浏览器的地理位置信息                          |
| [language](https://www.runoob.com/jsref/prop-nav-language.html) | 返回浏览器使用的语言                              |
| [onLine](https://www.runoob.com/jsref/prop-nav-online.html)  | 返回浏览器是否在线，在线返回 ture，否则返回 false |
| [product](https://www.runoob.com/jsref/prop-nav-product.html) | 返回浏览器使用的引擎（产品）                      |

```js
navigator.appName
//'Netscape'
navigator.appVersion
//'5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/118.0.0.0 Safari/537.36'
navigator.userAgent
//'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/118.0.0.0 Safari/537.36'
navigator.platform
//'Win32'
```

因为会被人为修改，所以不建议使用

#### Navigator 对象方法

| 方法                                                         | 描述                                      |
| :----------------------------------------------------------- | :---------------------------------------- |
| [javaEnabled()](https://www.runoob.com/jsref/met-nav-javaenabled.html) | 指定是否在浏览器中启用Java                |
| [taintEnabled()](https://www.runoob.com/jsref/met-nav-taintenabled.html) | 规定浏览器是否启用数据污点(data tainting) |

### [screen](https://www.runoob.com/jsref/obj-screen.html)

Screen 对象包含有关客户端显示屏幕的信息。

#### Screen 对象属性

| 属性                                                         | 说明                                     |
| :----------------------------------------------------------- | :--------------------------------------- |
| [availHeight](https://www.runoob.com/jsref/prop-screen-availheight.html) | 返回屏幕的高度（不包括Windows任务栏）    |
| [availWidth](https://www.runoob.com/jsref/prop-screen-availwidth.html) | 返回屏幕的宽度（不包括Windows任务栏）    |
| [colorDepth](https://www.runoob.com/jsref/prop-screen-colordepth.html) | 返回目标设备或缓冲器上的调色板的比特深度 |
| [height](https://www.runoob.com/jsref/prop-screen-height.html) | 返回屏幕的总高度                         |
| [pixelDepth](https://www.runoob.com/jsref/prop-screen-pixeldepth.html) | 返回屏幕的颜色分辨率（每象素的位数）     |
| [width](https://www.runoob.com/jsref/prop-screen-width.html) | 返回屏幕的总宽度                         |

```js
screen.width
//2048
screen.height
//1152
```

### [location](https://www.runoob.com/jsref/obj-location.html)

Location 对象包含有关当前 URL 的信息。

Location 对象是 window 对象的一部分，可通过 **window.location.****xxx** 格式的相关属性对其进行访问

#### Location 对象属性

| 属性                                                         | 描述                          |
| :----------------------------------------------------------- | :---------------------------- |
| [hash](https://www.runoob.com/jsref/prop-loc-hash.html)      | 返回一个URL的锚部分           |
| [host](https://www.runoob.com/jsref/prop-loc-host.html)      | 返回一个URL的主机名和端口     |
| [hostname](https://www.runoob.com/jsref/prop-loc-hostname.html) | 返回URL的主机名               |
| [href](https://www.runoob.com/jsref/prop-loc-href.html)      | 返回完整的URL                 |
| [pathname](https://www.runoob.com/jsref/prop-loc-pathname.html) | 返回的URL路径名。             |
| [port](https://www.runoob.com/jsref/prop-loc-port.html)      | 返回一个URL服务器使用的端口号 |
| [protocol](https://www.runoob.com/jsref/prop-loc-protocol.html) | 返回一个URL协议               |
| [search](https://www.runoob.com/jsref/prop-loc-search.html)  | 返回一个URL的查询部分         |

```js
// 创建 a 标签并设置 href 属性
var url = document.createElement('a');
url.href = 'https://www.runoob.com/?s=python#test';
console.log(url.href);      // https://www.runoob.com/?s=python
console.log(url.protocol);  // https:
console.log(url.host);      // www.runoob.com
console.log(url.hostname);  // www.runoob.com
console.log(url.port);      // (输出为空 - https 默认端口为 443)
console.log(url.pathname);  // /
console.log(url.search);    // en-US/search
console.log(url.hash);      // #test
console.log(url.origin);    // https://www.runoob.com
```

#### Location 对象方法

| 方法                                                         | 说明                   |
| :----------------------------------------------------------- | :--------------------- |
| [assign()](https://www.runoob.com/jsref/met-loc-assign.html) | 载入一个新的文档       |
| [reload()](https://www.runoob.com/jsref/met-loc-reload.html) | 重新载入当前文档       |
| [replace()](https://www.runoob.com/jsref/met-loc-replace.html) | 用新的文档替换当前文档 |

```js
location.reload() //刷新页面

location.assign('https://www.runoob.com/jsref/obj-location.html') //重新加载新页面 ，可以点击后退返回上一个页面
window.location.replace(url) //这个方法是替换当前窗口页面，前后两个页面共用一个窗口，所以是没有后退返回上一页的
```

### [history](https://www.runoob.com/jsref/obj-history.html)

History 对象包含用户（在浏览器窗口中）访问过的 URL。

#### History 对象属性

| 属性                                                        | 说明                   |
| :---------------------------------------------------------- | :--------------------- |
| [length](https://www.runoob.com/jsref/prop-his-length.html) | 返回历史列表中的网址数 |

#### History 对象方法

| 方法                                                         | 说明                                              |
| :----------------------------------------------------------- | :------------------------------------------------ |
| [back()](https://www.runoob.com/jsref/met-his-back.html)     | 加载 history 列表中的前一个 URL                   |
| [forward()](https://www.runoob.com/jsref/met-his-forward.html) | 加载 history 列表中的下一个 URL                   |
| [go()](https://www.runoob.com/jsref/met-his-go.html)         | 加载 history 列表中的某个具体页面History 对象属性 |

## [DOM](https://www.runoob.com/jsref/dom-obj-document.html)

HTML文档中可以使用以下属性和方法:

| 属性 / 方法                                                  | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [document.activeElement](https://www.runoob.com/jsref/prop-document-activeelement.html) | 返回当前获取焦点元素                                         |
| [document.addEventListener()](https://www.runoob.com/jsref/met-document-addeventlistener.html) | 向文档添加句柄                                               |
| [document.adoptNode(node)](https://www.runoob.com/jsref/met-document-adoptnode.html) | 从另外一个文档返回 adapded 节点到当前文档。                  |
| [document.anchors](https://www.runoob.com/jsref/coll-doc-anchors.html) | 返回对文档中所有 Anchor 对象的引用。                         |
| [document.baseURI](https://www.runoob.com/jsref/prop-doc-baseuri.html) | 返回文档的绝对基础 URI                                       |
| [document.body](https://www.runoob.com/jsref/prop-doc-body.html) | 返回文档的body元素                                           |
| [document.close()](https://www.runoob.com/jsref/met-doc-close.html) | 关闭用 document.open() 方法打开的输出流，并显示选定的数据。  |
| [document.cookie](https://www.runoob.com/jsref/prop-doc-cookie.html) | 设置或返回与当前文档有关的所有 cookie。                      |
| [document.createAttribute()](https://www.runoob.com/jsref/met-document-createattribute.html) | 创建一个属性节点                                             |
| [document.createComment()](https://www.runoob.com/jsref/met-document-createcomment.html) | createComment() 方法可创建注释节点。                         |
| [document.createDocumentFragment()](https://www.runoob.com/jsref/met-document-createdocumentfragment.html) | 创建空的 DocumentFragment 对象，并返回此对象。               |
| [document.createElement()](https://www.runoob.com/jsref/met-document-createelement.html) | 创建元素节点。                                               |
| [document.createTextNode()](https://www.runoob.com/jsref/met-document-createtextnode.html) | 创建文本节点。                                               |
| [document.doctype](https://www.runoob.com/jsref/prop-document-doctype.html) | 返回与文档相关的文档类型声明 (DTD)。                         |
| [document.documentElement](https://www.runoob.com/jsref/prop-document-documentelement.html) | 返回文档的根节点                                             |
| [document.documentMode](https://www.runoob.com/jsref/prop-doc-documentmode.html) | 返回用于通过浏览器渲染文档的模式                             |
| [document.documentURI](https://www.runoob.com/jsref/prop-document-documenturi.html) | 设置或返回文档的位置                                         |
| [document.domain](https://www.runoob.com/jsref/prop-doc-domain.html) | 返回当前文档的域名。                                         |
| [document.embeds](https://www.runoob.com/jsref/coll-doc-embeds.html) | 返回文档中所有嵌入的内容（embed）集合                        |
| [document.forms](https://www.runoob.com/jsref/coll-doc-forms.html) | 返回对文档中所有 Form 对象引用。                             |
| [document.getElementsByClassName()](https://www.runoob.com/jsref/met-document-getelementsbyclassname.html) | 返回文档中所有指定类名的元素集合，作为 NodeList 对象。       |
| [document.getElementById()](https://www.runoob.com/jsref/met-document-getelementbyid.html) | 返回对拥有指定 id 的第一个对象的引用。                       |
| [document.getElementsByName()](https://www.runoob.com/jsref/met-doc-getelementsbyname.html) | 返回带有指定名称的对象集合。                                 |
| [document.getElementsByTagName()](https://www.runoob.com/jsref/met-document-getelementsbytagname.html) | 返回带有指定标签名的对象集合。                               |
| [document.images](https://www.runoob.com/jsref/coll-doc-images.html) | 返回对文档中所有 Image 对象引用。                            |
| [document.implementation](https://www.runoob.com/jsref/prop-document-implementation.html) | 返回处理该文档的 DOMImplementation 对象。                    |
| [document.importNode()](https://www.runoob.com/jsref/met-document-importnode.html) | 把一个节点从另一个文档复制到该文档以便应用。                 |
| [document.inputEncoding](https://www.runoob.com/jsref/prop-document-inputencoding.html) | 返回用于文档的编码方式（在解析时）。                         |
| [document.lastModified](https://www.runoob.com/jsref/prop-doc-lastmodified.html) | 返回文档被最后修改的日期和时间。                             |
| [document.links](https://www.runoob.com/jsref/coll-doc-links.html) | 返回对文档中所有 Area 和 Link 对象引用。                     |
| [document.normalize()](https://www.runoob.com/jsref/met-document-normalize.html) | 删除空文本节点，并连接相邻节点                               |
| [document.normalizeDocument()](https://www.runoob.com/jsref/met-document-normalizedocument.html) | 删除空文本节点，并连接相邻节点的                             |
| [document.open()](https://www.runoob.com/jsref/met-doc-open.html) | 打开一个流，以收集来自任何 document.write() 或 document.writeln() 方法的输出。 |
| [document.querySelector()](https://www.runoob.com/jsref/met-document-queryselector.html) | 返回文档中匹配指定的CSS选择器的第一元素                      |
| [document.querySelectorAll()](https://www.runoob.com/jsref/met-document-queryselectorall.html) | document.querySelectorAll() 是 HTML5中引入的新方法，返回文档中匹配的CSS选择器的所有元素节点列表 |
| [document.readyState](https://www.runoob.com/jsref/prop-doc-readystate.html) | 返回文档状态 (载入中……)                                      |
| [document.referrer](https://www.runoob.com/jsref/prop-doc-referrer.html) | 返回载入当前文档的文档的 URL。                               |
| [document.removeEventListener()](https://www.runoob.com/jsref/met-document-removeeventlistener.html) | 移除文档中的事件句柄(由 addEventListener() 方法添加)         |
| [document.renameNode()](https://www.runoob.com/jsref/met-document-renamenode.html) | 重命名元素或者属性节点。                                     |
| [document.scripts](https://www.runoob.com/jsref/coll-doc-scripts.html) | 返回页面中所有脚本的集合。                                   |
| [document.strictErrorChecking](https://www.runoob.com/jsref/prop-document-stricterrorchecking.html) | 设置或返回是否强制进行错误检查。                             |
| [document.title](https://www.runoob.com/jsref/prop-doc-title.html) | 返回当前文档的标题。                                         |
| [document.URL](https://www.runoob.com/jsref/prop-doc-url.html) | 返回文档完整的URL                                            |
| [document.write()](https://www.runoob.com/jsref/met-doc-write.html) | 向文档写 HTML 表达式 或 JavaScript 代码。                    |
| [document.writeln()](https://www.runoob.com/jsref/met-doc-writeln.html) | 等同于 write() 方法，不同的是在每个表达式之后写一个换行符。  |

### 获得DOM节点

```js
let h1 = document.getElementsByTagName('h1');//通过标签名获取 返回带有指定标签名的对象集合。
let p1 = document.getElementById('p1');//通过id名获取
let p2 = document.getElementsByClassName('p2');//通过类名获取 返回文档中所有指定类名的元素集合
let father = document.getElementById('father');
let childrens = father.children;//获取父节点下所有子节点
```

### 更新DOM节点

```html
 <div id="father">
     <h1>标题一</h1>
     <p id="p1">p1</p>
     <p class="p2">p2</p>
</div>
<script>
    let h1 = document.getElementsByTagName('h1')[0];//通过标签名获取,注意因为是集合所以下标
    h1.innerText='456';//修改内容
    h1.innnHTML='<strong>456</strong>';//解析HTML标签
    h1.style.color='color';//修改样式，等等....
</script>	
```

### 删除DOM节点

删除节点的步骤：先找到父节点，用父节点删除自己。

```js
let h1 = document.getElementsByTagName('h1')[0];//通过标签名获取
let father = h1.parentElement;//找自己的父节点
father.removeChild(h1);//父节点移除

//也可以通过下标删除，但注意删除是动态的，下标是变化的
let childrens = father.children;//获取父节点下所有子节点
father.removeChild(father.children[0]);//删除第一个，注意删除完后第二个变成第一个
```

### 创建和插入DOM节点

当我们获得一个空的Dom节点时，我们通过innerHtml就可以增加一个元素了，若这个DOM节点已经存在元素了，就不能这么干，**这样会覆盖之前所有的元素。**

#### 插入已有节点

```html
<p id="p3">p3</p>
<div id="father">
    <h1>标题一</h1>
    <p id="p1">p1</p>
    <p class="p2">p2</p>
</div>


<script>
    let father = document.getElementById('father');
    let p3 = document.getElementById('p3');
    father.append(p3);//追加
</script>
```

![image-20231026101115408](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202310261012357.png)



```js
let father = document.getElementById('father');
let p3 = document.getElementById('p3');
let p1 = document.getElementById('p1');
father.insertBefore(p3,p1);//在指定节点前插入
```

#### 创建新标签并插入

```js
let father = document.getElementById('father');
let p3 = document.getElementById('p3');
let htmlScriptElement = document.createElement('script');
htmlScriptElement.setAttribute('type','text/javascript');//设置属性
father.append(htmlScriptElement);
```

## 操作表单

> https://blog.csdn.net/pan_junbiao/article/details/98624439

## Es6新特性

### x:x冗余

```js
const getMousePosition = (x, y) => ({
  x: x,
  y: y
});
```

`getMousePosition` 简单的函数，返回拥有两个属性的对象。 ES6 提供了一个语法糖，消除了类似 `x: x` 这种冗余的写法。 你可以只写一次 `x`，解释器会自动将其转换成 `x: x`（或效果相同的内容）。 下面是使用这种语法重写的同样的函数：

```js
const getMousePosition = (x, y) => ({ x, y });
```

### 对象中的函数

在 ES5 中，当我们需要在对象中定义一个函数的时候，必须像这样使用 `function` 关键字：

```js
const person = {
  name: "Taylor",
  sayHello: function() {
    return `Hello! My name is ${this.name}.`;
  }
};
```

用 ES6 的语法在对象中定义函数的时候，可以删除 `function` 关键词和冒号。 请看以下例子：

```js
const person = {
  name: "Taylor",
  sayHello() {
    return `Hello! My name is ${this.name}.`;
  }
};
```

### 默认无参构造

在 ES5 里面，我们通过定义一个函数 `constructor` 来创建一个对象，然后使用 `new` 关键字来实例化对象。

在 ES6 里，`class` 声明有一个 `constructor` 方法，与 `new` 关键字一起被调用。 如果 `constructor` 方法没有明确定义，那么它就被含蓄地定义为没有参数。

### get and set

> 和java类似，但是不是显式地调用get和set

你可以从对象中获得一个值，也可以给对象的属性赋值。

这些操作通常被称为 getters 以及 setters。

Getter 函数的作用是可以让对象返回一个私有变量，而不需要直接去访问私有变量。

Setter 函数的作用是可以基于传进的参数来修改对象中私有变量。 这些修改可以是计算，或者是直接替换之前的值。

```js
class Book {
  constructor(author) {
    this._author = author;
  }
  // getter
  get writer() {
    return this._author;
  }
  // setter
  set writer(updatedAuthor) {
    this._author = updatedAuthor;
  }
}
const novel = new Book('anonymous');
console.log(novel.writer);
novel.writer = 'newAuthor';
console.log(novel.writer);
```

控制台将显示字符串 `anonymous` 和 `newAuthor`。

请注意用于调用 getter 和 setter 的语法。 它们甚至看起来不像是函数。 getter 和 setter 非常重要，因为它们隐藏了内部的实现细节。

**注意：** 通常会在私有变量前添加下划线（`_`）。 然而，这种做法本身**并不是将变量变成私有的**。

### Map

#### map和对象（Object）的区别

Map 是用于保存键值对，似乎与 Object 非常类似

- 键值

  Object 的键只能是字符串或者 Symbols(ES6 新增基础类型)，但 Map 的键可以是任意值–包括对象、函数等

- 键值顺序

  Map 中的键值是有序的（FIFO 原则），而添加到对象中的键则不是

- 键值个数

  Map 的键值对个数可以从 size 属性获取，而 Object 的键值对个数只能手动或其它自定义方法计算

并且 Object 都有原型，原型链上的键名(保留键)有可能和你在对象上的设置的键名产生冲突。

#### map初始化

Map 的设置与应用也可查看[Typescript 的 Map 对象](https://blog.csdn.net/tjj3027/article/details/126874373)，这部分简单介绍了键是字符串的 Map 的用法

```js
//方法1
let myMap = new Map();
/*这样也可以
const m1 = new Map([['a', 111], ['b', 222]])
console.log(m1) // {"a" => 111, "b" => 222}
*/
// 设置 Map 对象
myMap.set("key1", value1);
myMap.set("key2", value2);
myMap.set("key3", value3);

//方法2
let myMap = new Map([
  ["key1", "value1"],
  ["key2", "value2"]
]);
```

##### 键值是对象与函数的情况

用变量表示对象与函数，应用 Map 的情况与字符串是一样的，但是有一些特殊情况需要注意：

```js
let myMap = new Map();
let keyObj = {};
myMap.set(keyObj, "和键 keyObj 关联的值");
myMap.get(keyObj); // "和键 keyObj 关联的值"
myMap.get({}); // undefined, 因为 keyObj !== {}
```

> 函数也类似，所以复杂类型的键值并不是形式看着一样就是同一个值，必须是指针指向的是同一个地址的键值才能获取到对应值

##### 键值是 NaN

```js
let myMap = new Map();
myMap.set(NaN, "Key is NaN");
myMap.get(NaN); // "Key is NaN"

let otherNaN = Number("foo"); //Number(undefined)
myMap.get(otherNaN); // "Key is NaN"
```

我们知道 **NAN 是和任意值都不相等的，甚至它自己**，但是在上述的测试中发现 Map 中，**Map 认为 NaN 作为键值的时候被认为是相等**并且当 **Number()的参数是 string 类型与 undefined 的时候，myMap.get(otherNaN)的结果也和 NaN 是相同的其它的情况，包括myMap.get(undefined),myMap.get(null)直接获取**，设置otherNaN其它可能与 NaN 相等的情况均是 undefined

```js
let otherNaN = Number(true); //Number(false)
myMap.get(undefined); //undefined
myMap.get(null); //undefined
myMap.get(otherNaN); //undefined
```

#### map 方法

| Map           | 方法                                                         |
| ------------- | ------------------------------------------------------------ |
| map.set()     | 设置键值对，返回 Map  对象                                   |
| map.get()     | 返回键对应的值，如果不存在，则返回  undefined                |
| map.has()     | 返回布尔值，用于判断  Map  中是否包含键对应的值              |
| map.clear()   | 移除  Map  对象的**所有键/值对**                             |
| map.delete()  | 删除键对应元素,成功返回  true,失败或不存在返回 false         |
| map.size      | 返回  Map  对象键/值对的数量                                 |
| map.keys()    | 返回  Iterator  对象，  包含了  Map  对象中每个元素的**键**  |
| map.values()  | 返回 Iterator 对象，包含了 Map 对象中每个元素的**值**        |
| map.entries() | 返回键值对数组迭代对象. map.entries().next().value 可返回每个值 |

##### 方法的使用

```js
let nameSiteMapping = new Map();

// 设置 Map 对象
nameSiteMapping.set("Google", 1);
nameSiteMapping.set("Runoob", 2);
nameSiteMapping.set("Taobao", 3);

// 获取键对应的值
console.log(nameSiteMapping.get("Runoob")); // 2

// 判断 Map 中是否包含键对应的值
console.log(nameSiteMapping.has("Taobao")); // true
console.log(nameSiteMapping.has("Zhihu")); // false

// 返回 Map 对象键/值对的数量
console.log(nameSiteMapping.size); // 3

// 删除 Runoob
console.log(nameSiteMapping.delete("Runoob")); // true
console.log(nameSiteMapping);
// 移除 Map 对象的所有键/值对
nameSiteMapping.clear(); // 清除 Map
console.log(nameSiteMapping);
```

#### map的遍历/迭代

Map 对象中的元素是按顺序插入的，迭代 Map 对象也是按顺序迭代，每一次迭代返回 [key, value] 数组

```js
let nameSiteMapping = new Map();

nameSiteMapping.set("Google", 1);
nameSiteMapping.set("Runoob", 2);
nameSiteMapping.set("Taobao", 3);

// 迭代 Map 中的 key
for (let key of nameSiteMapping.keys()) {
  console.log(key);
}

// 迭代 Map 中的 value
for (let value of nameSiteMapping.values()) {
  console.log(value);
}

// 迭代 Map 中的 key => value
for (let entry of nameSiteMapping.entries()) {
  console.log(entry[0], entry[1]);
}

// 使用对象解析
for (let [key, value] of nameSiteMapping) {
  console.log(key, value);
}

nameSiteMapping.forEach(function (value, key) {
  console.log(key + " = " + value);
}, nameSiteMapping);
```

> forEach 方法的第二个参数，表示绑定处理函数内部的 this 对象，如果省略则表示是调用对象本身，上述可省略第二个参数

#### map 对象操作

##### map和数组转化

```js
let array = [
  ["key1", "value1"],
  ["key2", "value2"]
];
// Map 构造函数可以将 二维 键值对数组转换成Map 对象
let myMap = new Map(array);
// 使用 Array.from 函数可以将Map 对象转换成二维键值对数组
let outArray = Array.from(myMap);
```

##### map的克隆

Map 的克隆是 Map 对象构造函数生成实例，迭代出**新的对象**

```js
let myMap1 = new Map([
  ["key1", "value1"],
  ["key2", "value2"]
]);
let myMap2 = new Map(myMap1);
console.log(myMap1 === myMap2); //  false
```

##### map的合并

合并两个 Map 对象时，如果有重复的键值，则后面的会覆盖前面的

```js
let first = new Map([
  [1, "one"],
  [2, "two"],
  [3, "three"]
]);
let second = new Map([
  [1, "uno"],
  [2, "dos"]
]);
// 对应值即 uno，dos，three
let merged = new Map([...first, ...second]);
```

### Set

Set 对象允许你存储任何类型的唯一值，无论是原始值或者是对象引用。

> Set 结构的键名就是键值

#### Set 中的特殊值

Set [对象存储](https://so.csdn.net/so/search?q=对象存储&spm=1001.2101.3001.7020)的**值总是唯一的**，所以需要判断两个值是否恒等,恒等则不能同时存在。有几个特殊值需要特殊对待：

- +0 与 -0 在存储判断唯一性的时候是恒等的，所以不重复；
- undefined 与 undefined 是恒等的，所以不重复；
- NaN 与 NaN 是不恒等的，但是在 Set 中只能存一个 NaN，不重复。

#### Set 的初始化

```js
let mySet = new Set(); //Set(0) {}
mySet.add(1); // Set(1) {1}
mySet.add(5); // Set(2) {1, 5}
mySet.add(5); // Set(2) {1, 5} 这里体现了值的唯一性
mySet.add("some text"); // Set(3) {1, 5, "some text"} 这里体现了类型的多样性
let o = { a: 1, b: 2 };
mySet.add(o); // Set(5) {1, 5, "some text", {…}}
mySet.add({ a: 1, b: 2 }); // Set(5) {1, 5, "some text", {…}, {…}}
// 这里体现了对象之间引用不同不恒等，即使值相同，内存地址并不相同
```

以上是先创建空集合，使用了 add 方法设置 Set 的值

new Set()通过直接传递参数初始化的方式 请查看下面的类型转换

#### 类型转换

##### Array 与 Set 互相转换

```js
let mySet = new Set(["value1", "value2", "value3"]); // Array 转 Set
console.log(mySet); //Set(3) { 'value1', 'value2', 'value3' }
let myArray = [...mySet]; // 用...操作符，将 Set 转 Array
```

##### String 转 Set

```js
let mySet = new Set("hello"); // Set(4) {"h", "e", "l", "o"}
// 注：Set 中 toString 方法不能将 Set 转换成 String
```

#### Set方法

| Set 方法      | 描述                                   |
| ------------- | -------------------------------------- |
| set.add()     | 添加 set 成员                          |
| set.has()     | Set 是否包含成员，包含返回 true        |
| set.delete()  | 删除成员,成功返回  true,其它返回 false |
| set.size      | 返回  Map  对象键/值对的数量           |
| set.clear()   | **清空 set 实例**，无返回值            |
| set.keys()    | 返回键名的  Iterator  对象             |
| set.values()  | 返回键值的 Iterator 对象               |
| set.entries() | 返回键值对迭代对象                     |
| set.forEach() | 回调函数遍历每个成员                   |

#### Set 迭代

Set 结构的键名就是键值，因此两者相同

```js
let set = new Set(["value1", "value2", "value3"]);

for (let item of set.keys()) {
  console.log(item);
}

for (let item of set.values()) {
  console.log(item);
}

for (let item of set.entries()) {
  console.log(item);
}

set.forEach((value, key) => console.log(value));
//打印
//value1
//value2
//value3
```

#### Set 作用

> 利用 Set 值的唯一性

##### 数组去重

```js
let mySet = new Set([1, 2, 3, 4, 4]);
[...mySet]; // [1, 2, 3, 4]
```

##### Set 的合并-并集

```js
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);
let union = new Set([...a, ...b]); // {1, 2, 3, 4}
```

##### 交集

```js
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);
let intersect = new Set([...a].filter((x) => b.has(x))); // {2, 3}
```

##### 差集

```js
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);
let difference = new Set([...a].filter((x) => !b.has(x))); // {1}
```

### js共享代码

起初，JavaScript 几乎只在 HTML web 扮演一个很小的角色。 今天，一切不同了，很多网站几乎全是用 JavaScript 所写。 为了让 JavaScript 更模块化、更整洁以及更易于维护，ES6 引入了在多个 JavaScript 文件之间共享代码的机制。 它可以导出文件的一部分供其它文件使用，然后在需要它的地方按需导入。 为了使用这一功能， 需要在 HTML 文档里创建一个 `type` 为 `module` 的脚本。 例子如下：

```html
<script type="module" src="filename.js"></script>
```

使用了 `module` 类型的脚本可以使用 `import` 和 `export` 特性（接下来的挑战会介绍）。

#### 导出

假设有一个文件 `math_functions.js`，该文件包含了数学运算相关的一些函数。 其中一个存储在变量 `add` 里，该函数接受两个数字作为参数返回它们的和。 你想在几个不同的 JavaScript 文件中使用这个函数。 要实现这个目的，就需要 `export` 它。

```js
export const add = (x, y) => {
  return x + y;
}
```

上面是导出单个函数常用方法，还可以这样导出：

```js
const add = (x, y) => {
  return x + y;
}

export { add };
```

导出变量和函数后，就可以在其它文件里导入使用从而避免了代码冗余。 重复第一个例子的代码可以导出多个对象或函数，在第二个例子里面的导出语句中添加更多值也可以导出多项，例子如下：

```js
export { add, subtract };
```

#### 默认导出

在 `export` 的课程中，你学习了命名导出语法， 这可以在其他文件中引用一些函数或者变量。

还需要了解另外一种被称为默认导出的 `export` 的语法。 在文件中只有一个值需要导出的时候，通常会使用这种语法。 它也常常用于给文件或者模块创建返回值。

下面是使用 `export default` 的例子：

```js
export default function add(x, y) {
  return x + y;
}

export default function(x, y) {
  return x + y;
}

export default{
    add(x, y) {
      return x + y;
	},
    add2(x, y) {
      return x + y+y;
    }
}
```

第一个是命名函数，第二个是匿名函数。第三个是整体默认导出，所有需要的导出的模块都放在export default中

`export default` 用于为模块或文件声明一个返回值，在每个文件或者模块中应当只默认导出一个值。 此外，你不能将 `export default` 与 `var`、`let` 或 `const` 同时使用。

#### 默认导入

在上一个挑战里，学习了 `export default` 的用法。 还需要一种 `import` 的语法来导入默认的导出。 在下面的例子里，`add` 是 `math_functions.js` 文件的默认导出。 以下是如何导入它：

```js
import add from "./math_functions.js";
```

**这个语法有一处特别的地方， 被导入的 `add` 值没有被花括号（`{}`）所包围**。 `add` 只是一个变量的名字，对应 `math_functions.js` 文件的任何默认导出值。 在导入默认导出时，可以使用任何名字。

#### 导入

`import` 可以导入文件或模块的一部分。 在之前的课程里，例子从 `math_functions.js` 文件里导出了 `add`。 下面看一下如何在其它文件导入它：

```js
import { add } from './math_functions.js';
```

在这里，`import` 会在 `math_functions.js` 里找到 `add`，只导入这个函数，忽略剩余的部分。 `./` 告诉程序在当前文件的相同目录寻找 `math_functions.js` 文件。 用这种方式导入时，相对路径（`./`）和文件扩展名（`.js`）都是必需的。

通过在 `import` 语句里添加项目，可以从文件里导入多个项目，如下：

```js
import { add, subtract } from './math_functions.js';
```

如果想**导入目标文件所有内容**

 可以用 `import * as` 语法来实现。 下面是一个从同目录下的 `math_functions.js` 文件中导入所有内容的例子：

```js
import * as myMathModule from "./math_functions.js";
```

上面的 `import` 语句会创建一个叫作 `myMathModule` 的对象。 这只是一个变量名，可以随便命名。 对象包含 `math_functions.js` 文件里的所有导出，可以像访问对象的属性那样访问里面的函数。 下面是使用导入的 `add` 和 `subtract` 函数的例子：

```js
myMathModule.add(2,3);
myMathModule.subtract(5,3);
```

### Promise 异步

Promise 是异步编程的一种解决方案 - 它在未来的某时会生成一个值。 任务完成，分执行成功和执行失败两种情况。 `Promise` 是构造器函数，需要通过 `new` 关键字来创建。 构造器参数是一个函数，该函数有两个参数 - `resolve` 和 `reject`。 通过它们来判断 promise 的执行结果。 用法如下：

```js
const myPromise = new Promise((resolve, reject) => {

});
```

Promise 有三个状态：`pending`、`fulfilled` 和 `rejected`。 上一个挑战里创建的 promise 一直阻塞在 `pending` 状态里，因为没有调用 promise 的完成方法。 Promise 提供的 `resolve` 和 `reject` 参数就是用来结束 promise 的。 Promise 成功时调用 `resolve`，promise 执行失败时调用 `reject`， 如下文所述，这些方法需要有一个参数。

```js
const myPromise = new Promise((resolve, reject) => {
  if(condition here) {
    resolve("Promise was fulfilled");
  } else {
    reject("Promise was rejected");
  }
});
```

当程序需要花费未知的时间才能完成时（比如一些异步操作），一般是服务器请求，promise 很有用。 服务器请求会花费一些时间，当结束时，需要根据服务器的响应执行一些操作。 这可以用 `then` 方法来实现， 当 promise 完成 `resolve` 时会触发 `then` 方法。 例子如下：

```js
const myPromise = new Promise((resolve, reject) => {
  if(condition here) {
    resolve("Promise was fulfilled");
  } else {
    reject("Promise was rejected");
  }
});
myPromise.then(result => {

});
myPromise.catch(error => {

});
```

`result` 即传入 `resolve` 方法的参数。

当 promise 失败时会调用 `catch` 方法。 当 promise 的 `reject` 方法执行时会直接调用。 用法如上

## this 关键字

JavaScript `this` 关键词指的是它所属的对象。

它拥有不同的值，具体取决于它的使用位置：

- 在方法中，`this` 指的是所有者对象。
- 单独的情况下，`this` 指的是全局对象。
- 在函数中，`this` 默认指的是全局对象。严格模式下，`this` 是 undefined。
- 在事件中，`this` 指的是接收事件的元素。

像 `call()` 和 `apply()` 这样的方法可以将 this 引用到任何对象。

### 方法中的this

在对象方法中，`this` 指的是此方法的“拥有者”。

在本页的例子中，`this` 指的是 person 对象。

person 对象是 fullName 方法的拥有者。

```js
var person = {
  firstName: "Bill",
  lastName : "Gates",
  id       : 678,
  fullName : function() {
    return this.firstName + " " + this.lastName;
  }
};
```

### 单独的this

在单独使用时，拥有者是全局对象，因此 `this` 指的是全局对象。

在浏览器窗口中，全局对象是 `[object Window]`：

```js
var x = this;
```

### 函数中的this

在 JavaScript 函数中，函数的拥有者默认绑定 `this`。

因此，在函数中，`this` 默认指的是全局对象 `[object Window]`。

JavaScript 严格模式不允许默认绑定。因此，在函数中使用时，在严格模式下，`this` 是未定义的（`undefined`）。

### 事件处理程序中的this

在 HTML 事件处理程序中，`this` 指的是接收此事件的 HTML 元素：

```this
<button onclick="this.style.display='none'">
  点击来删除我！
</button>
```

### 显示函数绑定this

`call()` 和 `apply()` 方法是预定义的 JavaScript 方法。

它们都可以用于将另一个对象作为参数调用对象方法。apply`的第二个参数接受的是**数组**。

而`call`不是，它的参数接受的是**分别地提供的参数**，这就是它们最大的区别，其他用法一致。

在下面的例子中，当使用 person2 作为参数调用 person1.fullName 时，`this` 将引用 person2，即使它是 person1 的方法：

```js
var person1 = {
  fullName: function() {
    return this.firstName + " " + this.lastName;
  }
}
var person2 = {
  firstName:"Bill",
  lastName: "Gates",
}
person1.fullName.call(person2);  // 会返回 "Bill Gates"
```

#### apply

语法：apply([thisObj[,argArray]])

定义：应用某一对象的一个方法，用另一个对象替换当前对象。

说明：

- 如果 argArray 不是一个有效的数组或者不是 arguments 对象，那么将导致一个 TypeError。
- 如果没有提供 argArray 和 thisObj 任何一个参数，那么 Global 对象将被用作 thisObj， 并且无法被传递任何参数。

#### call

语法：call([thisObj[,arg1[, arg2[, [,.argN]]]]])

定义：调用一个对象的一个方法，以另一个对象替换当前对象。

说明：

- call 方法可以用来代替另一个对象调用一个方法。call 方法可将一个函数的对象上下文从初始的上下文改变为由 thisObj 指定的新对象。
- 如果没有提供 thisObj 参数，那么 Global 对象被用作 thisObj。

#### bind

bind的区别要同时与apply、call比较。

bind是创造了一个新的函数，apply、call是直接执行。

```js
var person = {
    fullName: function (city, country) {
        return this.firstName + " " + this.lastName + "," + city + "," + country;
    }
}
var person1 = {
    firstName: "John",
    lastName: "Doe"
}
// apply
const bindFun = person.fullName.bind(person1);
```

我们分析一下bindFun这个新函数，其实就是绑定了person1的person.fullName，入参有就是person.fullName的入参，

我们可以直接使用

```js
console.log(bindFun("Oslo", "Norway")) // John Doe,Oslo,Norway
```

`bind`同时可以添加后面的入参，入参形式与`call`一样。

但是不同的是bind可以在创造时添加**固定入参**，后续调用新函数时的入参会加在**固定入参**后面。

```js
var person = {
    fullName: function (city, country) {
        return this.firstName + " " + this.lastName + "," + city + "," + country;
    }
}
var person1 = {
    firstName: "John",
    lastName: "Doe"
}
// apply
const bindFun = person.fullName.bind(person1,"Oslo");// 固定第一个入参"Oslo"
console.log(bindFun()) // John Doe,Oslo,undefined

//这里传递的是第二个入参"Norway"，加在固定入参"Oslo"的后面
console.log(bindFun("Norway")) // John Doe,Oslo,Norway
```

```js
var person = {
    fullName: function (city, country) {
        return this.firstName + " " + this.lastName + "," + city + "," + country;
    }
}
var person1 = {
    firstName: "John",
    lastName: "Doe"
}
// apply
const bindFun = person.fullName.bind(person1,"Oslo", "Norway"); //固定了两个入参
console.log(bindFun()) // John Doe,Oslo,Norway

// 两个入参都已经被固定了，这里传递的入参已经无效了
console.log(bindFun("newOslo", "newNorway")) // John Doe,Oslo,Norway
```



## 正则化

在编程语言中，正则表达式用于匹配指定的字符串。 通过正则表达式创建匹配模式（规则）可以帮你完成指定匹配。

### 正则表达式符号

| 符号 |                             含义                             |
| :--: | :----------------------------------------------------------: |
| `i`  |                       忽略大小写`//i`                        |
| `g`  |                全局匹配，返回所有匹配值`//g`                 |
| `.`  |  通配符,代表任意字符`/b.g/`匹配首为b尾为g的所有长为3字符串   |
| `[]` |     定义一组需要匹配的字符串,`/b[aiu]g/`,中间只为a\|i\|u     |
| `-`  | 使用连字符（`-`）来定义要匹配的字符范围。`/[a-e]/`匹配小写a到e。也可以匹配数字，例如，`/[0-5]/` 匹配 `0` 和 `5` 之间的任意数字，包含 `0` 和 `5`。此外，还可以在单个字符集中组合一系列字母和数字。`/[a-z0-9]/` |
| `^`  | 不想匹配的字符集合,`/[^aeiou]/gi` 匹配所有非元音字符,或者**匹配文本是否在字符串的开始位置** |
| `+`  |      匹配出现一次或者连续多次的的字符（或字符组）`/a+/`      |
| `*`  |                匹配出现零次或多次的字符`/a*/`                |
| `?`  | 懒惰（lazy）匹配，它会匹配到满足正则表达式的字符串的最小可能部分 |
| `\w` |           匹配字母和数字以及下划线 `[A-Za-z0-9_]`            |
| `\W` |              此缩写与 `[^A-Za-z0-9_]` 是一样的               |
| `\d` |                     等同于元字符 `[0-9]`                     |
| `\D` | 等同于字符串 `[^0-9]`，它查找不是 0 - 9 之间数字的单个字符。 |
| `\s` |  将匹配空格、回车符、制表符、换页符和换行符`[ \r\t\f\n\v]`   |
| `\S` |                     等价`[^ \r\t\f\n\v]`                     |
| `{}` | 数量说明符指定匹配模式的上下限,也就是匹配几个,也可不写上限如`{1,}`,也可指定数量如`{3}`，只匹配3次 |

**注意：**在正则表达式上可以有多个标志，比如 `/search/gi`

### 测试正则表达式

JavaScript 中有多种使用正则表达式的方法。 测试正则表达式的一种方法是使用 `.test()` 方法。 `.test()` 方法会把编写的正则表达式和字符串（即括号内的内容）匹配，如果成功匹配到字符，则返回 `true`，反之，返回 `false`。

```js
let testStr = "freeCodeCamp";
let testRegex = /Code/;
testRegex.test(testStr);//创建正则表达式，使用其去匹配字符串
```

`test` 方法会返回 `true`。

### 提取匹配的字符串

可以使用 `.match()` 方法来提取找到的实际**第一个**匹配项。

可以使用字符串来调用 `.match()` 方法，并在括号内传入正则表达式。

```js
"Hello, World!".match(/Hello/);
let ourStr = "Regular expressions";
let ourRegex = /expressions/;
ourStr.match(ourRegex);
```

这里第一个 `match` 将返回 `["Hello"]` 第二个将返回 `["expressions"]`。

请注意， `.match` 语法是目前为止一直使用的 `.test` 方法中的“反向”：

```js
'string'.match(/regex/);
/regex/.test('string');
```

实际上match返回的是一个数组,内有找到的第一个匹配项，下标以及源输入和分组情况

```
let extractStr = "Extract the word 'coding' from this 'coding' string.";
let codingRegex = /coding/; // 修改这一行
let result = extractStr.match(codingRegex); // 修改这一行
console.log(result);
```

```json
[
  'coding',
  index: 18,
  input: "Extract the word 'coding' from this string.",
  groups: undefined
]
```

#### 提取所有匹配值

到目前为止，只能提取或搜寻一次模式匹配。

```js
let testStr = "Repeat, Repeat, Repeat";
let ourRegex = /Repeat/;
testStr.match(ourRegex);
```

在这里 `match` 将返回 `["Repeat"]`。

**要多次搜索或提取模型，你可以使用全局搜索标志： `g`。**

```js
let repeatRegex = /Repeat/g;
testStr.match(repeatRegex);
```

这里 `match` 返回值 `["Repeat", "Repeat", "Repeat"]`

**当加g后，返回值变为匹配值，即使只有一个匹配值，也不会像加标志g前那样返回详尽的匹配信息了。**

```
let extractStr = "Extract the word 'coding' from this  string.";
let codingRegex = /coding/g; // 修改这一行
let result = extractStr.match(codingRegex); // 修改这一行
console.log(result);
```

```json
[ 'coding' ]
```

### 匹配单个字符串区分大小写

如果想要在字符串 `Hello, my name is Kevin.` 中匹配到 `Kevin` 这个单词，可以使用如下正则表达式：`/Kevin/`。 注意，正则表达式中不需要引号。

**任何其他形式的 `Kevin` 都不会被匹配。例如，正则表达式 `/Kevin/` 不会匹配 `kevin` 或者`KEVIN`。**

### 匹配多个不同字符串区分大小写

使用正则表达式`/coding/`，你可以在其他字符串中查找`coding`。

这对于搜寻单个字符串非常有用，但仅限于一种匹配模式。 你可以使用 `alternation` 或 `OR` 操作符搜索多个模式： `|`

此操作符匹配操作符前面或后面的字符。 例如，如果你想匹配 `yes` 或 `no`，你需要的正则表达式是 `/yes|no/`。

### 匹配字符串结尾

可以使用正则表达式的美元符号 `$` 来搜寻字符串的结尾。

```js
let theEnding = "This is a never ending story";
let storyRegex = /story$/;
storyRegex.test(theEnding);
let noEnding = "Sometimes a story will have to end";
storyRegex.test(noEnding);
```

第一次 `test` 调用将返回 `true`, 而第二次调用将返回 `false`。

### + 号

有时，需要匹配出现一次或者连续多次的的字符（或字符组）。 这意味着它至少出现一次，并且可能重复出现。

可以使用 `+` 符号来检查情况是否如此。 记住，字符或匹配模式**必须一个接一个地连续出现**。 这就是说，字符**必须一个接一个地重复**。

例如，`/a+/g` 会在 `abc` 中匹配到一个匹配项，并且返回 `["a"]`。 因为 `+` 的存在，它也会在 `aabc` 中匹配到一个匹配项，然后返回 `["aa"]`。

如果它是检查字符串 `abab`，它将匹配到两个匹配项并且返回`["a", "a"]`，因为`a`字符不连续，在它们之间有一个`b`字符。 最后，因为在字符串 `bcd` 中没有 `a`，因此找不到匹配项。

### * 号

上一次的挑战中使用了加号 `+` 来查找出现一次或多次的字符。 还有一个选项可以匹配出现零次或多次的字符。

执行该操作的字符叫做星号，即`*`。

```js
let soccerWord = "gooooooooal!";
let gPhrase = "gut feeling";
let oPhrase = "over the moon";
let goRegex = /go*/;
soccerWord.match(goRegex);
gPhrase.match(goRegex);
oPhrase.match(goRegex);
```

按顺序排列，三次 `match` 调用将返回值 `["goooooooo"]`，`["g"]` 和 `null`。

```js
let soccerWord = "gooooooooal!";
let gPhrase = "gut feeling";
let oPhrase = "over the moon";
let goRegex = /v*/;//let goRegex = /ov*/;
console.log(soccerWord.match(goRegex))
console.log(gPhrase.match(goRegex))
console.log(oPhrase.match(goRegex))
```

```json
[ '', index: 0, input: 'gooooooooal!', groups: undefined ] 
[ '', index: 0, input: 'gut feeling', groups: undefined ]  
[ '', index: 0, input: 'over the moon', groups: undefined ]

//let goRegex = /ov*/;
[ 'o', index: 1, input: 'gooooooooal!', groups: undefined ]  
null
[ 'ov', index: 0, input: 'over the moon', groups: undefined ]
```

用人话说就是*号代表它前面的字符可以不出现也可以出现多次，比如说上面的例子，当只写`/v*/`都匹配上了而不是返回null，因为字符`v`可以出现可以不出现,此时返回的是空字符串。而当使用`/ov*/`，第二个就返回null，因为该字符串没有以o开头第二个任意的字符，其他都正常返回了。

### ? 号

在正则表达式中，贪婪（greedy）匹配会匹配到符合正则表达式匹配模式的字符串的最长可能部分，并将其作为匹配项返回。 另一种方案称为懒惰（lazy）匹配，它会匹配到满足正则表达式的字符串的**最小可能部分**。

可以将正则表达式 `/t[a-z]*i/` 应用于字符串 `"titanic"`。 这个正则表达式是一个以 `t` 开始，以 `i` 结束，并且中间有一些字母的匹配模式。

正则表达式默认是贪婪匹配，因此匹配返回为 `["titani"]`。 它会匹配到适合该匹配模式的最大子字符串。

但是，你可以使用 `?` 字符来将其变成懒惰匹配。 调整后的正则表达式 `/t[a-z]*?i/` 匹配字符串 `"titanic"` 返回 `["ti"]`。

**注意：应该避免使用正则表达式解析 HTML，但是可以用正则表达式匹配 HTML 字符串。**

问号 `?` 指定可能存在的元素。 这将检查前面的零个或一个元素。 可以将此符号视为前面的元素是可选的

例如，美式英语和英式英语略有不同，可以使用问号来匹配两种拼写。

```js
let american = "color";
let british = "colour";
let rainbowRegex= /colou?r/;
rainbowRegex.test(american);
rainbowRegex.test(british);
```

上面的 `test` 都会返回 `true`。

### ^ 号 否定以及字符串开始

使用字符集中前插入符号（`^`）来创建一个否定字符集，形如 `[^thingsThatWillNotBeMatched]`。 除了在字符集中使用之外，插入符号（^）用于匹配文本是否在字符串的开始位置

```js
let firstString = "Ricky is first and can be found.";
let firstRegex = /^Ricky/;
firstRegex.test(firstString);
let notFirst = "You can't find Ricky now.";
firstRegex.test(notFirst);
```

第一次 `test` 调用将返回 `true`，而第二次调用将返回 `false`。

### () 号

有时候我们想使用正则表达式里的括号 `()` 来检查字符组。

如果想在字符串找到 `Penguin` 或 `Pumpkin`，可以用这个正则表达式：`/P(engu|umpk)in/g`。

然后使用 `test()` 方法检查 test 字符串里面是否包含字符组。

```js
let testStr = "Pumpkin";
let testRegex = /P(engu|umpk)in/;
testRegex.test(testStr);
```

`test` 方法会返回 `true`。

### 捕获组

当你想要匹配一个像下面这样多次出现的单词，

```js
let repeatStr = "row row row your boat";
```

你可以使用 `/row row row/`。**但如果你不知道重复的特定单词即只知道有重复单词，怎么办**？ 捕获组可以用于找到重复的子字符串。

捕获组是通过把要捕获的正则表达式放在括号中来构建的。 在这个例子里， 目标是捕获一个包含字母数字字符的词，所以捕获组是将 `\w+` 放在括号中：`/(\w+)/`。

分组匹配的子字符串被保存到一个临时的“变量”， 可以使用同一正则表达式和反斜线及捕获组的编号来访问它（例如：`\1`）。 捕获组按其开头括号的位置自动编号（从左到右），从 1 开始。

### 捕获组的搜索与替换

搜索功能是很有用的。 但是，当搜索同时也执行更改（或替换）匹配文本的操作时，搜索功能就会显得更加强大。

可以在字符串上使用 `.replace()` 方法来搜索并替换字符串中的文本。 `.replace()` 的输入首先是想要搜索的正则表达式匹配模式。 第二个参数是用于替换匹配的字符串或用于执行某些操作的函数。

```js
let wrongText = "The sky is silver.";
let silverRegex = /silver/;
wrongText.replace(silverRegex, "blue");
```

`replace` 调用将返回字符串 `The sky is blue.`。

你还可以使用美元符号（`$`）访问替换字符串中的捕获组。

```js
"Code Camp".replace(/(\w+)\s(\w+)/, '$2 $1');
```

### 正向先行断言

先行断言 （Lookaheads）是告诉 JavaScript 在字符串中向前查找的匹配模式。 当想要在同一个字符串上搜寻多个匹配模式时，这可能非常有用。正向先行断言会查看并确保搜索匹配模式中的元素存在，但实际上并不匹配。 正向先行断言的用法是 `(?=...)`，其中 `...` 就是需要存在但不会被匹配的部分。

```js
let quit = "qu";
let noquit = "qt";
let quRegex= /q(?=u)/;
let qRegex = /q(?!u)/;//负向
quit.match(quRegex);
noquit.match(qRegex);
```

这两次 `match` 调用都将返回 `["q"]`。

先行断言的更实际用途是检查一个字符串中的两个或更多匹配模式。 这里有一个简单的密码检查器，密码规则是 3 到 6 个字符且至少包含一个数字：**这个代码有匹配问题**

```js
let password = "abc123";
let checkPass = /(?=\w{3,6})(?=\D*\d)/;//这里改成应当改成/^(?=.*\d)\w{3,6}$/ 或者/(?=\w{3,6}$)(?=\D*\d)/
checkPass.test(password);
```

#### 断言冲突

**如果改成`/^(?=\w*)(?=\d+)\w{3,6}$/`**,依然无法成功匹配，这是因为两个正向先行断言产生了冲突

- `^(?=\w*)` 这部分正向预查要求字符串中只包含字母和数字（`\w*` 匹配零个或多个字母和数字）。这个部分会匹配空字符串，因为它允许零个字符。
- `(?=\d+)` 这部分正向预查要求字符串中至少包含一个或多个数字。这是一个正确的条件，但它不需要和前面的正向预查一起使用。
- `\w{3,6}` 这部分要求匹配 3 到 6 个字母或数字

因此，上述正则表达式实际上匹配了包含 3 到 6 个字母或数字的字符串，但它不要求至少包含一个数字，因为第一个正向预查 `^(?=\w*)` 允许零个字符。

**在 JavaScript 中，如果两个正向预查在同一位置发生冲突，正则表达式引擎会尝试所有可能的组合，以找到匹配。如果找到一种组合可以成功匹配整个正则表达式，那么匹配就会成功。如果没有找到匹配的组合，匹配失败。**

举个例子：

```js
/^(?=\d)(?=[a-z])[0-9a-z]+$/
```

这个正则表达式包含两个正向预查：

1. `(?=\d)` 要求字符串中至少包含一个数字。
2. `(?=[a-z])` 要求字符串中至少包含一个小写字母。

如果字符串同时包含至少一个数字和至少一个小写字母，那么正则表达式会匹配成功。正则表达式引擎会尝试不同的组合，例如从字符串的开头找数字，然后从开头找小写字母，或者反过来。只要找到一种组合可以匹配成功，正则表达式就会匹配。

**注意如果改成`/^(?=\d+)\w{3,6}$/`**,也会失败，因为：

`/^(?=\d+)\w{3,6}$/` 的意思是要求字符串中的前面一部分是一个或多个数字，然后后面是 3 到 6 个字母或数字，这些字符可以是任意字符，它并没有要求数字必须是连续的。如果字符串以数字开头，然后包含 3 到 6 个字母或数字，这个正则表达式就会匹配成功。

### 负向先行断言

另一方面，负向先行断言会查看并确保搜索匹配模式中的元素不存在。 负向先行断言的用法是 `(?!...)`，其中 `...` 是希望不存在的匹配模式。 如果负向先行断言部分不存在，将返回匹配模式的其余部分。

### 正则表达式无法限制匹配长度

当使用正则表达式`/[0-9]{5,12}/`限制参数为5到12位数字时，出现可以限制最小长度为5，但无法限制最大长度的问题。例如，目标是限制长度在5-12位之间，使用 `/[0-9]{5,12}/` 判断<5位是不合法的，>12时却合法了，按道理>12位应该是不合法的才对。

```js
let password = "99999945468711";
let checkPass = /[0-9]{5,12}/;
console.log(checkPass.test(password));
```

```json
true
```

**解决方法**:在正则表达式前后添加^与$，如下

```js
/^[0-9]{5,12}$/
```

**正则当中" ^ “代表从开头匹配起，” $ "代表匹配到结尾**，如果匹配条件类似这样 ： /^ 匹配条件 $/，那么表达式要从开头开始匹配到结尾，要完全满足匹配条件；如果没有开头和结尾限定符类似这样： / 匹配条件 /，那么表达式不用从开头开始匹配到结尾，只要包含匹配条件即可。

## 正则化实战

### 用户名检索

需要检索数据库中的所有用户名。 以下是用户在创建用户名时必须遵守的一些简单规则。

1. 用户名只能是数字字母字符。`[A-Za-z0-9]`
2. 用户名中的数字**必须在最后**。 数字可以有零个或多个。 用户名不能以数字开头。`/[0-9]*/`
3. 用户名字母可以是小写字母和大写字母。`[A-Za-z]`
4. 用户名长度必须至少为两个字符。 两位用户名只能使用字母。`/[A-Za-z][0-9]*/`

```js
let username = "JackOfAllTrades";
let userCheck = /^[a-z][a-z]+\d*$|^[a-z]\d\d+$/i; // 修改这一行
let result = userCheck.test(username);

//^[a-z]:检测是否是以字符开头，
//^[a-z][a-z]+:若以字符开头，字符串数量为2或更多
//\d*$:结尾为数字，0或多个
但上述解决不了如Z97类型的要求，引入or
//|^[a-z]:或者以字符开头
//\d\d+$:2或多个数字结尾
或者用下面的写法
let username = "JackOfAllTrades";
const userCheck = /^[a-z]([0-9]{2,}|[a-z]+\d*)$/i;
let result = userCheck.test(username);

//^[a-z][0-9]{2,}$:字符开头,2或多个数字结尾
//^[a-z][a-z]+\d*$:字符开头，字符串数量为2或更多，结尾为数字，0或多个
```

### 匹配大于 5 个字符且有两个连续数字的密码

```js
let sampleWord = "astronaut";
let pwRegex = /(?=\w{5,}$)(?=\D+\d{2})/; // 修改这一行
let result = pwRegex.test(sampleWord);
```

### 删除开头结尾的空白

有时字符串周围存在的空白字符并不是必需的。 字符串的典型处理是删除字符串开头和结尾处的空格。

## js 调试

### console.log

`console.log()` 方法可能是最有用的调试工具，它可以将括号中的内容输出到控制台。 将它放在代码中的关键点可以显示变量在当时的值。 在查看输出之前，最好先想清楚输出应该是什么。 在整个代码中使用检查点来查看计算状态将有助于缩小问题的范围。

```js
console.log('Hello world!');
```

|      方法       |               含义               |
| :-------------: | :------------------------------: |
|  console.log()  |       浏览器控制台打印信息       |
| console.warn()  | 浏览器控制台打印警告信息（黄色） |
| console.warn()  | 浏览器控制台打印错误信息（红色） |
| console.warn()  |             计时开始             |
| console.warn()  |             结束计时             |
| console.clear() |     清除当前控制台的所有输出     |
|  console.dir()  |    可以显示对象所有属性和方法    |

