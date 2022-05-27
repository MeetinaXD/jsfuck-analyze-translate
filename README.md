# JSFuck `[]()!+`

Original Version (English): [JSFuck By aemkei](./README-en.md).

JSFuck是一种利用Javascript语言特性来编写代码的方式，即仅使用六个字符来完成编写可执行的代码。这种编码方式可谓反人类，但对于学习Javascript有所帮助。

在线demo: [jsfuck.com](http://www.jsfuck.com)

译者：[@MeetinaXD](https://github.com/MeetinaXD) 以及 [MeetinaXD的博客](https://meetinaxd.ltiex.com/)

原作者 [@aemkei](https://twitter.com/aemkei) 以及 [项目贡献者](https://github.com/aemkei/jsfuck/graphs/contributors)

### 试试看吧

以下代码实际上执行的是 `alert(1)`

复制到console运行试试吧！

```js
[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[
]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]][([][(![]+[])[+[]]+([![]]+[][[]
])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+
(!![]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+
!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![
]+[])[+!+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]
+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(![]+[])[+[]]+([![]]+[][[]])[
+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!!
[]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[][(![]+[])[+[]]+([![
]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[
]+!+[]]+(!![]+[])[+!+[]]])[+!+[]+[+[]]]+(!![]+[])[+!+[]]]((![]+[])[+!+[]]+(![
]+[])[!+[]+!+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]+(!![]+[])[+[]]+(!
[]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])
[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[!+[]+!+[]+[+[]]]+[+!+[]]+(
!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[
])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[!+[]+!+[]+[+[]]])()
```

### 基础知识

下面是一些基本对应关系。

    false       =>  ![]
    true        =>  !![]
    undefined   =>  [][[]]
    NaN         =>  +[![]]
    0           =>  +[]
    1           =>  +!+[]
    2           =>  !+[]+!+[]
    10          =>  +[[+!+[]]+[+[]]]
    Array       =>  []
    Number      =>  +[]
    String      =>  []+[]
    Boolean     =>  ![]
    Function    =>  []["filter"]
    run         =>  []["filter"]["constructor"]( CODE )()
    eval        =>  []["filter"]["constructor"]("return eval")()( CODE )
    window      =>  []["filter"]["constructor"]("return this")()

完整的映射列表见 [此处](https://github.com/MeetinaXD/jsfuck-analyze-translate/blob/master/jsfuck.js)。

# 工作原理

**⚠️ 注意：** 缩进一般是有意义的，如果缩进出现在并列的语句中，通常代表着**上下两条等价**。

## `[]` – 方括号

先从方括号开始讲，看看这里都能有什么骚操作。
方括号在这里至关重要，因为它能做的事情非常多，例如：

1. 与数组相关（如访问数组，包裹元素使其成为数组等）
2. 访问对象的属性和方法


### `[]` – 数组字面量

可以用于创建数组：

```js
[]   // 一个空数组
[[]] // 一个包含一个元素的数组 (包含另一个数组)
```

### `[X][i]` – 访问数组 / 对象

```js
[][[]] // undefined, 如同 [][""]
```

我们还能这么用：

```js
"abc"[0]     // 获得一个字符
[]["length"] // 访问属性
[]["fill"]   // 访问方法
```

### `[X][0]` – 包裹为数组的技巧

通过将一个表达式包裹在方括号内，可以使其变为一个长度为1的数组。同时，我们能像普通变量一样访问其中的元素。如访问`[X][0]`，并对其应用操作符`++[X][0]`相当于`X + 1`。

换句话说，方括号`[]`可以代替圆括号`()`来隔离表达式。

```js
          [X][0]           // X
++[ ++[ ++[X][0] ][0] ][0] // X + 3
```

## `+` – 加号

这个操作符也很有用，我们可以用来：

1. 创建number（或者转型为number）
2. 将两个值相加
3. 拼接字符串
4. 创建字符串（或者转型为字符串）

当前版本的JSFuck大量使用了这个技巧，但我们也不好说它们算不算核心内容。

### 转型为Number

```js
+[] // 0 - 数字 0
```

### 数字自增

这里利用上面刚提到的那个技巧(包裹为数组的技巧)：
```js
++[ 0  ][  0  ] // 1
++[ [] ][ +[] ] // 1
```

### 获取 `undefined`

访问**空数组**的其中的某个元素，会返回`undefined`：

```js
[][   0 ] // undefined
[][ +[] ] // 同上，访问下标0 (undefined)
[][  [] ] // 访问其属性 ""
```

### 获取 `NaN`

试图将`undefined`转换为Number类型会返回一个`不是数字(not-a-number)`的结果（即`NaN`）：

```js
+[][[]]    // +undefined == NaN
```

### 数字相加

> **⚠️ 译者注：** 现在我们要开始获取字符的旅程了。需要提前说明的是，JSFuck建立在字符拼接的基础上。为了实现将普通的js代码转换为仅能出现6个字符的代码，我们不得不考虑如何用这6个字符生成所有可能要用到的字符。

可以用加号将几个数字相加：

```js
          1 +           1 // 2
++[[]][+[]] + ++[[]][+[]] // 2，注意缩进，上面和下面是等价的
```

更简洁的方法是使用 `++`：

```js
++[          1][  0] // 2
++[++[[]][  0]][  0] // 2
++[++[[]][+[]]][+[]] // 2
```

用上这些小技巧，我们可以获得以下数字：

`0`, `1`, `2`, `3`, `4`, `5`, `6`, `7`, `8`, `9`

### `+[]` – 转型为String

将表达式与`+[]`（即加号与方括号）组合在一起，就可以将其值转型为string。

```js
  []        +[] // "" - 空字符串
 +[]        +[] // "0"
  [][[]]    +[] // "undefined"
++[][[]]    +[] // "NaN"
++[[]][+[]] +[] // "1"
```

### `"word"[i]` – 获得单个字符

我们还能拿到一个字符串里的某个字符：

```js
  "undefined"          [  0] // "u"
[ "undefined"    ][  0][  0] // "u"
[  undefined +[] ][+[]][+[]] // "u"
[  [][[]]    +[] ][+[]][+[]] // "u"
```

```js
  "undefined"   [           1 ] // "n"
[[][[]]+[]][+[]][ ++[[]][+[]] ] // "n"
```

通过上面介绍的方法，现在我们能拿到`"NaN"`和`"undefined"`，那么现在我们可以获得以下字符：

`N`,`a`,`d`,`e`,`f`,`i`,`n`,`u`.

## `+` – 字符拼接

更进一步，现在我们可以将字符拼接成字符串：

```js
"undefined"[4] // "f"
"undefined"[5] // "i"
"undefined"[6] // "n"
"undefined"[2] // "d"

// 以上代码可以仅用 []+ 三个字符实现，这里使用了上述的自增以及方括号隔离技巧:
[[][[]] +[]] [+[]] [++[++[++[++[[]][+[]]][+[]]][+[]]][+[]]] // "f"
[[][[]] +[]] [+[]] [++[++[++[++[++[[]][+[]]][+[]]][+[]]][+[]]][+[]]] // "i"
[[][[]] +[]] [+[]] [++[++[++[++[++[++[[]][+[]]][+[]]][+[]]][+[]]][+[]]][+[]]] // "n"
[[][[]] +[]] [+[]] [++[++[[]][+[]]][+[]]] // "d"

// 用加号(+)拼接字符
"f"+"i"+"n"+"d" // "find"
```

## `"e"` – 用科学计数法表示数字

现在我们可以从`"undefined"`中获得字符`'e'`，所以现在我们可以用它来表示非常大的数字，也可以用来获得`Infinity`这个值。

```js
+("1e309")         //  Infinity
+("1e309")     +[] // "Infinity"
+("11e100")        //  1.1e+101
+("11e100")    +[] // "1.1e+101"   (可以拿到字符 '.' 和 '+')
+("0.0000001")     //  1e-7
+("0.0000001") +[] // "1e-7"       (可以拿到字符 '-')
```

可以获得以下字符：

`I`,`f`,`i`,`n`,`t`,`y`,`.`,`+`,`-`.


## `[]["方法名"]` – 访问对象中的方法


用字符拼接的方法可以得到任何字符串，包括方法名称（函数名称）。与方括号组合使用，即可访问对象中的方法。

```js
[]["f"+"i"+"n"+"d"] // 此处的 "f" 可以从 "false" 的第一个字符中得到，也可以从其他地方得到
[]["find"]          // 和下面用点(.)符号访问的效果一样
[] .find
```

**注意**：我们在`"undefined"`, `"NaN"` 以及 `"Infinity"`中能凑出的字符，只能满足我们访问到`Array.prototype.find`（通过`[]["find"]`）。

## `方法名 +[]` – 获取函数定义

我们可以通过将函数转型为字符串，从而得到它的定义（字符串）：

```js
[]["find"] +[]
```

这将返回：

```js
"function find() { [native code] }"
```

**注意：**`native函数`转换为字符串后的结果并未在ECMAScript标准中提及，因此在不同浏览器中的表现会有所不同。如Firefox会用`\n`输出一个不太一样的字符串，并且带有额外的换行。

可以获得以下字符：

* `a`,`c`,`d`,`e`,`f`,`i`,`n`,`o`,`t`,`u`,`v`
* ` `, `{`, `}`, `(`, `)`, `[`,`]`

拼凑字符串后可以访问以下方法：

* `.concat`
* `.find`

## `!` – 逻辑取反运算符

这是JSFuck中用到的第四个字符，用于创建boolean类型的值

**注意：** 这个符号也可以用别的来代替，例如 `<` 或者 `=` 。具体可以查看[其他实现方法](#其他实现方法)章节。

### `!X` – 转型为Boolean

逻辑"非"操作符可以用来创建boolean类型值 `false` 和 `true`：

```js
 ![] // false
!![] // true
```

### `!X+[]` – 获得 "true" 和 "false"

可以转型为字符串：

```js
 ![] +[] // "false"
!![] +[] // "true"
```

获得以下字符：

`a`, `e`, `f`, `l`, `r`, `s`, `t`, `u`.

综上所述, 现在我们拿到了 `{}()[]+. INacdefilnorstuvy` 这些字符，可以访问以下方法：

* `call`
* `concat`
* `constructor`
* `entries`
* `every`
* `fill`
* `filter`
* `find`
* `fontcolor`
* `includes`
* `italics`
* `reduce`
* `reverse`
* `slice`
* `sort`

*重点:* 我们还能用类似于 `=` 这样的操作符创建boolean值，这些操作符非常有用。（具体可以查看[其他实现方法](#其他实现方法)章节。）

## `X["constructor"]` – 原始构造器名称

**译者注：** 原文标题使用的是"Primitive wrapper names", 即“原始包装器名称”

对于实例对象来说，使用`.constructor`可以访问到创建这些实例的函数。而对于基本类型的值来说，（使用这种方法）则可以获取到它们对应的内置构造器（built-in wrapper）。

```js
0       ["constructor"] // Number
""      ["constructor"] // String
[]      ["constructor"] // Array
false   ["constructor"] // Boolean
[].find ["constructor"] // Function
```

使用 `+[]` 将上述构造器转型为字符串，即可利用它们的名称来获得更多的字符。

```js
0["constructor"]+[] // "function Number() { ... }"
```

现在有了更多的字符：
`m`, `b`, `S`, `g`, `B`, `A`, `F`.

...并且可以访问更多的方法：

* `arguments`
* `big`
* `bind`
* `bold`
* `name`
* `small`
* `some`
* `sub`
* `substr`
* `substring`
* `toString`
* `trim`


## `()` – 圆括号

### 调用方法

到这里，我们已经可以访问到方法了，这就可以做更多的事情！这里我们就要介绍两位朋友，`(` 和 `)` 这两个符号。

无参数调用方法的示例：

```js
""["fontcolor"]()   // "<font color="undefined"></font>"
[]["entries"]() +[] // "[object Array Iterator]"
```

出现了新的可用字符：

`j`, `<`, `>`, `=`, `"`, `/`

### 带多个参数调用方法

⚠️（在JSFuck中）带多个参数调用方法非常麻烦。原因是JSFuck `[]()!+` 中**不能出现逗号**，而逗号是传递多参数的一个重要条件，但你可以参考[这个方法](https://stackoverflow.com/q/63601330/860099) (由trincot发现)。例如：

调用字符串方法：

`"truefalse".replace("true","1")`

也可以写成：

`["true", "1"].reduce("".replace.bind("truefalse"))`

这么写的原因是因为将多参数改写成数组后，可以变成**无逗号**的形式。于是最后变成这个样子：

```js
["true"]["concat"]("1")["reduce"](""["replace"]["bind"]("truefalse"))
```

同理，调用数组方法 `[1,2,3].slice(1,2)` 也可以写成 `[1,2].reduce([].slice.bind([1,2,3]))` ，最后变成这个样子：

```js
[1]["concat"](2)["reduce"]([]["slice"]["bind"]([1,2,3]))
```

#### 稍微解释一下

reduce会传入四个参数，分别是`previous`, `current`, `index`, `array`，但在这里只有前两个有用。执行以上语句：

```js
["true", "1"].reduce((previous, current, index, array) => {
  console.log(previous, current, index, array)
})

// 输出以下结果
// true 1 1 ["true", 1]
```

而后面的`"".replace.bind("truefalse")`，其中的`"".replace`是为了尽可能简便地调用string中的replace方法，当然也可以替换为`String.prototype.replace`。而`.bind("truefalse")`则是改变`replace`中的`this`。简而言之，这一句可以改写为：

```js
function(...args) {
  return "truefalse".replace(...args)
}
```

而加上前面的部分，则相当于：

```js
(function(...args) {
  return "truefalse".replace(...args)
})(true, 1, 1, ["true", 1]) // 只有前两个参数有用
```

### 带多个参数链式调用方法

如果需要链式调用方法（也就是说下一个函数的执行依靠上一个函数的执行结果进行），而且调用时需要传入多个参数的话，再使用上面这个方法就不太合适了。上面所说的方法将原来代码的顺序颠倒了，只调用一次方法的话还可以接受，但如果需要链式调用，就难以编写和调试。因此，链式调用方法可以参考[这个方法](https://stackoverflow.com/q/63604058/860099) (由trincot发现)。

例如： `"truefalse".replace("true","1").replace("false","0")` 也可以写成：

```js
"truefalse"
    .split().concat([["true", "1"]]).reduce("".replace.apply.bind("".replace))
    .split().concat([["false", "0"]]).reduce("".replace.apply.bind("".replace))
```

可以看到，改写后的代码顺序与原顺序还是相近的。去掉逗号后，最后变成这个样子：

```js
"truefalse"
  ["split"]()["concat"]([["true"]["concat"]("1")])["reduce"](""["replace"]["apply"]["bind"](""["replace"]))
  ["split"]()["concat"]([["false"]["concat"]("0")])["reduce"](""["replace"]["apply"]["bind"](""["replace"]))

```

#### 稍微解释一下

⚠️ 这里对`"".replace.apply.bind("".replace)`作一些解释。这绝不是什么正常的用法，为此我纠结了很久，最后才明白是什么原因：

前面的`"".replace`可以被替换为任意函数（即使是空函数）。例如：

`Function().apply.bind("".replace)`

这与上面的效果完全等价，同时，也能被改写成：

```js
function(str, ...args) {
  return str.replace(...args)
}
```

而前面的部分 `"truefalse".split().concat([["true", "1"]])`，将得到: `["truefalse", ["true", "1"]]`。

其他部分和上面的方法没有区别。

### 对数组带多个参数链式调用方法

对数组链式调用方法（参数可能不止一个）时，也可以用和上面的类似的方法，但是得再加一些小技巧（[点此](https://stackoverflow.com/q/63631908/860099)查看详情）才能满足我们的需要。

这里给出一个示例，如：

`[3,4,5].slice(1,2).concat(6)`

（和上面的例子类似）可以写成：

`[[3,4,5]].concat([[1,2]]).reduce([].slice.apply.bind([].slice)).concat(6)`

**现在的关键是：** 找到一个方法来包裹住数组，使得 `[3,4,5]` 变成 `[[3,4,5]]`。这可以通过 `[3,4,5].map([].constructor).concat([[[]]])[0].slice(-1)` 来完成。

> ⚠️ 重点是.map([].constructor)以及.slice(-1)，去试试吧！

因此，综上所述，我们可以改写为：

```js
[3,4,5]
    // call: slice(1,2)
    .map([].constructor).concat([[[]]])[0].slice(-1)
    .concat([[1,2]]).reduce([].slice.apply.bind([].slice))
    // call next method (in flow)
    .concat(6)
```

最后，去掉了`逗号(,)`和`点(.)`之后：

```js
[3]["concat"](4)["concat"](5)
    ["map"]([]["constructor"])["concat"]([[[]]])[0]["slice"](-1)
    ["concat"]([[1]["concat"](2)])["reduce"]([]["slice"]["apply"]["bind"]([]["slice"]))
    ["concat"](6)
```



### `number.toString(x)` – 获得小写英文字符

Number的 `toString` 方法有一个可选参数，指明需要转换到什么进制（2到36之间）。使用36进制，我们可以获得所有的小写英文字符。

```js
10["toString"](36) // "a"
11["toString"](36) // "b"
...
34["toString"](36) // "y"
35["toString"](36) // "z"
```
得到字符： `abcdefghijklmnopqrstuvwxyz`

### `Function("code")()` – 动态执行代码

Function的构造器是实现JSFuck的精髓所在：它可以接受字符串类型的语句，并创建一个匿名函数，将代码语句作为函数内容。就像大名鼎鼎的邪恶函数 `eval` 一样，它可以将字符串解析为语句并执行，而且还不需要依赖上下文环境（例如 `window`）。随便来一个函数我们都可以获取到它的构造器，例如 `[]["find"]["constructor"]`。

对于将js编译成JSFuck来说，这是第一步，也是最重要的一步。
...

### `Function("return this")()` – window对象

当执行语句 `function () { return this }` 时，我们返回了函数的上下文。（而因为函数是匿名函数）此时的上下文是 `window` 对象。

拿到 `window` 对象对于实现JSFuck同样重要。如果只用方括号，我们只能获得一丁点可怜的资源：number、Array、一些方法函数... 而拿到了全局上下文window，我们就能获取到所有全局变量及其内部的属性。

### 创建正则表达式对象

可以通过以下方法来实现形如 `/pattern/g` 的正则对象：

```js
[]["fill"]["constructor"]("return RegExp")()("pattern","g")
```

去掉了逗号之后（使用上面的[带多个参数调用方法](#带多个参数调用方法)技巧），看上去是这样的 ：

```js
["pattern"]["concat"]("g")["reduce"]([]["fill"]["constructor"]("return RegExp")())
```

---

# 其他实现方法

## 字符拼接

不用 `加号(+)` 的话可以考虑用 `.concat` 方法拼接字符：

```js
"f"["concat"]("i")["concat"]("l")["concat"]("l") // fill
```

前提条件：需要先拼接"c", "o", "n", "c", "a" 和 "t" 这几个字符才能用这个方法。

## 逻辑值(Boolean)

有时 `!` 能用其他操作符代替，而且功能更强大、用途更广。

### `=` – 判断 / 赋值

```js
X == X // true
X == Y // false
X = Y  // 赋值
```

### `>` – 判断 / 创建number

```js
X > Y  // true
X > X  // false
X >> Y // number
```

更复杂一些的例子是只用 `[]>+` 创建字符 `'f'`：

```js
[[ []>[] ] + [] ] [[]>>[]] [[]>>[]]
[[ false ] + [] ] [     0] [     0]
[ "false"       ] [     0] [     0]
  "false"                  [     0]
```

## 数字类型值(Number)

不用 `加号(+)` 的话，可以考虑使用逻辑值以及位移操作创建数字：

```js
true >> false         // 1
true << true          // 2
true << true << true  // 4
```

注意：有些数字（如 `5`）难以获得，但是用字符串是可以实现的，例如： `"11" >> true`。

## 执行函数

以下方法都可以代替圆括号 `()` 执行函数：

1. 使用反引号： `` ` ``
2. 捕获事件： `on...`
3. 使用构造器： `new ...`
4. 重写类型转换方法 `toString|valueOf`
5. Symbol数据类型 `[Symbol...]`

### 使用反引号

不用圆括号的情况下，用反引号 `` ` `` 也可以执行函数。

一般用法是：在ES6用作模板字符串，并作为表达式返回一个字面量。

> ⚠️ 译者注：作者没有解释为什么可以这么用

```js
([]["entries"]``).constructor // Object
```

用这个方法可以访问到`Object`以及"Object"里的字符。

**这个方法的缺陷在于，** 只能传入一个参数（参数的内容也只能是JSFuck六个字符中的），也找不到别的方法可以传入多个参数。也不能传入模版字符串，因为那样就会引入新的字符 `${}`。

使用反引号的其他玩法可以查看 [Gitter chat room](https://gitter.im/aemkei/jsfuck)。

### 重写类型转换方法

另一个半用圆括号执行函数的方法就是重写类型转换方法，例如 `.toString` 或者 `.valueOf`，并通过相应的操作隐式调用它们：

```js
A = []
A["toString"] = A["pop"]
A+"" // 会执行A.pop，因为发生了向string的类型转换
```

**这个方法的缺陷在于，** 无法传递任何参数，而且需要引入字符 `=` ，而且仅在返回基本类型的方法中生效。

所以，这个方法唯一有用的用途就是在Firefox里连接 `.toSource` ，以获得像反斜杠 `\` 这样的特殊字符.

### 触发捕获事件

还可以考虑定义捕获事件这样的方法执行函数，以下是几个例子：

```js
// 在一开始重写onload事件
onload = f

// 写入image标签
document.body.innerHTML = '<img onerror=f src=X />'

// 然后抛出异常
onerror=f; throw 'x'

// 触发事件
onhashchange = f; location.hash = 1;
```

**注意：** 这个方法需要引入字符`=`。

**这个方法的缺陷在于，** 需要先访问到 `window` 或者某个DOM元素才能定义捕获事件。

### 构造器(Constructor)

假装函数是一个对象，并对这个函数使用关键字 `new`，也能执行这个函数。

```js
new f
```

**缺陷：** 同样需要引入新的字符，同时，`new` 关键字还比较棘手，不能通过定义字符集的方式实现。

### Symbol值

Symbol具有唯一性，而且不可枚举。因此它也可以作为标识符以及对象的属性值使用。以下方法可以隐式地调用函数。

```js
f[Symbol.toPrimitive] = f;  f++;
f[Symbol.iterator]    = f; [...f];
```

**注意：** 这个方法需要引入字符`=`。

**缺陷：** `Symbol` 同样不在我们的字符集里。

# 更多资料

JSFuck不是第一个实现这些事情的（指写出反人类的代码）！许多人在尝试突破常规方法，可以参考以下资料扩展阅读：

* [Esolang Wiki: JSFuck](https://esolangs.org/wiki/JSFuck)
* [sla.ckers.org](http://sla.ckers.org/forum/read.php?24,32930) – Original Discussion
* [Xchars.js](http://slides.com/sylvainpv/xchars-js/) – Sylvain Pollet-Villard
* [Non Alphanumeric JavaScript](http://patriciopalladino.com/blog/2012/08/09/non-alphanumeric-javascript.html) – Patricio Palladino
* [Non-alphanumeric code](http://www.businessinfo.co.uk/labs/talk/Nonalpha.pdf) – Gareth Heyes
* [Executing non-alphanumeric JavaScript without parenthesis](http://blog.portswigger.net/2016/07/executing-non-alphanumeric-javascript.html) – Portswigger
