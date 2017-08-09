JavaScript 糟粕
===============

## 1.全局变量

JavaScript所有的糟粕特性中最为糟糕的就是它对全局变量的依赖性。全局变量就是在所有的作用域中都可见的变量。全局变量在很小的程序中可能会带来方便，但随着程序变得越来越大，他们很快变得难以处理。因为一个全局变量可以被程序的任何部分在任意时间改变，他们会使得程序的行为被极大的复杂化。程序中使用全局变量降低可程序的可靠性。

全局变量是的在同一个程序中运行独立的子程序更难。如果某些全局变量的名称碰巧和子程序中的变量名相同，那么他们将会互相冲突并可能导致程序无法运行，而且通常难以调试。

许多变成语言都是全局变量。例如，java中的public static成员元素就是全局变量。JavaScript的问题不仅在于它允许全局变量，而且在于它要求使用它们。JavaScript没有连接器（linker）。所有的编译单元都载入一个公用的全局对象中。

共有3种方式定义全局变量。第一种是脱离任何函数安排一个var语句：

```js

var foo = value;

```

第二种是直接添加一个属性的全局对象上。全局对象是所有全局变量的容器。在Web浏览器里，全局对象名window：

```js

window.foo = value;

```

第三种是直接使用未经声明的变量。这被称为隐式的全局变量：

```js

foo = value;

```

这本来是为了方便初学者而有意让变量在使用前无需声明。不幸的是，忘记声明变量称为一个非常普遍的错误。JavaScript的策略是让那些忘记预先声明的变量成为全局变量，这导致查找bug非常困难。

## 2.作用域

JavaScript的语法来源于C。在所有其他类似C语言风格的语言里，一个代码块（扩在一堆花括号中的一组语句）会创造一个作用域。代码块中声明的变量在其外部是不可见的。JavaScript采用了这样的块语法，但没有提供会计作用域：代码块中声明的变量在包含此代码块的函数的任何位置都是可见的，这样有其他语言编码经验的程序员大为意外。

在大多数语言中，一般来说声明变量的最好的地方是在第一次用到它的地方。但这种做法在JavaScript采用了这样的块语法，但没有提供块级作用域：代码块中声明的变量在包含此代码块的函数的任何位置都可见。折让有其他语言编码经验的程序员大为意外。

在大多数语言中，一般说声明变量的最好的地方是在第一次用到它的地方。但这种做法在JavaScript里反而是一个坏习惯，因为它没有块级作用域。更好的方式是在每个函数的凯有部分声明所有变量。

### 3.自动插入分号

JavaScript有一个机制，它试图通过自动插入分号来修正缺损的程序。千万不要依靠它，它可能会掩盖更为严重的错误。

有时它会不合时宜地插入分号。请考虑在return语句中自动插入分号导致的后果。如果一个return语句返回一个值，这个值表达式的开始部分必须和return在同一行上：

```js

return 
{
	status: true
};

```

这看起来是要返回一个包含status成员元素的对象。不行的是，自动插入分号让它变成了返回undefined。自动插入分号导致程序被误解却没有任何井盖提醒。如果把{放在上一行的尾部而不是下一行的开头就可以避免该问题：

```js

return {
	status: true
};

```

## 保留字

下面的单词在JavaScript里被保留：

```

abstract boolean break byte case catch char class const continue debugger default delete do double else enum export extends false final finally float for function goto if implements import in instanceof int interface long native new null package private protected public return short static super switch synchronized this throw throws transient true try typeof var volatile void while with

```

这些单词中的大多数并没有在语言中使用

他们不能被用来命名变量或参数。当保留字被用作对象字面量的键值时，他们必须被引号括起来。他们不能被用在点表示法中，所以有时必须使用括号表示法：

```js

var method;                 // ok
var class;                  // 非法
object = {box: value};      // ok
object = {case: value};     // 非法
object = {'case': value};   // ok
object.box = value;         // ok
object.case = value;        // 非法
object['case'] = value;     // ok

```

## Unicode

当涉及JavaScript的时候，Unicode预设计最多65536个字符。而从那以后它的容量慢慢增长到了拥有1百万个字符。

JavaScript的字符时16位的。那足够覆盖原有的65536个字符（现在被称为基本多文种平面）。剩下的百万字符中的每一个都可以用一对字符来表示。Unicode把一对字符视为一个单一的字符。而JavaScript认为一对字符是两个不同的字符。

## typeof

typeof运算符返回一个用于识别运算数类型的字符串。所以：

typeof 98.6

返回 'number'。不行的是：

typeof null

返回'object'而不是'null'。真糟糕。更好的检测null的方式其实很简单：

my_value === null

一个更大的问题是检测对象的值。typeof不能辨别出null对象，但你可以像下面这样做，因为null值为假，而所有对象值为真：

```js

if(my_value && typeof my_value === 'object'){
	// my_value 是一个对象或数组
}

```

在对正则表达式的类型识别上，各种JavaScript的实现不太一致。对于下面代码：

```js

typeof /a/

```

一些实现会报告'object'，而其他的说它是'function'。如果报告'regxp'可能会更有用些，但标准不允许那么做。

## parseInt

parseInt是一个将字符串转换为整数的函数。它在遇到非数字时停止解析，所以parseInt('16')与parseInt('16 tons')产生相同的结果。如果该函数会提醒我们出现额外文本就好，但它不会那么做。

如果该字符串第一个字符时0，那么该字符串将被基于八进制而不是十进制来求值。在八进制中，8和9不是数字，所以parseInt('08')和parseInt('09')产生0作为结果。这个错误导致程序解析日期和时间时出现问题。幸运的是parseInt可以接受一个技术作为参数，如此一来parseInt('08', 10)结果为8.我建议你总是提供这个基数参数。

## +

`+` 运算符可以用于加法运算或字符串链接。它究竟如何执行取决于其参数的类型。如果其中的一个运算符是空字符串，它会把另一个运算数转换成字符串并返回。如果两个元素安抚都是数字，他返回两者之和。否则，它把两个运算数都转换成为字符串并连接起来。这个复杂的行为bug的常见来源。如果你打算用 + 去做加法运算，请确保两个运算数都是整数。

## 浮点数

二进制的浮点数不能正确地处理十进制的小数，因此0.1 + 0.2 不等于 0.3.这是JavaScript中经常被报告的bug，并且它是遵循二进制浮点数算数运算标准而有意导致的结果。这个标准对很多应用都适合的，但它违背了大多数你在中学所学过的关于数字的知识。幸运的是，浮点数中的整数运算是精确的，所以小数表现出来的错误可以通过精度来避免。

举例来说，美元可以通过乘以100而全部转成美分，然后就可以准确地将美分相加。他们的和可以除以100来转换回美元。当人们计算货币时当然会期望得到精确的结果。

## NaN

NaN IEEE 754中定义的一个特殊的数量值。它表示不是一个数字，尽管下面的表达式返回的是true：

```js

typeof NaN === 'number'  // true

```

该值可能会试图将非数字形式的字符串转换为数字时产生。例如：

```js

+ '0'    // 0
+ 'oops' // NaN

```

如果NaN是数学运算中的一个运算数，那么结果就会是NaN。所以，如果你有一个公式链产生NaN的结果，那么至少其中一个输入项是NaN，或者在某个地方产生了NaN。

你可以对NaN进行检测。正如我们之前所见，typeof不能辨别数字和NaN，并且试试证明NaN不等同于它自己。所以，下面的代码结果令人惊讶：

```js

NaN === NaN   // false
NaN !== NaN   // true

```

JavaScript提供了一个isNaN函数来辨别数字与NaN：

```js

isNaN(NaN)     // true
isNaN(0)       // false
isNaN('oops')  // true
isNaN('0')     // false 

```

判断一个值是否可用做数字的最佳方法是使用isFinite函数，因为它会筛选除掉NaN和Infinity。不幸的是，isFinite会试图把他们的运算数转换成一个数字，所以，如果值事实上不是一个数字，它就不是一个好的测试。你可能希望定义自己的isNumber函数：

```js

function isNumber(value) {
	return typeof value === 'number' && isFinite(value);
}

```

## 伪数组

JavaScript没有真正的数组。这也不全是坏事。JavaScript的数组确实很容易使用。不必给它们设置维度，而且他们永远不会产生越界错误。但它们的性能相比真正的数组可能相当糟糕。

typeof运算符不能辨别数组和对象。要判断一个值是否为数组，你还需要检查它的constructor属性：

```js

if (my_value && typeof my_value === 'object' && my_value.constructor === Array) {
	// my_value 是一个数组
}

```

上面的检测对于在不同帧或窗口创建的数组将会给出false。当数组有可能在其他的帧中被创建，下面的检测更为可靠：

```js

if (my_value && typeof my_value === 'object' && typeof my_value.length === 'number' && !(my_value.propertyIsEnumerable('length'))) {
	// my_value 确实是一个数组
}

```

arguments数组不是一个数组，它是一个带有length成员元素的对象。上面的检测会将arguments数组识别为一个数组，有时候这是你希望得到的结果，尽管arguments不包括任何数组的方法。无论如何，如果propertyIsEnumerable方法被覆盖，该检测仍然可能会失败。

## 假值






