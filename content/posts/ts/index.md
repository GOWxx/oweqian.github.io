---
title: "‍💻 Typescript 使用手册"
date: 2023-03-28T10:20:47+08:00
weight: 3
tags: ["第一技能"]
categories: ["第一技能"]
---

项目地址： [TSHandbook](https://github.com/OweQian/TSHandbook.git)  

<!--more-->

## 类型世界

### 原始类型 

number / string / boolean / null / undefined / symbol / bigint 

```ts
const name: string = 'wangxiaobia';
const age: number = 18;
const male: boolean = false;
const undef: undefined = undefined;
const nul: null = null;
const bigIntVar1: bigint = 9007199254740991n;
const symbolVar: symbol = Symbol('unique');
```

#### null 和 undefined  

null 与 undefined 都是有具体意义的类型。在没有开启 strictNullChecks 检查的情况下，会被视作其他类型的子类型，比如 string 类型会被认为包含了 null 与 undefined 类型：  

```ts
const temp1: null = null;
const temp2: undefined = undefined;
const temp3: string = null;
const temp4: string = undefined;
```

#### void 

描述一个内部没有 return，或没有显示 return 一个值的函数的返回值。  

```ts
function func1 () {};
function func2 () { return };
function func3 () {
  return undefined;
}
```

func1 与 func2 的返回值类型会被隐式推导为 void，显式返回了 undefined 值的 func3 其返回值类型被推导为了 undefined。但在实际的代码执行中，func1 与 func2 的返回值均是 undefined。  

虽然 func3 的返回值类型会被推导为 undefined，但仍可以使用 void 类型进行标注，因为在类型层面 func1、func2、func3 都表示“没有返回一个有意义的值”。  

void 表示一个空类型，而 null 与 undefined 都是一个具有意义的实际类型。而 undefined（null） 能够被赋值给 void 类型的变量，但需要在关闭 strictNullChecks 配置的情况下才能成立。   

```ts
const voidVar1: void = null;
const voidVar2: void = undefined;
```

### 数组类型 

有两种方式来声明一个数组类型：  

```ts
const arr1: string[] = [];
const arr2: Array<string> = [];
```

这两种方式是完全等价的，更多的以前者为主。   

### 元组类型

用来代替数组，已知数组长度和成员类型，在越界访问时（数组不会给出）可以给出类型报错。  

```ts
const arr3: [string, string, string] = ['wang', 'xiao', 'bai'];
arr3[18]; 
```

此时将会产生一个类型错误：长度为“3”的元组类型“[string, string, string]”在索引“18“处没有元素。  

元组内部也可以声明多个与其位置强绑定的，不同类型的元素：  

```ts
const arr4: [string, number, boolean] = ['wang', 18, true];
```

支持在某一个位置上的可选成员：  

```ts
const arr5: [string, number?, boolean?] = ['wang'];
```

TypeScript 4.0 中的具名元组（Labeled Tuple Elements），支持为元组中的元素打上类似属性的标记：  

```ts
const arr6: [name: string, age: number, male: boolean] = ['wang', 18, false];
```

具名元组也支持可选元素的修饰符：  

```ts
const arr7: [name: string, age?: number, male?: boolean] = ['wang'];
```

### 对象类型

interface 来描述对象类型，它代表了这个对象对外提供的接口结构。   

```ts
interface IDescription {
  name: string;
  age: number;
  male: boolean;
}

const obj1: IDescription = {
  name: 'wangxiaobai',
  age: 18,
  male: false,
}
```

每一个属性的值必须一一对应到接口的属性类型，不能有多的属性，也不能有少的属性。  

#### 修饰接口属性  

在接口中用 ? 来标记一个属性为可选：  

```ts
interface IDescription {
  name: string;
  age: number;
  male?: boolean;
  func?: Function;
}

const obj2: IDescription = {
  name: 'wangxiaobai',
  age: 18,
}
```

定义一个可选的布尔类型属性，当你访问 obj2.male 时，它的类型是 boolean | undefined。  

定义一个可选的函数类型属性，进行调用：obj2.func() ，此时将会产生一个类型报错：不能调用可能是未定义的方法。  

可选属性标记不会影响你对这个属性进行赋值。   

```ts
obj2.male = false;
obj2.func = () => {};
```

在接口中用 readonly 来标记一个属性为只读，防止属性被再次赋值。  

```ts
interface IDescriptionProps {
  readonly name: string;
  age: number;
}

const obj3: IDescriptionProps = {
  name: 'wangxiaobai',
  age: 18,
};

obj3.name = 'OweQian';
```

此时会抛出错误，无法分配到 "name" ，因为它是只读属性。   

ps: 在数组和元组层面也存在着只读的修饰。  

* 只能将整个数组/元组标记为只读，而不能像对象那样标记某个属性为只读。  
* 一旦被标记为只读，那这个只读数组/元组的类型上，将不再具有 push、pop 等方法。  

#### object、Object、{} 

在 TS 中，Object 包含了所有的类型:  

```ts
const temp1: Object = undefined;
const temp2: Object = null;
const temp3: Object = void 0;
const temp4: Object = 'wangxiaobai';
const temp5: Object = 18;
const temp6: Object = () => {};
const temp7: Object = { name: 'wangxiaobai' };
const temp8: Object = [];
```

对于 undefined、null、void 0 ，需要关闭 strictNullChecks。  

和 Object 类似的还有 Boolean、Number、String、Symbol，这几个装箱类型（Boxed Types），同样包含了一些超出预期的类型。  

以 String 为例，它同样包括 undefined、null、void，以及代表的拆箱类型（Unboxed Types）string，但并不包括其他装箱类型对应的拆箱类型，如 boolean 与基本对象类型。  

```ts
const temp9: String = undefined;
const temp10: String = null;
const temp11: String = void 0;
const temp12: String = 'wangxiaobai';

// 以下不成立，因为不是字符串类型的拆箱类型
const temp13: String = 18; // X
const temp14: String = { name: 'wangxiaobai' }; // X
const temp15: String = () => {}; // X
const temp16: String = []; // X
```

任何情况下，你都不应该使用这些装箱类型。  

object 类型的引入就是为了解决 Object 类型的错误使用，它代表所有非原始类型的类型，即数组、函数、对象类型。  

```ts
const temp17: object = undefined;
const temp18: object = null;
const temp19: object = void 0;

const temp20: object = 'wangxiaobai'; // X
const temp21: object = 18; // X

const temp22: object = { name: 'wangxiaobai' };
const temp23: object = () => {};
const temp24: object = [];
```

{} 是一个对象字面量类型，它代表内部无属性定义的空对象，这类似于 Object。  

```ts
const temp25: {} = undefined;
const temp26: {} = null;
const temp27: {} = void 0;
const temp28: {} = 'wangxiaobai';
const temp29: {} = 18;
const temp30: {} = () => {};
const temp31: {} = { name: 'wangxiaobai' };
const temp32: {} = [];
```

无法对变量进行任何赋值操作。  

```ts
temp31.age = 18; // X 类型“{}”上不存在属性“age”
```

总结：  

* 在任何时候都不要使用 Object 以及类似的装箱类型。   
* 当你不确定某个变量的具体类型，但能确定不是原始类型，可以使用 object。  
* 可以使用 Record<string, unknown> 或 Record<string, any> 表示对象。  
* 可以使用 any[] 或 unknown[] 表示数组。  
* 可以使用 (...args: any[]) => any 表示函数。  
* 避免使用 {}。  

### 字面量类型与联合类型

定义一个接口，它描述了响应的消息结构：   

```ts
interface IResponseProps {
  code: number;
  status: string;
  data: any;
}
```

这里的 code 与 status 实际值会来自于一组确定值的集合，比如 code 可能是 10000 / 10001 / 50000，status 可能是 "success" / "failure"。   

上面的类型只给出了一个宽泛的 number / string，既不能在访问 code 时获得精确的提示，也失去了 TypeScript 类型即文档的功能。  

可以使用字面量类型加上联合类型进行改写：  

```ts
interface IResponseProps {
  code: 10000 | 10001 | 50000;
  status: 'success' | 'failure';
  data: any;
}
```

这时就能在访问时获得精确地类型推导了。  

<img src="https://oweqian.oss-cn-hangzhou.aliyuncs.com/ts/img_01.png" alt="" width="700" />  

对于 declare var res: Res，它是快速生成一个符合指定类型，但没有实际值的变量，同时它也不存在于运行时中。   

#### 字面量类型

"success" 不是一个值吗？为什么它可以作为类型？在 TypeScript 中，这叫做字面量类型（Literal Types），它代表着比原始类型更精确的类型，同时也是原始类型的子类型。  

字面量类型主要包括字符串字面量类型、数字字面量类型、布尔字面量类型和对象字面量类型。   

```ts
const name: 'wangxiaobai' = 'wangxiaobai';
const age: 18 = 18;
const male: false = false;
```

为什么说字面量类型比原始类型更精确？  

```ts
// 报错！不能将类型“"wangxiaobai18"”分配给类型“"wangxiaobai"”。
const str1: 'wangxiaobai' = 'wangxiaobai18';
const str2: string = 'wangxiaobai';
const str3: string = 'wangxiaobai3';
```

原始类型的值可以包括任意的同类型值，而字面量类型要求的是值级别的字面量一致。  

单独使用字面量类型比较少见，因为单个字面量类型并没有什么实际意义。它通常和联合类型（即这里的 |）一起使用，表达一组字面量类型：   

```ts
interface ITempProps {
  bool: true | false;
  num: 1 | 2 | 3;
  str: 'wang' | 'xiao' | 'bai'
}
```

#### 联合类型

它代表了一组类型的可用集合，只要最终赋值的类型属于联合类型的成员之一，就可以认为符合这个联合类型。  

联合类型对其成员并没有任何限制，除了上面这样对同一类型字面量的联合，还可以将各种类型混合到一起。   

```ts
interface ITempMixedProps {
  mixed: true | string | 18 | {} | (() => {}) | (1 | 2)
}
```

这里有几点需要注意：  

* 联合类型中的函数类型需要使用括号 () 包裹起来  
* 函数类型并不存在字面量类型，因此这里的 (() => {}) 就是一个合法的函数类型  
* 可以在联合类型中进一步嵌套联合类型，但这些嵌套的联合类型最终都会被展平到第一级中  

常用场景之一是通过多个对象类型的联合，来实现手动的互斥属性，即这一属性如果有字段 1，那就没有字段 2。  

```ts
interface ITempProps {
  user:
    | {
    vip: true;
    expires: string;
  }
  | {
    vip: false;
    promotion: string;
  }
}

declare var temp: ITempProps;

if (temp.user.vip) {
  console.log(temp.user.expires);
}
```

user 属性会满足普通用户与 VIP 用户两种类型，这里 vip 属性的类型基于布尔字面量类型声明。  

在实际使用时可以通过判断此属性为 true，确保接下来的类型推导都会将其类型收窄到 VIP 用户的类型（即联合类型的第一个分支）。  

可以通过类型别名来复用一组字面量联合类型：  

```ts
type Code = 10000 | 10001 | 50000;
type Status = 'success' | 'failure';
```

除了原始类型的字面量类型以外，对象类型也有着对应的字面量类型。  

#### 对象字面量类型

对象字面量类型就是一个对象类型的值，这也就意味着这个对象的值全都为字面量值。  

```ts
interface ITempProps {
  obj: {
    name: 'wangxiaobai';
    age: 18
  }
}

const temp: ITempProps = {
  obj: {
    name: 'wangxiaobai',
    age: 18
  }
}
```

如果要实现一个对象字面量类型，意味着完全的实现这个类型每一个属性的每一个值。  

无论是原始类型还是对象类型的字面量类型，它们的本质都是类型而不是值。在编译时同样会被擦除，同时也是被存储在内存中的类型空间而非值空间。

### 枚举类型

```ts
enum PageUrl {
  Home_Page_Url = 'home',
  Setting_Page_Url = 'setting',
  Share_Page_Url = 'share',
}

const home = PageUrl.Home_Page_Url;
```

使用枚举拥有了更好的类型提示，并且这些常量被真正地约束在一个命名空间下。  

如果没有声明枚举的值，它会默认使用数字枚举，并且从 0 开始，以 1 递增。  

```ts
enum Items {
  Foo,
  Bar,
  Baz,
}
```

在这个例子中，Items.Foo、Items.Bar、Items.Baz 的值依次是 0、1、2。

如果只为某一个成员指定了枚举值，那么之前未赋值成员仍然会使用从 0 递增的方式，之后的成员则会开始从枚举值递增。  

```ts
enum Items {
  // 0
  Foo,
  Bar = 18,
  // 19
  Baz,
}
```

在数字型枚举中，可以使用延迟求值的枚举值，比如函数。  

```ts
const returnNum = () => 100 + 18;
enum Items {
  Foo = returnNum(),
  Bar = 18,
  Baz,
}
```

但要注意，延迟求值的枚举值是有条件的。如果使用了延迟求值，那么没有使用延迟求值的枚举成员必须放在使用常量枚举值声明的成员之后，或者放在第一位。   

```ts
enum Items {
  Baz,
  Foo = returnNum(),
  Bar = 18,
}
```

也可以同时使用字符串枚举值和数字枚举值。   

```ts
enum Mixed {
  Num = 18,
  Str = 'wangxiaobai'
}
```

枚举和对象的重要差异在于，对象是单向映射的，只能从键映射到键值。而枚举是双向映射的，可以从枚举成员映射到枚举值，也可以从枚举值映射到枚举成员。    

```ts
enum Items {
  Foo,
  Bar,
  Baz,
}

const fooValue = Items.Foo; // 0
const fooKey = Items[0]; // 'Foo'
```

要了解这一现象的本质，需要来看一看枚举的编译产物，如以上的枚举会被编译为以下 JavaScript 代码：   

```javascript
"use strict";
var Items;
(function (Items) {
  Items[Items["Foo"] = 0] = "Foo";
  Items[Items["Bar"] = 1] = "Bar";
  Items[Items["Baz"] = 2] = "Baz";
})(Items || (Items = {}));
```

obj[k] = v 的返回值即是 v，因此这里的 obj[obj[k] = v] = k 本质上就是进行了 obj[k] = v 与 obj[v] = k 这样两次赋值。  

仅有值为数字的枚举成员才能够进行这样的双向枚举，字符串枚举成员仍然只会进行单次映射：  

```ts
enum Items {
  Foo,
  Bar = 'BarValue',
  Baz = 'BazValue'
}
```

```javascript
// 编译结果，只会进行 键-值 的单向映射
"use strict";
var Items;
(function (Items) {
  Items[Items["Foo"] = 0] = "Foo";
  Items["Bar"] = "BarValue";
  Items["Baz"] = "BazValue";
})(Items || (Items = {}));
```

#### 常量枚举

常量枚举和枚举相似，只是其声明多了一个 const。   

```ts
const enum Items {
  Foo,
  Bar,
  Baz,
}

const fooValue = Items.Foo;
```

它和普通枚举的差异主要在访问性与编译产物。对于常量枚举，只能通过枚举成员访问枚举值（而不能通过值访问成员）。同时，在编译产物中并不会存在一个额外的辅助对象（如上面的 Items 对象），对枚举成员的访问会被直接内联替换为枚举的值。   

以上的代码会被编译为如下形式：

```javascript
const fooValue = 0 /* Foo */; // 0
```

### 函数类型

#### 类型签名

函数类型是为了描述了函数入参类型与函数返回值类型，它们同样使用 : 的语法进行类型标注。   

```ts
function foo(name: string): number {
return name.length;
}
```

在函数类型中同样存在类型推导。比如下面这个例子，你可以不写返回值处的类型，它也能被正确推导为 number 类型。    

function name () {} 这一声明函数的方式为函数声明（Function Declaration）。除了函数声明以外，还可以通过函数表达式（Function Expression），即 const foo = function(){} 的形式声明一个函数。      

在表达式中进行类型声明的方式是这样的：   

```ts
const foo = function (name: string): number {
return name.length
}
```

还可以像对变量进行类型标注那样，对 foo 这个变量进行类型声明：   

```ts
const foo: (name: string) => number = function (name) {
return name.length
}
```

这里的 (name: string) => number 是 TypeScript 中的函数类型签名，有点类似 ES6 中的箭头函数。  

而实际的箭头函数的类型标注也是类似的：  

```ts
// 方式一
const foo = (name: string): number => {
	return name.length
}

// 方式二
const foo: (name: string) => number = (name) => {
	return name.length
}
```

在方式二的声明方式中，你会发现函数类型声明混合箭头函数声明时，代码的可读性非常差。  

一般不推荐这么使用，要么直接在函数中进行参数和返回值的类型声明，要么使用类型别名将函数声明抽离出来：   

```ts
type FuncFoo = (name: string) => number

const foo: FuncFoo = (name) => {
	return name.length
}
```

如果只是为了描述这个函数的类型结构，也可以使用 interface 来进行函数声明：  

```ts
interface FuncFooStruct {
	(name: string): number
}
```

这时的 interface 被称为 Callable Interface。  

#### void 类型

在 TypeScript 中，一个没有返回值（即没有调用 return 语句）的函数，其返回类型应当被标记为 void 而不是 undefined，即使它实际的值是 undefined。

```ts
// 没有调用 return 语句
function foo(): void { }
```

在 TypeScript 中，undefined 类型是一个实际的、有意义的类型值，而 void 代表着空的、没有意义的类型值。    

相比之下，void 类型就像是 JavaScript 中的 null 一样。因此在没有实际返回值时，使用 void 类型能更好地说明这个函数没有进行返回操作。   

但当函数中有 return 语句但没有显示返回一个值时，其实更好的方式是使用 undefined：   

```ts
function bar(): undefined {
	return;
}
```

这个函数进行了返回操作，但没有返回实际的值。   

#### 可选参数

函数存在一些可选参数的情况，当不传入参数时函数会使用此参数的默认值。正如在对象类型中使用 ? 描述一个可选属性一样，在函数类型中也使用 ? 描述一个可选参数：   

```ts
// 在函数逻辑中注入可选参数默认值
function foo1(name: string, age?: number): number {
	const inputAge = age ?? 18;
	return name.length + inputAge
}

// 直接为可选参数声明默认值
function foo2(name: string, age: number = 18): number {
  const inputAge = age || 18;
  return name.length + inputAge
}
```

可选参数必须位于必选参数之后。这里的可选参数类型也可以省略，如这里原始类型的情况可以直接从提供的默认值类型推导出来。但对于联合类型或对象类型的复杂情况，还是需要老老实实地进行标注。    

#### rest 参数

rest 参数的类型标注也比较简单，由于其实际上是一个数组，这里也应当使用数组类型进行标注：   

对于 any 类型，你可以简单理解为它包含了一切可能的类型。   

```ts
function foo(arg1: string, ...rest: any[]) { }
```

也可以使用元组类型进行标注：   

```ts
function foo(arg1: string, ...rest: [number, boolean]) { }

foo('wangxiaobai', 18, true)
```

#### 重载

在某些逻辑较复杂的情况下，函数可能有多组入参类型和返回值类型：   

```ts
function func(foo: number, bar?: boolean): string | number {
	if (bar) {
		return String(foo);
	} else {
		return foo * 18;
	}
}
```

在这个实例中，函数的返回类型基于其入参 bar 的值，并且从其内部逻辑中知道，当 bar 为 true，返回值为 string 类型，否则为 number 类型。而这里的类型签名完全没有体现这一点，只知道它的返回值是个联合类型。   

要想实现与入参关联的返回值类型，可以使用 TypeScript 提供的函数重载签名（Overload Signature），将以上的例子使用重载改写：   

```ts
function func(foo: number, bar: true): string;
function func(foo: number, bar?: false): number;
function func(foo: number, bar?: boolean): string | number {
	if (bar) {
		return String(foo);
	} else {
		return foo * 18;
	}
}

const res1 = func(18); // number
const res2 = func(18, true); // string
const res3 = func(18, false); // number
```

这里的三个 function func 其实具有不同的意义：  

* function func(foo: number, bar: true): string，重载签名一，传入 bar 的值为 true 时，函数返回值为 string 类型。
* function func(foo: number, bar?: false): number，重载签名二，不传入 bar，或传入 bar 的值为 false 时，函数返回值为 number 类型。
* function func(foo: number, bar?: boolean): string | number，函数的实现签名，会包含重载签名的所有可能情况。

基于重载签名就实现了将入参类型和返回值类型的可能情况进行关联，获得了更精确的类型标注能力。   

这里有一个需要注意的地方，拥有多个重载声明的函数在被调用时，是按照重载的声明顺序往下查找的。   

因此在第一个重载声明中，为了与逻辑中保持一致，即在 bar 为 true 时返回 string 类型，这里需要将第一个重载声明的 bar 声明为必选的字面量类型。   

你可以试着为第一个重载声明的 bar 参数也加上可选符号，然后就会发现第一个函数调用错误地匹配到了第一个重载声明。   

#### 异步函数、Generator 函数等类型签名

对于异步函数、Generator 函数、异步 Generator 函数的类型签名，其参数签名基本一致，而返回值类型则稍微有些区别：   

```ts
async function asyncFunc(): Promise<void> {}

function* genFunc(): Iterable<void> {}

async function* asyncGenFunc(): AsyncIterable<void> {}
```

对于异步函数（即标记为 async 的函数），其返回值必定为一个 Promise 类型，而 Promise 内部包含的类型则通过泛型的形式书写，即 Promise<T>。   

### Class

#### 类与类成员的类型签名  

类的主要结构有构造函数、属性、方法和访问符（Accessor）。   

属性的类型标注类似于变量，而构造函数、方法、存取器的类型编标注类似于函数：  

```ts
class Foo {
  prop: string;

  constructor(inputProps: string) {
    this.prop = inputProps;
  }

  print (addon: string): string {
    return `${this.prop} and ${addon}`;
  }

  get PropA(): string {
    return `${this.prop}+A`;
  }

  set PropA(value: string) {
    this.prop = `${value}`;
  }
}
```

setter 方法不允许进行返回值的类型标注，可以理解为 setter 的返回值并不会被消费，它是一个只关注过程的函数。类的方法同样可以进行函数那样的重载。  

类也可以通过类声明和类表达式的方式创建。上面的写法即是类声明，而使用类表达式的语法则是这样的：  

```ts
const Foo = class {
  prop: string;

  constructor(inputProps: string) {
    this.prop = inputProps;
  }

  print (addon: string): string {
    return `${this.prop} and ${addon}`;
  }

  get PropA(): string {
    return `${this.prop}+A`;
  }

  set PropA(value: string) {
    this.prop = `${value}`;
  }
}
```

##### 修饰符

在 TypeScript 中能够为 Class 成员添加这些修饰符：public / private / protected / readonly。   

除 readonly 以外，其他三位都属于访问性修饰符，而 readonly 属于操作性修饰符（就和 interface 中的 readonly 意义一致）。   

这些修饰符应用的位置在成员命名前：  

```ts
class Foo {
  private prop: string;

  constructor(inputProps: string) {
    this.prop = inputProps;
  }

  protected print (addon: string): string {
    return `${this.prop} and ${addon}`;
  }

  public get PropA(): string {
    return `${this.prop}+A`;
  }

  public set PropA(value: string) {
    this.prop = `${value}`;
  }
}
```

通常不会为构造函数添加修饰符，而是让它保持默认的 public。  

* public：此类成员在类、类的实例、子类中都能被访问。  
* private：此类成员仅能在类的内部被访问。  
* protected：此类成员仅能在类与子类中被访问。  

当你不显式使用访问性修饰符，成员的访问性默认会被标记为 public。简单起见，可以在构造函数中对参数应用访问性修饰符：   

```ts
class Foo {
  constructor(public arg1: string, private arg2: boolean) {
  }
}

new Foo('wangxiaobai', false);
```

此时，参数会被直接作为类的成员（即实例的属性），免去后续的手动赋值。   

##### 静态成员

在 TypeScript 中，可以使用 static 关键字来标识一个成员为静态成员：   

```ts
class Foo {
  static staticHandler () {}
  public instanceHandler () {}
}
```

不同于实例成员，在类的内部静态成员无法通过 this 来访问，需要通过 Foo.staticHandler 这种形式进行访问。   

可以查看编译到 ES5 及以下 target 的 JavaScript 代码（ES6 以上就原生支持静态成员了），来进一步了解它们的区别：   

```javascript
var Foo = /** @class */ (function () {
	function Foo() {
	}
	Foo.staticHandler = function () { };
	Foo.prototype.instanceHandler = function () { };
		return Foo;
	}());
```

从中可以看到，静态成员直接被挂载在函数体上，而实例成员挂载在原型上，这就是二者的最重要差异：静态成员不会被实例继承，它始终只属于当前定义的这个类（以及其子类）。而原型对象上的实例成员则会沿着原型链进行传递，也就是能够被继承。  

而对于静态成员和实例成员的使用时机，其实并不需要非常刻意地划分。比如用类 + 静态成员来收敛变量与 utils 方法：   

```ts
class Utils {
  static identifier = 'wangxiaobai'

  static studyWithU () {

  }

  static makeUHappy () {
    Utils.studyWithU()
  }
}

Utils.makeUHappy()
```

#### 继承、实现、抽象类

说到 Class，那一定离不开继承。TypeScript 中也使用 extends 关键字来实现继承：  

```ts
class Base {}

class Derived extends Base {}
```

对于这里的两个类，比较严谨的称呼是基类（Base）与派生类（Derived）。当然，如果叫父类与子类也没问题。关于基类与派生类，需要了解的主要是派生类对基类成员的访问与覆盖操作。  

基类中的哪些成员能够被派生类访问，完全是由其访问性修饰符决定的。派生类中可以访问到使用 public 或 protected 修饰符的基类成员。   

除了访问以外，基类中的方法也可以在派生类中被覆盖，但仍然可以通过 super 访问到基类中的方法：   

```ts
class Base {
  print () {}
}

class Derived extends Base {
  print() {
    super.print();
    // ...
  }
}
```

在派生类中覆盖基类方法时，并不能确保派生类的这一方法能覆盖基类方法，万一基类中不存在这个方法呢？  

所以，TypeScript 4.3 新增了 override 关键字，来确保派生类尝试覆盖的方法一定在基类中存在定义：  

```ts
class Base {
  printWithLove() {}
}

class Derived extends Base {
  override print() {
    super.print();
  }
}
```

在这里 TypeScript 将会给出错误，因为尝试覆盖的方法并未在基类中声明。通过这一关键字就能确保首先这个方法在基类中存在，同时标识这个方法在派生类中被覆盖了。   

除了基类与派生类以外，还有一个比较重要的概念：抽象类。   

抽象类是对类结构与方法的抽象，简单来说，一个抽象类描述了一个类中应当有哪些成员（属性、方法等），一个抽象方法描述了这一方法在实际实现中的结构，抽象方法其实描述的就是这个方法的入参类型与返回值类型。  

抽象类使用 abstract 关键字声明：  

```ts
abstract class AbsFoo {
  abstract absProp: string;
  abstract get absGetter (): string;
  abstract absMethod (name: string): string;
}
```

注意，抽象类中的成员也需要使用 abstract 关键字才能被视为抽象类成员，如这里的抽象方法。   

实现（implements）一个抽象类：   

```ts
class Foo implements AbsFoo {
  absProp: string = 'wangxiaobai';

  get absGetter(): string {
    return 'wangxiaobai';
  }

  absMethod(name: string): string {
    return name;
  }
}
```

此时，必须完全实现这个抽象类的每一个抽象成员。需要注意的是，在 TypeScript 中无法声明静态的抽象成员。   

对于抽象类，它的本质就是描述类的结构。interface 不仅可以声明函数结构，也可以声明类的结构：  

```ts
interface IFooStruct {
  absProp: string;
  get absGetter (): string;
  absMethod (name: string): string;
}

class Foo implements IFooStruct {
  absProp: string = 'wangxiaobai';

  get absGetter(): string {
    return 'wangxiaobai';
  }

  absMethod(name: string): string {
    return name;
  }
}
```

在这里让类去实现了一个接口。这里接口的作用和抽象类一样，都是描述这个类的结构。除此以外，还可以使用 Newable Interface 来描述一个类的结构（类似于描述函数结构的 Callable Interface）：   

```ts
class Foo { }

interface FooStruct {
	new(): Foo
}

declare const NewableFoo: FooStruct;

const foo = new NewableFoo();
```

### 内置类型

#### any 

TypeScript 中提供了一个内置类型 any，表示任意类型。    

```
log(message?: any, ...optionalParams: any[]): void
```

一个被标记为 any 类型的参数可以接受任意类型的值。除了 message 是 any 以外，optionalParams 作为一个 rest 参数，也使用 any[] 进行了标记，这就意味着你可以使用任意类型的任意数量类型来调用这个方法。    

除了显式的标记一个变量或参数为 any，在某些情况下你的变量 / 参数也会被隐式地推导为 any。比如使用 let 声明一个变量但不提供初始值，以及不为函数参数提供类型标注：   

````ts
// any
let foo;

// foo、bar 均为 any
function func(foo, bar){}
````

以上的函数声明在 tsconfig 中启用了 noImplicitAny 时会报错，你可以显式为这两个参数指定 any 类型，或者暂时关闭这一配置（不推荐）。   

any 类型的变量几乎无所不能，它可以在声明后再次接受任意类型的值，同时可以被赋值给任意其它类型的变量：   

```ts
// 被标记为 any 类型的变量可以拥有任意类型的值
let anyVar: any = 'wangxiaobai';

anyVar = false;
anyVar = 'wangxiaobai';
anyVar = {
site: 'github.io'
};

anyVar = () => { }

// 标记为具体类型的变量也可以接受任何 any 类型的值
const val1: string = anyVar;
const val2: number = anyVar;
const val3: () => {} = anyVar;
const val4: {} = anyVar;
```

可以在 any 类型变量上任意地进行操作，包括赋值、访问、方法调用等等，此时可以认为类型推导与检查是被完全禁用的：   

```ts
let anyVar: any = null;

anyVar.foo.bar.baz();
anyVar[0][1][2].prop1;
```

any 类型的主要意义，是为了表示一个无拘无束的“任意类型”，它能兼容所有类型，也能够被所有类型兼容。    

无论什么时候，你都可以使用 any 类型跳过类型检查。当然，运行时出了问题就需要你自己负责了。   

any 的本质是类型系统中的顶级类型，即 Top Type。  

any 类型的万能性也导致经常滥用它，此时的 TypeScript 就变成了令人诟病的 AnyScript。为了避免这一情况，记住以下使用小 tips：   

* 如果是类型不兼容报错导致你使用 any，考虑用类型断言替代。  
* 如果是类型太复杂导致不想全部声明而使用 any，考虑将这一处的类型去断言为你需要的最简类型。  
* 如果你是想表达一个未知类型，更合理的方式是使用 unknown。   

#### unknown 类型和

unknown 类型代表未知类型，这个类型的变量可以再次赋值为任意其它类型，但只能赋值给 any 与 unknown 类型的变量：   

```ts
let unknownVar: unknown = 'wangxiaobai';

unknownVar = false;
unknownVar = 'wangxiaobai';
unknownVar = {
site: 'github.io'
};

unknownVar = () => { }

const val1: string = unknownVar; // Error
const val2: number = unknownVar; // Error
const val3: () => {} = unknownVar; // Error
const val4: {} = unknownVar; // Error

const val5: any = unknownVar;
const val6: unknown = unknownVar;
```

unknown 和 any 的一个主要差异在赋值给别的变量时，any 就像是 “我身化万千无处不在”，所有类型都把它当自己人。     

而 unknown 就像是 “我虽然身化万千，但我坚信我在未来的某一刻会得到一个确定的类型”，只有 any 和 unknown 自己把它当自己人。      

简单地说，any 放弃了所有的类型检查，而 unknown 并没有。这一点也体现在对 unknown 类型的变量进行属性访问时：     

```ts
let unknownVar: unknown;

unknownVar.foo(); // 报错：对象类型为 unknown
```

要对 unknown 类型进行属性访问，需要进行类型断言，即“虽然这是一个未知的类型，但我跟你保证它在这里就是这个类型！”：     

```ts
let unknownVar: unknown;

(unknownVar as { foo: () => {} }).foo();
```

在类型未知的情况下，推荐使用 unknown 标注。这相当于你使用额外的心智负担保证了类型在各处的结构，后续重构为具体类型时也可以获得最初始的类型信息，同时还保证了类型检查的存在。    

#### never 类型

```ts
type UnionWithNever = 'wangxiaobai' | 18 | true | void | never;  
```

将鼠标悬浮在类型别名之上，你会发现这里显示的类型是 "wangxiaobai" | 18 | true | void。   

never 类型被直接无视掉了，而 void 仍然存在。这是因为，void 作为类型表示一个空类型，就像没有返回值的函数使用 void 来作为返回值类型标注一样，void 类型就像 JS 中的 null 一样代表“这里有类型，但是个空类型”。   

而 never 才是一个 “什么都没有” 的类型，它甚至不包括空的类型，严格来说，never 类型不携带任何的类型信息，因此会在联合类型中被直接移除。   

void 和 never 的类型兼容性：   

```ts
declare let v1: never;
declare let v2: void;

v1 = v2; // X 类型 void 不能赋值给类型 never

v2 = v1;
```

在编程语言的类型系统中，never 类型被称为 Bottom Type，是整个类型系统层级中最底层的类型。   

和 null、undefined 一样，它是所有类型的子类型，但只有 never 类型的变量能够赋值给另一个 never 类型变量。   

它主要被类型检查所使用。但在某些情况下使用 never 确实是符合逻辑的，比如一个只负责抛出错误的函数：   

```ts
function justThrow(): never {
	throw new Error()
}
```

在类型流的分析中，一旦一个返回值类型为 never 的函数被调用，那么下方的代码都会被视为无效的代码（即无法执行到）：   

```ts
function justThrow(): never {
	throw new Error()
}

function foo (input:number){
	if(input > 1){
		justThrow();
		// 等同于 return 语句后的代码，即 Dead Code
		const name = 'wangxiaobai';
	}
}
```

#### 类型断言

类型断言能够显式告知类型检查程序当前这个变量的类型，可以进行类型分析地修正、类型。   

它其实就是一个将变量的已有类型更改为新指定类型的操作，它的基本语法是 as NewType，你可以将 any / unknown 类型断言到一个具体的类型：   

```ts
let unknownVar: unknown;

(unknownVar as { foo: () => {} }).foo();
```

还可以 as 到 any 来为所欲为，跳过所有的类型检查：   

```ts
const str: string = 'wangxiaobai';

(str as any).func().foo().prop;
```

也可以在联合类型中断言一个具体的分支：   

```ts
function foo(union: string | number) {
	if ((union as string).includes('wangxiaobai')) { }
	if ((union as number).toFixed() === '18') { }
}
```

类型断言的正确使用方式是，在 TypeScript 类型分析不正确或不符合预期时，将其断言为此处的正确类型：   

```ts
interface IFoo {
	name: string;
}

declare const obj: {
	foo: IFoo
}

const {
	foo = {} as IFoo
} = obj
```

这里从 {} 字面量类型断言为了 IFoo 类型，即为解构赋值默认值进行了预期的类型断言。当然，更严谨的方式应该是定义为 Partial<IFoo> 类型，即 IFoo 的属性均为可选的。     

除了使用 as 语法以外，也可以使用 <> 语法。它虽然书写更简洁，但效果一致。可以通过 TypeScript ESLint 提供的 consistent-type-assertions 规则来约束断言风格。  

类型断言应当是在迫不得己的情况下使用的。虽然说可以用类型断言纠正不正确的类型分析，但类型分析在大部分场景下还是可以智能地满足需求的。   

总的来说，在实际场景中，还是 as any 这一种操作更多。但这也是让你的代码编程 AnyScript 的罪魁祸首之一，请务必小心使用。   

#### 双重断言

如果在使用类型断言时，原类型与断言类型之间差异过大，TypeScript 会给你一个类型报错：   

```ts
const str: string = 'wangxiaobai';

// 从 X 类型 到 Y 类型的断言可能是错误的，blabla
(str as { handler: () => {} }).handler()
```

此时它会提醒你先断言到 unknown 类型，再断言到预期类型：    

```ts
const str: string = 'wangxiaobai';

(str as unknown as { handler: () => {} }).handler();

// 使用尖括号断言
(<{ handler: () => {} }>(<unknown>str)).handler();
```

这是因为你的断言类型和原类型的差异太大，需要先断言到一个通用的类，即 any / unknown。这一通用类型包含了所有可能的类型，因此断言到它和从它断言到另一个类型差异不大。   

#### 非空断言

非空断言其实是类型断言的简化，它使用 ! 语法，即 obj!.func()!.prop 的形式标记前面的一个声明一定是非空的（实际上就是剔除了 null 和 undefined 类型）。     

```ts
declare const foo: {
	func?: () => ({
		prop?: number | null;
	})
};

foo.func().prop.toFixed();
```

此时，func 在 foo 中不一定存在，prop 在 func 调用结果中不一定存在，且可能为 null，会收获两个类型报错。如果坚持调用，想要解决掉类型报错就可以使用非空断言：    

```ts
foo.func!().prop!.toFixed();
```

其应用位置类似于可选链：   

```ts
foo.func?.().prop?.toFixed();
```

不同的是，非空断言的运行时仍然会保持调用链，因此在运行时可能会报错。而可选链则会在某一个部分收到 undefined 或 null 时直接短路掉，不会再发生后面的调用。   

非空断言的常见场景还有 document.querySelector、Array.find 方法等：   

```ts
const element = document.querySelector('#id')!;
const target = [1, 2, 3, 18].find(item => item === 18)!;
```

上面的非空断言实际上等价于以下的类型断言操作：   

```ts
((foo.func as () => ({
prop?: number;
}))().prop as number).toFixed();
```

非空断言是不是简单多了？可以通过 non-nullable-type-assertion-style 规则来检查代码中是否存在类型断言能够被简写为非空断言的情况。    

类型断言还有一种用法是作为代码提示的辅助工具，比如对于以下这个稍微复杂的接口：    

```ts
interface IStruct {
	foo: string;
	bar: {
		barPropA: string;
		barPropB: number;
		barMethod: () => void;
		baz: {
			handler: () => Promise<void>;
		};
	};
}
```

假设你想要基于这个结构随便实现一个对象，你可能会使用类型标注：   

```ts
const obj: IStruct = {};
```

这个时候等待你的是一堆类型报错，你必须规规矩矩地实现整个接口结构才可以。但如果使用类型断言，就可以在保留类型提示的前提下，不那么完整地实现这个结构：   

```ts
// 这个例子是不会报错的
const obj = <IStruct>{
	bar: {
		baz: {},
	},
};
```

类型提示仍然存在：

<img src="https://oweqian.oss-cn-hangzhou.aliyuncs.com/ts/img_02.png" alt="" width="700" />  

在你错误地实现结构时仍然可以给到你报错信息：

<img src="https://oweqian.oss-cn-hangzhou.aliyuncs.com/ts/img_03.png" alt="" width="700" />  

## 类型工具

类型工具就是对类型进行处理的工具，分为类型创建与类型安全保护两类。  

### 类型创建  

基于已有的类型创建新的类型，这些类型工具包括类型别名、交叉类型、索引类型与映射类型。   

#### 类型别名

对一组类型或一个特定类型结构进行封装，以便于在其它地方进行复用。   

使用 type 关键字进行声明：  

```ts
type A = string;
```

抽离一组联合类型：  

```ts
type StatusCode = 200 | 301 | 400 | 500 | 502;
type PossibleDataTypes = string | number | (() => unknown);

const status: StatusCode = 502;
```

抽离一个函数类型：  

```ts
type Handler = (e: Event) => void;

const clickHandler: Handler = (e) => { };
const moveHandler: Handler = (e) => { };
const dragHandler: Handler = (e) => { };
```

声明一个对象类型，就像接口那样：   

```ts
type ObjType = {
	name: string;
	age: number;
}
```

在类型别名中，类型别名还可以声明自己能够接受泛型。一旦接受了泛型，它就叫工具类型：  

```ts
type Factory<T> = T | number | string;
```

它的基本功能仍然是创建类型，基于传入的泛型进行各种类型操作，得到一个新的类型。    

```ts
const foo: Factory<boolean> = true;
```

一般不会直接使用工具类型来做类型标注，而是再度声明一个新的类型别名：  

```ts
type FactoryWithBool = Factory<boolean>;

const foo: FactoryWithBool = true;
```

泛型参数的名称（上面的 T ）也不是固定的。通常使用大写的 T / K / U / V / M / O ...这种形式。  

声明一个简单、有实际意义的工具类型：  

```ts
type MaybeNull<T> = T | null;
```

这个工具类型会接受一个类型，并返回一个包括 null 的联合类型。这样一来，在实际使用时就可以确保你处理了可能为空值的属性读取与方法调用：   

```ts
type MaybeNull<T> = T | null;

function process(input: MaybeNull<{ handler: () => {} }>) {
  input?.handler();
}
```

类似的还有 MaybePromise、MaybeArray。  

```ts
type MaybeArray<T> = T | T[];

function ensureArray<T>(input: MaybeArray<T>): T[] {
  return Array.isArray(input) ? input : [input];
}
```

另外，类型别名中可以接受任意个泛型，以及为泛型指定约束、默认值等。    

#### 交叉类型 

它和联合类型的使用位置一样，只不过符号是 &，即按位与运算符。   

你需要符合这里的所有类型，才可以说实现了这个交叉类型，即 A & B，需要同时满足 A 与 B 两个类型才行。   

声明一个交叉类型：   

```ts
interface NameStruct {
  name: string;
}

interface AgeStruct {
  age: number;
}

type ProfileStruct = NameStruct & AgeStruct;

const profile: ProfileStruct = {
  name: 'wangxiaobai',
  age: 18
}
```

ProfileStruct 是一个同时包含 NameStruct 和 AgeStruct 两个接口所有属性的类型。  

```ts
type StrAndNum = string & number; // never
```

原始类型的合并变成了 never。实际上，这也是 never 这一 BottomType 的实际意义之一，描述根本不存在的类型。   

对于对象类型的交叉类型，其内部的同名属性类型同样会按照交叉类型进行合并：   

```ts
type Struct1 = {
  primitiveProp: string;
  objectProp: {
    name: string;
  }
}

type Struct2 = {
  primitiveProp: number;
  objectProp: {
    age: number;
  }
}

type Composed = Struct1 & Struct2;

type PrimitivePropType = Composed['primitiveProp']; // never
type ObjectPropType = Composed['objectProp']; // { name: string; age: number; }
```

两个联合类型组成的交叉类型，各实现两边联合类型中的一个就行了，也就是两边联合类型的交集：   

```ts
type UnionIntersection1 = (1 | 2 | 3) & (1 | 2); // 1 | 2
type UnionIntersection2 = (string | number | symbol) & string; // string
```

#### 索引类型

索引类型包含三个部分：索引签名类型、索引类型查询与索引类型访问。  

##### 索引类型签名

指的是在接口或类型别名中，通过以下语法来快速声明一个键值类型一致的类型结构：   

```ts
interface AllStringTypes {
  [key: string]: string;
}

type AllStringTypes = {
  [key: string]: string;
}
```

即使你还没声明具体的属性，对于这些类型结构的属性访问也将全部被视为 string 类型：   

```ts
interface AllStringTypes {
  [key: string]: string;
}

type PropType1 = AllStringTypes['wangxiaobai']; // string
type PropType2 = AllStringTypes['18']; // string
```

这也意味着在实现这个类型结构的变量中只能声明字符串类型的键：   

```ts
interface AllStringTypes {
  [key: string]: string;
}

const foo: AllStringTypes = {
  wangxiaobai: '18'
}
```

索引签名类型也可以和具体的键值对类型声明并存，但这时这些具体的键值类型也需要符合索引签名类型的声明：   

```ts
interface AllStringTypes {
	// 类型“number”的属性“propA”不能赋给“string”索引类型“boolean”。
	propA: number;
	[key: string]: boolean;
}
```

这里的符合即指子类型，因此自然也包括联合类型：    

```ts
interface StringOrBooleanTypes {
	propA: number;
	propB: boolean;
	[key: string]: number | boolean;
}
```

索引签名类型的一个常见场景是在重构 JavaScript 代码时，为内部属性较多的对象声明一个 any 的索引签名类型，以此来暂时支持对类型未明确属性的访问，并在后续一点点补全类型：   

```ts
interface AnyTypeHere {
	[key: string]: any;
}

const foo: AnyTypeHere['wangxiaobai'] = 'any value';
```

##### 索引类型查询  

索引类型查询，也就是 keyof 操作符。它可以将对象中的所有键转换为对应字面量类型，然后再组合成联合类型。   

```ts
interface Foo {
  wangxiaobai: 1,
  18: 2
}

type FooKeys = keyof Foo; // "wangxiaobai" | '18'
```

##### 索引类型访问

在 Typescript 中可以通过类似 obj[expression] 的方式来动态访问一个对象属性，只不过这里的 expression 要换成类型。   

```ts
interface NumberRecord {
  [key: string]: number;
}

type PropType = NumberRecord[string]; // number
```

更直观的例子是通过字面量类型来进行索引类型访问：   

```ts
interface Foo {
  propA: number;
  propB: boolean;
}

type PropAType = Foo['propA']; // number
type PropBType = Foo['propB']; // boolean
```

这里的 'propA' 和 'propB' 都是字符串字面量类型，而不是一个 JavaScript 字符串值。索引类型查询的本质其实就是，通过键的字面量类型（'propA'）访问这个键对应的键值类型（number）。     

```ts
interface Foo {
  propA: number;
  propB: boolean;
  propC: string;
}

type PropTypeUnion = Foo[keyof Foo]; // string | number | boolean
```

使用字面量联合类型进行索引类型访问时，其结果就是将联合类型每个分支对应的类型进行访问后的结果，重新组装成联合类型。   

#### 映射类型

映射类型的主要作用即是基于键名映射到键值类型。   

```ts
type Stringify<T> = {
  [K in keyof T]: string;
};
```

这个工具类型会接受一个对象类型，使用 keyof 获得这个对象类型的键名组成字面量联合类型，然后通过映射类型（即这里的 in 关键字）将这个联合类型的每一个成员映射出来，并将其键值类型设置为 string。   

```ts
interface Foo {
  prop1: string;
  prop2: number;
  prop3: boolean;
  prop4: () => void;
}

type StringifiedFoo = Stringify<Foo>;

// 等价于
interface StringifiedFoo {
  prop1: string;
  prop2: string;
  prop3: string;
  prop4: string;
}
```

键值类型也能拿到：  

```ts
type Clone<T> = {
  [K in keyof T]: T[K];
};
```

这里的 T[K] 其实就是上面说到的索引类型访问，使用键的字面量类型访问到了键值的类型，这里就相当于克隆了一个接口。   

这里只有 K in 属于映射类型的语法，keyof T 属于 keyof 操作符，[K in keyof T] 的 [] 属于索引签名类型，T[K] 属于索引类型访问。  

### 类型安全  

#### 类型查询操作符：typeof 

TypeScript 新增了用于类型查询的 typeof ，即 Type Query Operator，这个 typeof 返回的是一个类型：   

```ts
const str = 'wangxiaobai';

const obj = { name: 'wangxiaobai' };

const nullVar = null;
const undefinedVar = undefined;

const func = (input: string) => {
  return input.length > 10;
}

type Str = typeof str; // "wangxiaobai"
type Obj = typeof obj; // { name: string; }
type Null = typeof nullVar; // null
type Undefined = typeof undefined; // undefined
type Func = typeof func; // (input: string) => boolean
```

可以直接在类型标注中使用 typeof，也可以在工具类型中使用 typeof。   

```ts
const func = (input: string) => {
  return input.length > 10;
}

const func2: typeof func = (name: string) => {
  return name === 'wangxiaobai'
}
```

大部分情况下，typeof 返回的类型就是当你把鼠标悬浮在变量名上时出现的推导后的类型，并且是最窄的推导程度（即到字面量类型的级别）。   

为了更好地避免这种情况，也就是隔离类型层和逻辑层，类型查询操作符后是不允许使用表达式的：    

```ts
const isInputValid = (input: string) => {
  return input.length > 10;
}

// 不允许表达式
let isValid: typeof isInputValid('wangxiaobai');

```

#### 类型守卫

TypeScript 提供了非常强大的类型推导能力，它会随着你的代码逻辑不断尝试收窄类型，这一能力称之为类型的控制流分析（也可以简单理解为类型推导）。    

```ts
function foo (input: string | number) {
  if(typeof input === 'string') {}
  if(typeof input === 'number') {}
  // ...
}
```

在类型控制流分析下，每流过一个 if 分支，后续联合类型的分支就少了一个，因为这个类型已经在这个分支处理过了，不会进入下一个分支：    

```ts
declare const strOrNumOrBool: string | number | boolean;

if (typeof strOrNumOrBool === 'string') {
  // 一定是字符串！
  strOrNumOrBool.charAt(1);
} else if (typeof strOrNumOrBool === 'number') {
  // 一定是数字！
  strOrNumOrBool.toFixed();
} else if (typeof strOrNumOrBool === 'boolean') {
  // 一定是布尔值！
  strOrNumOrBool === true;
} else {
  // 要是走到这里就说明有问题！
  const _exhaustiveCheck: never = strOrNumOrBool;
  throw new Error(`Unknown input type: ${_exhaustiveCheck}`);
}
```

这里实际上通过 if 条件中的表达式进行了类型保护，即告知了流过这里的分析程序每个 if 语句代码块中变量会是何类型。   

这是编程语言类型能力中最重要的一部分：与实际逻辑紧密关联的类型，再反过来让类型为逻辑保驾护航。  

如果 if 条件中的表达式被提取出来了会发生什么情况？    

```ts
function isString(input: unknown): boolean {
  return typeof input === 'string';
}

function foo(input: string | number) {
  if (isString(input)) {
    // 类型“string | number”上不存在属性“replace”。
    (input).replace('wangxiaobai', 'wangxiaobai18')
  }
  if (typeof input === 'number') { }
  // ...
}
```

奇怪的事情发生了，只是把逻辑提取到了外面而已，如果 isString 返回了 true，那 input 肯定也是 string 类型啊？    

想象类型控制流分析，刚流进 if (isString(input)) 就戛然而止了。因为 isString 这个函数在另外一个地方，内部的判断逻辑并不在函数 foo 中。这里的类型控制流分析做不到跨函数上下文来进行类型的信息收集。     

##### 基于 is 的类型保护

将判断逻辑封装起来提取到函数外部进行复用很常见。为了解决这一类型控制流分析的能力不足， TypeScript 引入了 is 关键字来显式地提供类型信息：    

```ts
function isString(input: unknown): input is string {
  return typeof input === 'string';
}

function foo(input: string | number) {
  if (isString(input)) {
    // 正确了
    (input).replace('wangxiaobai', 'wangxiaobai18')
  }
  if (typeof input === 'number') { }
  // ...
}
```

isString 函数称为类型守卫，在它的返回值中不再使用 boolean 作为类型标注，而是使用 input is string 这么个奇怪的搭配：   

* input: 函数的某个参数。   
* is string: 即 is 关键字 + 预期类型，即如果这个函数成功返回为 true，那么 is 关键字前这个入参的类型，就会被这个类型守卫调用方后续的类型控制流分析收集到。   

但类型守卫函数中并不会对判断逻辑和实际类型的关联进行检查：   

```ts
function isString(input: unknown): input is number {
  return typeof input === 'string';
}

function foo(input: string | number) {
  if (isString(input)) {
    // 报错，在这里变成了 number 类型
    (input).replace('wangxiaobai', 'wangxiaobai18')
  }
  if (typeof input === 'number') { }
  // ...
}
```

类型守卫有些类似类型断言，但类型守卫更宽容，也更信任你一些。你指定什么类型，它就是什么类型。   

除了使用简单的原始类型以外，还可以在类型守卫中使用对象类型、联合类型等：        

```ts
export type Falsy = false | '' | 0 | null | undefined;

export const isFalsy = (val: unknown): val is Falsy => !val;

// 不包括不常用的 symbol 和 bigint
export type Primitive = string | number | boolean | undefined;

export const isPrimitive = (val: unknown): val is Primitive => ['string', 'number', 'boolean' , 'undefined'].includes(typeof val);
```

##### 基于 in 与 instanceof 的类型保护

Typescript 中的 in 操作符，可以通过 key in object 的方式来判断 key 是否存在于 object 或其原型链上（返回 true 说明存在）。    

```ts
interface Foo {
  foo: string;
  fooOnly: boolean;
  shared: number;
}

interface Bar {
  bar: string;
  barOnly: boolean;
  shared: number;
}

function handle(input: Foo | Bar) {
  if ('foo' in input) {
    input.fooOnly;
  } else {
    input.barOnly;
  }
}
```

这里的 foo / bar、fooOnly / barOnly、shared 属性们其实有着不同的意义。   

使用 foo 和 bar 来区分 input 联合类型，然后就可以在对应的分支代码块中正确访问到 Foo 和 Bar 独有的类型 fooOnly / barOnly。   

但是，如果用 shared 来区分，就会发现在分支代码块中 input 仍然是初始的联合类型：   

```ts
function handle(input: Foo | Bar) {
  if ('shared' in input) {
    // 类型“Foo | Bar”上不存在属性“fooOnly”。类型“Bar”上不存在属性“fooOnly”。
    input.fooOnly;
  } else {
    // 类型“never”上不存在属性“barOnly”。
    input.barOnly;
  }
}
```

Foo 与 Bar 都满足 'shared' in input 这个条件。因此在 if 分支中， Foo 与 Bar 都会被保留，那在 else 分支中就只剩下 never 类型。    

可辨识属性可以是结构层面的，比如结构 A 的属性 prop 是数组，而结构 B 的属性 prop 是对象，或者结构 A 中存在属性 prop 而结构 B 中不存在。    

它甚至可以是共同属性的字面量类型差异：

```ts
function ensureArray(input: number | number[]): number[] {
  if (Array.isArray(input)) {
    return input;
  } else {
    return [input];
  }
}

interface Foo {
  kind: 'foo';
  diffType: string;
  fooOnly: boolean;
  shared: number;
}

interface Bar {
  kind: 'bar';
  diffType: number;
  barOnly: boolean;
  shared: number;
}

function handle1(input: Foo | Bar) {
  if (input.kind === 'foo') {
    input.fooOnly;
  } else {
    input.barOnly;
  }
}
```

对于同名但不同类型的属性，需要使用字面量类型的区分，并不能使用简单的 typeof：    

```ts
function handle2(input: Foo | Bar) {
  // 报错，并没有起到区分的作用，在两个代码块中都是 Foo | Bar
  if (typeof input.diffType === 'string') {
    input.fooOnly;
  } else {
    input.barOnly;
  }
}
```

Typescript 中的 instanceof，判断的是原型级别的关系，如 foo instanceof Base 会沿着 foo 的原型链查找 Base.prototype 是否存在其上。   

```ts
class FooBase {}

class BarBase {}

class Foo extends FooBase {
  fooOnly() {}
}
class Bar extends BarBase {
  barOnly() {}
}

function handle(input: Foo | Bar) {
  if (input instanceof FooBase) {
    input.fooOnly();
  } else {
    input.barOnly();
  }
}
```

#### 类型断言守卫

断言守卫和类型守卫最大的不同点在于，在判断条件不通过时，断言守卫需要抛出一个错误，类型守卫只需要剔除掉预期的类型。    

这里的抛出错误可能让你想到了 never 类型，但实际情况要更复杂一些，断言守卫并不会始终都抛出错误，所以它的返回值类型并不能简单地使用 never 类型。    

为此，TypeScript 3.7 版本引入了 asserts 关键字来进行断言场景下的类型守卫：   

```ts
let name: any = 'wangxiaobai';

function assertIsNumber(val: any): asserts val is number {
  if (typeof val !== 'number') {
    throw new Error('Not a number!');
  }
}

assertIsNumber(name);

// number 类型！
name.toFixed();
```

这种情况下无需再为断言守卫传入一个表达式，而是可以将这个判断用的表达式放进断言守卫的内部，来获得更独立地代码逻辑。   

## 泛型  

TypeScript 是一门对类型进行编程的语言，泛型就是这门语言里的（函数）参数。  

### 类型别名中的泛型  

类型别名如果声明了泛型坑位，它就等价于一个接受参数的函数：  

```ts
type Factory<T> = T | number | string;
```

这个类型别名的本质就是一个函数，T 就是它的变量，返回值则是一个包含 T 的联合类型。   

类型别名中的泛型大多是用来进行工具类型封装。   

```ts
type Stringify<T> = {
  [K in keyof T]: string;
};

type Clone<T> = {
  [K in keyof T]: T[K];
};
```

Stringify 会将一个对象类型的所有属性类型置为 string ，而 Clone 则会进行类型的完全复制。   

```ts
type Partial<T> = {
    [P in keyof T]?: T[P];
};
```

工具类型 Partial 会将传入的对象类型复制一份，但会额外添加一个?，它会将所有属性转变为可选属性。  

```ts
interface IFoo {
  prop1: string;
  prop2: number;
  prop3: boolean;
  prop4: () => void;
}

type PartialIFoo = Partial<IFoo>;

// 等价于
interface PartialIFoo {
  prop1?: string;
  prop2?: number;
  prop3?: boolean;
  prop4?: () => void;
}
```

类型别名与泛型的结合中，还有一个非常重要的工具：条件类型。   

```ts
type IsEqual<T> = T extends true ? 1 : 2;

type A = IsEqual<true>; // 1
type B = IsEqual<false>; // 2
type C = IsEqual<'wangxiaobai'>; // 2
```

在条件类型参与的情况下，通常泛型会被作为条件类型中的判断条件（T extends Condition，或者 Type extends T）以及返回值（即 : 两端的值），这也是筛选类型需要依赖的能力之一。   

#### 泛型约束与默认值

泛型同样有着默认值的设定。  

```ts
type Factory<T = boolean> = T | number | string;
```

在你调用时可以不带任何参数，默认会使用声明的默认值来填充。   

```ts
const foo: Factory = false;
```

泛型还能做到函数参数做不到的事：泛型约束，可以要求传入这个工具类型的泛型必须符合某些条件，否则就拒绝进行后面的逻辑。     

使用 extends 关键字来约束传入的泛型参数必须符合要求。关于 extends，A extends B 意味着 A 是 B 的子类型，也就是说 A 比 B 的类型更精确，或者说更复杂。   

* 更精确：如字面量类型是对应原始类型的子类型，即 'wangxiaobai' extends string，18 extends number 成立。类似的，联合类型子集均为联合类型的子类型，即 1、 1 | 2 是 1 | 2 | 3 | 4 的子类型。   
* 更复杂，如 { name: string } 是 {} 的子类型，因为在 {} 的基础上增加了额外的类型，基类与派生类（父类与子类）同理。  

```ts
type ResStatus<ResCode extends number> = ResCode extends 10000 | 10001 | 10002
  ? 'success'
  : 'failure';
```

这个例子会根据传入的请求码判断请求是否成功，这意味着它只能处理数字字面量类型的参数，因此这里通过 extends number 来标明其类型约束，如果传入一个不合法的值，就会出现类型错误：  

```ts
type ResStatus<ResCode extends number> = ResCode extends 10000 | 10001 | 10002
  ? 'success'
  : 'failure';


type Res1 = ResStatus<10000>; // "success"
type Res2 = ResStatus<20000>; // "failure"

type Res3 = ResStatus<'10000'>; // 类型“string”不满足约束“number”。
```

如果想让这个类型别名可以无需显式传入泛型参数也能调用，并且默认情况下是成功地，这样就可以为这个泛型参数声明一个默认值：  

```ts
type ResStatus<ResCode extends number = 10000> = ResCode extends 10000 | 10001 | 10002
  ? 'success'
  : 'failure';

type Res4 = ResStatus; // "success"
```

#### 多泛型关联

不仅可以同时传入多个泛型参数，还可以让这几个泛型参数之间也存在联系。   

```ts
type Conditional<Type, Condition, TruthyResult, FalsyResult> =
  Type extends Condition ? TruthyResult : FalsyResult;

//  "passed!"
type Result1 = Conditional<'wangxiaobai', string, 'passed!', 'rejected!'>;

// "rejected!"
type Result2 = Conditional<'wangxiaobai', boolean, 'passed!', 'rejected!'>;
```

多泛型参数其实就像接受更多参数的函数，其内部的运行逻辑（类型操作）会更加抽象，表现在参数（泛型参数）需要进行的逻辑运算（类型操作）会更加复杂。   

多个泛型参数之间的依赖，其实指的即是在后续泛型参数中，使用前面的泛型参数作为约束或默认值：   

```ts
type ProcessInput<
  Input,
  SecondInput extends Input = Input,
  ThirdInput extends Input = SecondInput
> = number;
```

* 这个工具类型接受 1-3 个泛型参数。   
* 第二、三个泛型参数的类型需要是首个泛型参数的子类型。   
* 当只传入一个泛型参数时，其第二个泛型参数会被赋值为此参数，而第三个则会赋值为第二个泛型参数，相当于均使用了这唯一传入的泛型参数。   
* 当传入两个泛型参数时，第三个泛型参数会默认赋值为第二个泛型参数的值。   

### 对象类型中的泛型

```ts
interface IRes<TData = unknown> {
  code: number;
  error?: string;
  data: TData;
}
```

这个接口描述了一个通用的响应类型结构，预留出了实际响应数据的泛型坑位，然后在你的请求函数中就可以传入特定的响应类型了：  

```ts
interface IUserProfileRes {
  name: string;
  homepage: string;
  avatar: string;
}

function fetchUserProfile(): Promise<IRes<IUserProfileRes>> {}

type StatusSucceed = boolean;
function handleOperation(): Promise<IRes<StatusSucceed>> {}
```

泛型嵌套的场景也非常常用，比如对存在分页结构的数据，可以将其分页的响应结构抽离出来：   

```ts
interface IPaginationRes<TItem = unknown> {
  data: TItem[];
  page: number;
  totalCount: number;
  hasNextPage: boolean;
}

function fetchUserProfileList(): Promise<IRes<IPaginationRes<IUserProfileRes>>> {}
```

### 函数中的泛型

假设有这么一个函数，它可以接受多个类型的参数并进行对应处理，比如：   

* 对于字符串，返回部分截取。     
* 对于数字，返回它的 n 倍。    
* 对于对象，修改它的属性并返回。    

这个时候要怎么对函数进行类型声明？是 any 大法好？   

```ts
function handle(input: any): any {}
```

还是用联合类型来包括所有可能类型？   

```ts
function handle(input: string | number | {}): string | number | {} {}
```

第一种肯定要直接 pass，第二种虽然麻烦了一点，但似乎可以满足需要？   

但如果真的调用一下就知道不合适了。   

```ts
const shouldBeString = handle('wangxiaobai');
const shouldBeNumber = handle(18);
const shouldBeObject = handle({ name: 'wangxiaobai' });
```

虽然约束了入参的类型，但返回值的类型并没有像预期的那样和入参关联起来，上面三个调用结果的类型仍然是一个宽泛的联合类型 string | number | {}。    

难道要用重载一个个声明可能的关联关系？   

```ts
function handle(input: string): string
function handle(input: number): number
function handle(input: {}): {}
function handle(input: string | number | {}): string | number | {} { }
```

如果再多一些复杂的情况，别说你愿不愿意补充每一种关联了，同事看到这样的代码都会质疑你的水平。   

这个时候就该请出泛型了：   

```ts
function handle<T>(input: T): T {}
```

为函数声明一个泛型参数 T，并将参数的类型与返回值类型指向这个泛型参数。这样，在这个函数接收到参数时，T 会自动地被填充为这个参数的类型。   

这也就意味着你不再需要预先确定参数的可能类型了，而在返回值与参数类型关联的情况下，也可以通过泛型参数来进行运算。   

在基于参数类型进行填充泛型时，其类型信息会被推断到尽可能精确的程度，如这里会推导到字面量类型而不是基础类型。这是因为在直接传入一个值时，这个值是不会再被修改的，因此可以推导到最精确的程度。而如果你使用一个变量作为参数，那么只会使用这个变量标注的类型（在没有标注时，会使用推导出的类型）。    

```ts
function handle<T>(input: T): T {}

const author = 'wangxiaobai'; // 使用 const 声明，被推导为 'wangxiaobai'

let authorAge = 18; // 使用 let 声明，被推导为 number

handle(author); // 填充为字面量类型 'wangxiaobai'
handle(authorAge); // 填充为基础类型 number
```

你也可以将鼠标悬浮在表达式上，来查看填充的泛型信息。    

再看一个例子：

```ts
function swap<T, U>([start, end]: [T, U]): [U, T] {
	return [end, start];
}

const swapped1 = swap(['wangxiaobai', 18]);
const swapped2 = swap([null, 18]);
const swapped3 = swap([{ name: 'wangxiaobai' }, {}]);
```

在这里返回值类型对泛型参数进行了一些操作，而同样可以看到其调用信息符合预期。    

函数中的泛型同样存在约束与默认值，比如上面的 handle 函数，现在希望做一些代码拆分，不再处理对象类型的情况了：    

```ts
function handle<T extends string | number>(input: T): T {}
```

而 swap 函数，现在只想处理数字元组的情况：   

```ts
function swap<T extends number, U extends number>([start, end]: [T, U]): [U, T] {
	return [end, start];
}
```

而多泛型关联也是如此，比如 lodash 的 pick 函数，这个函数首先接受一个对象，然后接受一个对象属性名组成的数组，并从这个对象中截取选择的属性部分：   

```ts
const object = { 'a': 1, 'b': '2', 'c': 3 };

_.pick(object, ['a', 'c']);
// => { 'a': 1, 'c': 3 }
```

这个函数很明显需要在泛型层面声明关联，即数组中的元素只能来自于对象的属性名（组成的字面量联合类型！），因此可以这么写（部分简化）：   

```ts
pick<T extends object, U extends keyof T>(object: T, ...props: Array<U>): Pick<T, U>;
```

这里 T 声明约束为对象类型，而 U 声明约束为 keyof T。同时对应的其返回值类型中使用了 Pick<T, U> 这一工具类型，它与 pick 函数的作用一致，对一个对象结构进行裁剪。   

函数的泛型参数也会被内部的逻辑消费，如：   

```ts
function handle<T>(payload: T): Promise<[T]> {
	return new Promise<[T]>((res, rej) => {
		res([payload]);
	});
}
```

箭头函数的泛型，其书写方式是这样的：    

```ts
const handle = <T>(input: T): T => {}
```

需要注意的是在 tsx 文件中泛型的尖括号可能会造成报错，编译器无法识别这是一个组件还是一个泛型，此时你可以让它长得更像泛型一些：    

```ts
const handle = <T extends any>(input: T): T => {}
```

函数的泛型是日常使用较多的一部分，更明显地体现了泛型在调用时被填充这一特性，而类型别名中更多是手动传入泛型。这一差异的缘由其实就是它们的场景不同，通常使用类型别名来对已经确定的类型结构进行类型操作，比如将一组确定的类型放置在一起。而在函数这种场景中并不能确定泛型在实际运行时会被什么样的类型填充。    

### Class 中的泛型

Class 中的泛型消费方是属性、方法乃至装饰器等。同时 Class 内的方法还可以再声明自己独有的泛型参数。     

```ts
class Queue<TElementType> {
  private _list: TElementType[];

  constructor(initial: TElementType[]) {
    this._list = initial;
  }

  // 入队一个队列泛型子类型的元素
  enqueue<TType extends TElementType>(ele: TType): TElementType[] {
    this._list.push(ele);
    return this._list;
  }

  // 入队一个任意类型元素（无需为队列泛型子类型）
  enqueueWithUnknownType<TType>(element: TType): (TElementType | TType)[] {
    return [...this._list, element];
  }

  // 出队
  dequeue(): TElementType[] {
    this._list.shift();
    return this._list;
  }
}
```

### 内置方法中的泛型  

TypeScript 中为非常多的内置对象都预留了泛型坑位，如 Promise 中。   

```ts
function p() {
  return new Promise<boolean>((resolve, reject) => {
    resolve(true);
  });
}
```

数组 Array<T> 中，其泛型参数代表数组的元素类型，几乎贯穿所有的数组方法。   

```ts
const arr: Array<number> = [1, 2, 3];

// 类型“string”的参数不能赋给类型“number”的参数。
arr.push('wangxiaobai');
// 类型“string”的参数不能赋给类型“number”的参数。
arr.includes('wangxiaobai');

// number | undefined
arr.find(() => false);

// 第一种 reduce
arr.reduce((prev, curr, idx, arr) => {
  return prev;
}, 1);

// 第二种 reduce
// 报错：不能将 number 类型的值赋值给 never 类型
arr.reduce((prev, curr, idx, arr) => {
  return [...prev, curr]
}, []);
```

reduce 方法是相对特殊的一个，它的类型声明存在几种不同的重载：   

* 当你不传入初始值时，泛型参数会从数组的元素类型中进行填充。   
* 当你传入初始值时，如果初始值的类型与数组元素类型一致，则使用数组的元素类型进行填充。即这里第一个 reduce 调用。   
* 当你传入一个数组类型的初始值，比如这里的第二个 reduce 调用，reduce 的泛型参数会默认从这个初始值推导出的类型进行填充，如这里是 never[]。   

第三种情况也就意味着信息不足，无法推导出正确的类型，此时可以手动传入泛型参数来解决：   

```ts
arr.reduce<number[]>((prev, curr, idx, arr) => {
  return prev;
}, []);
```

React 中同样可以找到无处不在的泛型坑位：   

```ts
const [state, setState] = useState<number[]>([]);
// 不传入默认值，则类型为 number[] | undefined
const [state, setState] = useState<number[]>();

// 体现在 ref.current 上
const ref = useRef<number>();

const context =  createContext<ContextType>({});
```

## 类型系统

在 TypeScript 中，你可能遇见过以下这样 “看起来不太对，但竟然能正常运行” 的代码：   

```ts
class Cat {
  eat() { }
}

class Dog {
  eat() { }
}

function feedCat(cat: Cat) { }

feedCat(new Dog())
```

这就是 TypeScript 的类型系统特性：结构化类型系统，还存在另一种类型系统：标称类型系统。     

### 结构化类型系统

如果为 Cat 类新增一个独特的方法，这个时候的表现才是符合预期的，即只能用真实的 Cat 类来进行调用：   

```ts
class Cat {
  meow() { }
  eat() { }
}

class Dog {
  eat() { }
}

function feedCat(cat: Cat) { }

// 报错！
feedCat(new Dog())
```

TypeScript 比较两个类型并非通过类型的名称（即 feedCat 函数只能通过 Cat 类型调用），而是比较这两个类型上实际拥有的属性与方法。   

最初的例子里，Cat 与 Dog 类型上的方法是一致的，所以它们虽然是两个名字不同的类型，但仍然被视为结构一致，这就是结构化类型系统的特性。   

结构类型的别称为鸭子类型（Duck Typing），这个名字来源于鸭子测试（Duck Test）。其核心理念是，如果你看到一只鸟走起来像鸭子，游泳像鸭子，叫得也像鸭子，那么这只鸟就是鸭子。      

但如果为 Dog 类型添加一个独特方法呢？   

```ts
class Cat {
  eat() { }
}

class Dog {
  bark() { }
  eat() { }
}

function feedCat(cat: Cat) { }

feedCat(new Dog())
```

这个时候为什么没有类型报错了？   

结构化类型系统认为 Dog 类型完全实现了 Cat 类型。至于额外的方法 bark，可认为是 Dog 类型继承 Cat 类型后添加的新方法，即此时 Dog 类可以被认为是 Cat 类的子类。    

更进一步，在比较对象类型的属性时，同样会采用结构化类型系统进行判断。而对结构中的函数类型（即方法）进行比较时，同样存在类型的兼容性比较：   

```ts
class Cat {
  eat(): boolean {
    return true
  }
}

class Dog {
  eat(): number {
    return 18;
  }
}

function feedCat(cat: Cat) { }

// 报错！
feedCat(new Dog())
```

这是结构化类型系统的核心理念，即基于类型结构进行判断类型兼容性。   

严格来说，鸭子类型系统和结构化类型系统并不完全一致，结构化类型系统意味着基于完全的类型结构来判断类型兼容性，而鸭子类型则只基于运行时访问的部分来决定。    

如果调用了走、游泳、叫这三个方法，那么传入的类型只需要存在这几个方法即可（而不需要类型结构完全一致）。   

由于 TypeScript 本身并不是在运行时进行类型检查，同时官方文档中同样认为这两个概念是一致的（One of TypeScript’s core principles is that type checking focuses on the shape that values have. This is sometimes called “duck typing” or “structural typing”.）。因此可以直接认为鸭子类型与结构化类型是同一概念。      

### 标称类型系统

标称类型系统（Nominal Typing System）要求两个可兼容的类型，其名称必须是完全一致的。   

```ts
type USD = number;
type CNY = number;

const CNYCount: CNY = 200;
const USDCount: USD = 200;

function addCNY(source: CNY, input: CNY) {
  return source + input;
}

addCNY(CNYCount, USDCount)
```

在结构化类型系统中，USD 与 CNY 被认为是两个完全一致的类型，因此在 addCNY 函数中可以传入 USD 类型的变量。人民币与美元这两个单位实际的意义并不一致，怎么能进行相加？   

在标称类型系统中，CNY 与 USD 被认为是两个完全不同的类型，因此能够避免这一情况发生。   

类型的重要意义之一是限制了数据的可用操作与实际意义，这一点在标称类型系统中的体现要更加明显。   

上面可以通过类型的结构，来让结构化类型系统认为两个类型具有父子类型关系，而对于标称类型系统，父子类型关系只能通过显式的继承来实现，称为标称子类型（Nominal Subtyping）。    

```ts
class Cat { }
// 实现一只短毛猫！
class ShorthairCat extends Cat { }
```

### 模拟标称类型系统

类型的重要意义之一是限制了数据的可用操作与实际意义，它是通过类型附带的额外信息来实现的（类似于元数据）。    

要在 TypeScript 中实现，其实也只需要为类型额外附加元数据即可，比如 CNY 与 USD，分别附加上它们的单位信息即可，但同时又需要保留原本的信息（即原本的 number 类型）。   

```ts
declare class TagProtector<T extends string> {
  protected __tag__: T;
}

type Nominal<T, U extends string> = T & TagProtector<U>;

type CNY = Nominal<number, 'CNY'>;

type USD = Nominal<number, 'USD'>;

const CNYCount = 100 as CNY;

const USDCount = 100 as USD;

function addCNY(source: CNY, input: CNY) {
  return (source + input) as CNY;
}

addCNY(CNYCount, CNYCount);

// 报错了！
addCNY(CNYCount, USDCount);
```

使用 TagProtector 声明了一个具有 protected 属性的类，使用它来携带额外的信息，并和原本的类型合并到一起，就得到了 Nominal 工具类型。   

这一实现方式本质上只在类型层面做了数据的处理，在运行时无法进行进一步的限制。可以从逻辑层面入手进一步确保安全性：   

```ts
class CNY {
  private __tag!: void;
  constructor(public value: number) {}
}
class USD {
  private __tag!: void;
  constructor(public value: number) {}
}
```

使用方式也要进行变化：   

```ts
const CNYCount = new CNY(100);
const USDCount = new USD(100);

function addCNY(source: CNY, input: CNY) {
  return (source.value + input.value);
}

addCNY(CNYCount, CNYCount);
// 报错了！
addCNY(CNYCount, USDCount);
```

通过这种方式，在运行时添加更多的检查逻辑，同时在类型层面也得到了保障。    

这两种方式的本质都是通过额外属性实现了类型信息的附加，从而使得结构化类型系统将结构一致的两个类型也判断为不可兼容。    

将其标记为 private / protected 其实不是必须的，只是为了避免类型信息被错误消费。    

## 类型系统层级

### 判断类型兼容性的方式 

使用条件类型来判断类型兼容性。   

```ts
type Result = 'wangxiaobai' extends string ? 1 : 2;
```

如果返回 1，则说明 'wangxiaobai' 为 string 的子类型。否则，说明不成立。但注意，不成立并不意味着 string 就是 'wangxiaobai' 的子类型了。   

还有一种通过赋值来进行兼容性检查的方式。   

```ts
declare let source: string;

declare let anyType: any;
declare let neverType: never;

anyType = source;

// 不能将类型“string”分配给类型“never”。
neverType = source;
```

对于变量 a = 变量 b，如果成立，意味着 <变量 b 的类型> extends <变量 a 的类型> 成立，即 b 类型是 a 类型的子类型，在这里即是 string extends never ，这明显是不成立的。    

### 从原始类型开始

首先从原始类型、对象类型和它们对应的字面量类型开始。   

```ts
type Result1 = 'wangxiaobai' extends string ? 1 : 2; // 1
type Result2 = 1 extends number ? 1 : 2; // 1
type Result3 = true extends boolean ? 1 : 2; // 1
type Result4 = { name: string } extends object ? 1 : 2; // 1
type Result5 = { name: 'wangxiaobai' } extends object ? 1 : 2; // 1
type Result6 = [] extends object ? 1 : 2; // 1
```

一个基础类型和它们对应的字面量类型必定存在父子类型关系。    

object 代表着所有非原始类型的类型，即数组、对象与函数类型，所以这里 Result6 成立的原因即是 [] 这个字面量类型也可以被认为是 object 的字面量类型。    

结论简记为，字面量类型 < 对应的原始类型。    

### 联合类型  

在联合类型中，只需要符合其中一个类型，就可以认为实现了这个联合类型，用条件类型表达是这样的：   

```ts
type Result7 = 1 extends 1 | 2 | 3 ? 1 : 2; // 1
type Result8 = 'wang' extends 'wang' | 'xiao' | 'bai' ? 1 : 2; // 1
type Result9 = true extends true | false ? 1 : 2; // 1
```

并不需要联合类型的所有成员均为字面量类型，或者字面量类型来自于同一基础类型这样的前提，只需要这个类型存在于联合类型中。   

对于原始类型，联合类型的比较其实也是一致的：   

```ts
type Result10 = string extends string | false | number ? 1 : 2; // 1
```

结论：字面量类型 < 包含此字面量类型的联合类型 / 原始类型 < 包含此原始类型的联合类型。    

```ts
type Result11 = 'wang' | 'xiao' | 'bai' extends string ? 1 : 2; // 1
type Result12 = {} | (() => void) | [] extends object ? 1 : 2; // 1
```

结论：同一基础类型的字面量联合类型 < 此基础类型。   

合并一下结论，去掉比较特殊的情况：字面量类型 < 包含此字面量类型的联合类型（同一基础类型） < 对应的原始类型，即：   

```ts
type Result13 = 'wangxiaobai' extends 'wangxiaobai' | '18'
  ? 'wangxiaobai' | '18' extends string
    ? 2
    : 1
  : 0;
```

### 装箱类型

string 类型会是 String 类型的子类型，String 类型会是 Object 类型的子类型，中间还有一个奇怪的 {}。   

```ts
type Result14 = string extends String ? 1 : 2; // 1
type Result15 = String extends {} ? 1 : 2; // 1
type Result16 = {} extends object ? 1 : 2; // 1
type Result18 = object extends Object ? 1 : 2; // 1
```

{} 不是 object 的字面量类型吗？为什么能在这里比较，并且 String 还是它的子类型？    

把 String 看作一个普通的对象，上面存在一些方法，如：   

```ts
interface String {
  replace: // ...
  replaceAll: // ...
  startsWith: // ...
  endsWith: // ...
  includes: // ...
}
```

这时是不是能看做 String 继承了 {} 这个空对象，然后自己实现了这些方法？当然可以！    

在结构化类型系统的比较下，String 会被认为是 {} 的子类型。这里从 string < {} < object 看起来构建了一个类型链，但实际上 string extends object 并不成立：   

```ts
type Tmp = string extends object ? 1 : 2; // 2
```

由于结构化类型系统这一特性的存在，会得到一些看起来矛盾的结论：   

```ts
type Result16 = {} extends object ? 1 : 2; // 1
type Result18 = object extends {} ? 1 : 2; // 1

type Result17 = object extends Object ? 1 : 2; // 1
type Result20 = Object extends object ? 1 : 2; // 1

type Result19 = Object extends {} ? 1 : 2; // 1
type Result21 = {} extends Object ? 1 : 2; // 1
```

16-18 和 19-21 这两对，为什么无论如何判断都成立？难道说明 {} 和 object 类型相等，也和 Object 类型一致？    

当然不，这里的 {} extends 和 extends {} 实际上是两种完全不同的比较方式。   

{} extends object 和 {} extends Object 意味着， {} 是 object 和 Object 的字面量类型，是从类型信息的层面出发的，即字面量类型在基础类型之上提供了更详细的类型信息。    

object extends {} 和 Object extends {} 则是从结构化类型系统的比较出发的，即 {} 作为一个一无所有的空对象，几乎可以被视作是所有类型的基类，万物的起源。    

如果混淆了这两种类型比较的方式，就可能会得到 string extends object 这样的错误结论。

而 object extends Object 和 Object extends object 这两者的情况就要特殊一些，它们是因为“系统设定”的问题，Object 包含了所有除 Top Type 以外的类型（基础类型、函数类型等），object 包含了所有非原始类型的类型，即数组、对象与函数类型，这就导致了你中有我、我中有你的神奇现象。     

由此得出结论：原始类型 < 原始类型对应的装箱类型 < Object 类型。    

### Top Type

类型层级的顶端只有 any 和 unknown 这两兄弟。   

Object 类型自然会是 any 与 unknown 类型的子类型。   

```ts
type Result22 = Object extends any ? 1 : 2; // 1
type Result23 = Object extends unknown ? 1 : 2; // 1
```

但如果把条件类型的两端对调一下呢？   

```ts
type Result24 = any extends Object ? 1 : 2; // 1 | 2
type Result25 = unknown extends Object ? 1 : 2; // 2
```

你会发现，any 竟然调过来，值竟然变成了 1 | 2？  

```ts
type Result26 = any extends 'wangxiaobai' ? 1 : 2; // 1 | 2
type Result27 = any extends string ? 1 : 2; // 1 | 2
type Result28 = any extends {} ? 1 : 2; // 1 | 2
type Result29 = any extends never ? 1 : 2; // 1 | 2
```

实际上，还是因为“系统设定”的原因。any 代表了任何可能的类型，当使用 any extends 时，它包含了“让条件成立的一部分”，以及“让条件不成立的一部分”。  

而从实现上说，在 TypeScript 内部代码的条件类型处理中，如果接受判断的是 any，那么会直接返回条件类型结果组成的联合类型。   

因此 any extends string 并不能简单地认为等价于以下条件类型：

```ts
type Result30 = ("I'm string!" | {}) extends string ? 1 : 2; // 2
```

这种情况下，由于联合类型的成员并非均是字符串字面量类型，条件显然不成立。   

在赋值给其他类型时，any 来者不拒，而 unknown 则只允许赋值给 unknown 类型和 any 类型，这也是由于“系统设定”的原因，即 any 可以表达为任何类型。   

你需要我赋值给这个变量？那我现在就是这个变量的子类型了，我是不是很乖巧？   

另外，any 类型和 unknown 类型的比较也是互相成立的：   

```ts
type Result31 = any extends unknown ? 1 : 2;  // 1
type Result32 = unknown extends any ? 1 : 2;  // 1
```

虽然还是存在系统设定的部分，但仍然只关注类型信息层面的层级，即结论为：Object < any / unknown。   

### never 类型

never 类型，它代表了“虚无”的类型，一个根本不存在的类型。对于这样的类型，它会是任何类型的子类型，当然也包括字面量类型：    

```ts
type Result33 = never extends 'wangxiaobai' ? 1 : 2; // 1
```

但你可能又想到了一些特别的部分，比如 null、undefined、void。

```ts
type Result34 = undefined extends 'wangxiaobai' ? 1 : 2; // 2
type Result35 = null extends 'wangxiaobai' ? 1 : 2; // 2
type Result36 = void extends 'wangxiaobai' ? 1 : 2; // 2
```

上面三种情况当然不应该成立。在 TypeScript 中，void、undefined、null 都是切实存在、有实际意义的类型，它们和 string、number、object 并没有什么本质区别。

这里得到的结论是，never < 字面量类型。     

现在可以开始组合整个类型层级了。      

### 类型层级链   

```ts
type TypeChain = never extends 'wangxiaobai'
  ? 'wangxiaobai' extends 'wangxiaobai' | '18'
    ? 'wangxiaobai' | '18' extends string
      ? string extends String
        ? String extends Object
          ? Object extends any
            ? any extends unknown
              ? unknown extends any
                ? 8
                : 7
              :6
            :5
          :4
        :3
      :2
    :1
  :0
```

其返回的结果为 8 ，也就意味着所有条件均成立。   

还可以构造出一条更长的类型层级链：   

```ts
type VerboseTypeChain = never extends 'wangxiaobai'
  ? 'wangxiaobai' extends 'wangxiaobai' | '18'
    ? 'wangxiaobai' | '18' extends string
      ? string extends {}
        ? string extends String
          ? String extends {}
            ? {} extends object
              ? object extends {}
                ? {} extends Object
                  ? Object extends {}
                    ? object extends Object
                      ? Object extends object
                        ? Object extends any
                          ? Object extends unknown
                            ? any extends unknown
                              ? unknown extends any
                                ? 8
                                : 7
                              : 6
                            : 5
                          : 4
                        : 3
                      : 2
                    : 1
                  : 0
                : -1
              : -2
            : -3
          : -4
        : -5
      : -6
    : -7
  : -8
```

结果仍然为 8 。   

### 其他比较场景 

* 对于基类和派生类，通常情况下派生类会完全保留基类的结构，而只是自己新增新的属性与方法。在结构化类型的比较下，其类型自然会存在子类型关系。更不用说派生类本身就是 extends 基类得到的。   

* 联合类型的判断，前面只是判断联合类型的单个成员，那如果是多个成员呢？

```ts
type Result36 = 1 | 2 | 3 extends 1 | 2 | 3 | 4 ? 1 : 2; // 1
type Result37 = 2 | 4 extends 1 | 2 | 3 | 4 ? 1 : 2; // 1
type Result38 = 1 | 2 | 5 extends 1 | 2 | 3 | 4 ? 1 : 2; // 2
type Result39 = 1 | 5 extends 1 | 2 | 3 | 4 ? 1 : 2; // 2
```

实际上，对于联合类型地类型层级比较，只需要比较一个联合类型是否可被视为另一个联合类型的子集，即这个联合类型中所有成员在另一个联合类型中都能找到。    

* 数组和元组

数组和元组是一个比较特殊的部分:  

```ts
type Result40 = [number, number] extends number[] ? 1 : 2; // 1
type Result41 = [number, string] extends number[] ? 1 : 2; // 2
type Result42 = [number, string] extends (number | string)[] ? 1 : 2; // 1
type Result43 = [] extends number[] ? 1 : 2; // 1
type Result44 = [] extends unknown[] ? 1 : 2; // 1
type Result45 = number[] extends (number | string)[] ? 1 : 2; // 1
type Result46 = any[] extends number[] ? 1 : 2; // 1
type Result47 = unknown[] extends number[] ? 1 : 2; // 2
type Result48 = never[] extends number[] ? 1 : 2; // 1
```

1. 40，这个元组类型实际上能确定其内部成员全部为 number 类型，因此是 number[] 的子类型。而 41 中混入了别的类型元素，因此认为不成立。     
2. 42 混入了别的类型，但其判断条件为 (number | string)[] ，即其成员需要为 number 或 string 类型。     
3. 43 的成员是未确定的，等价于 never[] extends number[]，44 同理。    
4. 45类似于41，即可能存在的元素类型是符合要求的。    
5. 46、47，还记得身化万千的 any 类型和小心谨慎的 unknown 类型嘛？    
6. 48，类似于 43、44，由于 never 类型本就位于最下方，这里显然成立。只不过 never[] 类型的数组也就无法再填充值了。    

### 总结

<img src="https://oweqian.oss-cn-hangzhou.aliyuncs.com/ts/img_04.png" alt="" width="600" />  

## 类型逻辑运算

### 条件类型基础

条件类型的语法类似于平时常用的三元表达式：   

```
ValueA === ValueB ? Result1 : Result2;
TypeA extends TypeB ? Result1 : Result2;
```

条件类型中使用 extends 判断类型的兼容性，而非判断类型的全等性。在类型层面中，对于能够进行赋值操作的两个变量，并不需要它们的类型完全相等，只需要具有兼容性，而两个完全相同的类型，其 extends 自然也是成立的。    

条件类型绝大部分场景下会和泛型一起使用，泛型参数的实际类型会在实际调用时才被填充，而条件类型在这一基础上，可以基于填充后的泛型参数做进一步的类型操作。    

```ts
type LiteralType<T> = T extends string ? 'string' : 'other';

type Res1 = LiteralType<'wangxiaobai'>; // "string"
type Res2 = LiteralType<18>; // "other"
```

条件类型中也常见多层嵌套，如：  

```ts
export type LiteralType<T> = T extends string
	? 'string'
	: T extends number
	? 'number'
	: T extends boolean
	? 'boolean'
	: T extends null
	? 'null'
	: T extends undefined
	? 'undefined'
	: never;

type Res1 = LiteralType<'wangxiaobai'>; // "string"
type Res2 = LiteralType<18>; // "number"
type Res3 = LiteralType<true>; // "boolean"
```

在函数中，条件类型与泛型的搭配同样很常见。   

```ts
function universalAdd<T extends number | bigint | string>(x: T, y: T) {
    return x + (y as any);
}
```

当调用这个函数时，由于两个参数都引用了泛型参数 T ，因此泛型会被填充为一个联合类型：   

```ts
universalAdd(18, 1); // T 填充为 18 | 1
universalAdd('wangxiaobai', '18'); // T 填充为 'wangxiaobai' | '18'
```

此时的返回值类型就需要从这个字面量联合类型中推导回其原本的基础类型。      

同一基础类型的字面量联合类型可以被认为是此基础类型的子类型，即 18 | 1 是 number 的子类型。   

因此可以使用嵌套的条件类型来进行字面量类型到基础类型地提取：    

```ts
function universalAdd<T extends number | bigint | string>(
	x: T,
	y: T
): LiteralToPrimitive<T> {
	return x + (y as any);
}

export type LiteralToPrimitive<T> = T extends number
	? number
	: T extends bigint
	? bigint
	: T extends string
	? string
	: never;

universalAdd('wangxiaobai', '18'); // string
universalAdd(18, 1); // number
universalAdd(10n, 10n); // bigint
```

条件类型还可以用来对更复杂的类型进行比较，比如函数类型：    

```ts
type Func = (...args: any[]) => any;

type FunctionConditionType<T extends Func> = T extends (
  ...args: any[]
) => string
  ? 'A string return func!'
  : 'A non-string return func!';

//  "A string return func!"
type StringResult = FunctionConditionType<() => string>;
// 'A non-string return func!';
type NonStringResult1 = FunctionConditionType<() => boolean>;
// 'A non-string return func!';
type NonStringResult2 = FunctionConditionType<() => number>;
```

条件类型用于判断两个函数类型是否具有兼容性，而条件中并不限制参数类型，仅比较二者的返回值类型。   

### infer 关键字

在上面的例子中，假如不再比较填充的函数类型是否是 (...args: any[]) => string 的子类型，而是要拿到其返回值类型呢？  

TypeScript 中支持通过 infer 关键字来在条件类型中提取类型的某一部分信息。    

```ts
type FunctionReturnType<T extends Func> = T extends (
  ...args: any[]
) => infer R
  ? R
  : never;
```

上面的代码表达了当传入的类型参数满足 T extends (...args: any[] ) => infer R 这样一个结构，返回 infer R 位置的值，即 R。否则，返回 never。    

infer 是 inference 的缩写，意为推断，如 infer R 中 R 就表示 待推断的类型。    

infer 只能在条件类型中使用。    

这里的类型结构并不局限于函数类型结构，还可以是数组：    

```ts
type Swap<T extends any[]> = T extends [infer A, infer B] ? [B, A] : T;

type SwapResult1 = Swap<[1, 2]>; // 符合元组结构，首尾元素替换[2, 1]
type SwapResult2 = Swap<[1, 2, 3]>; // 不符合结构，没有发生替换，仍是 [1, 2, 3]
```

由于声明的结构是一个仅有两个元素的元组，因此三个元素的元组就被认为是不符合类型结构了。但可以使用 rest 操作符来处理任意长度的情况：    

```ts
// 提取首尾两个
type ExtractStartAndEnd<T extends any[]> = T extends [
  infer Start,
  ...any[],
  infer End
]
  ? [Start, End]
  : T;

// 调换首尾两个
type SwapStartAndEnd<T extends any[]> = T extends [
  infer Start,
  ...infer Left,
  infer End
]
  ? [End, ...Left, Start]
  : T;

// 调换开头两个
type SwapFirstTwo<T extends any[]> = T extends [
  infer Start1,
  infer Start2,
  ...infer Left
]
  ? [Start2, Start1, ...Left]
  : T;
```

infer 甚至可以和 rest 操作符一样同时提取一组不定长的类型，而 ...any[] 的用法是否也让你直呼神奇？   

上面的输入输出仍然都是数组，而实际上完全可以进行结构层面的转换。比如从数组到联合类型：    

```ts
type ArrayItemType<T> = T extends Array<infer ElementType> ? ElementType : never;

type ArrayItemTypeResult1 = ArrayItemType<[]>; // never
type ArrayItemTypeResult2 = ArrayItemType<string[]>; // string
type ArrayItemTypeResult3 = ArrayItemType<[string, number]>; // string | number
```

原理即是这里的 [string, number] 实际上等价于 (string | number)[]。   

除了数组，infer 结构也可以是接口：   

```ts
// 提取对象的属性类型
type PropType<T, K extends keyof T> = T extends { [Key in K]: infer R }
  ? R
  : never;

type PropTypeResult1 = PropType<{ name: string }, 'name'>; // string
type PropTypeResult2 = PropType<{ name: string; age: number }, 'name' | 'age'>; // string | number

// 反转键名与键值
type ReverseKeyValue<T extends Record<string, unknown>> = T extends Record<infer K, infer V> ? Record<V & string, K> : never

type ReverseKeyValueResult1 = ReverseKeyValue<{ 'key': 'value' }>; // { "value": "key" }
```

为了体现 infer 作为类型工具的属性，结合了索引类型与映射类型，以及使用 & string 来确保属性名为 string 类型的小技巧。   

为什么需要这个小技巧，如果不使用又会有什么问题呢？    

```ts
// 类型“V”不满足约束“string | number | symbol”。
type ReverseKeyValue<T extends Record<string, string>> = T extends Record<
  infer K,
  infer V
>
  ? Record<V, K>
  : never;
```

明明约束已经声明了 V 的类型是 string，为什么还是报错了？    

这是因为泛型参数 V 的来源是从键值类型推导出来的，TypeScript 中这样对键值类型进行 infer 推导，将导致类型信息丢失，而不满足索引签名类型只允许 string | number | symbol 的要求。   

这里需要同时满足其两端的类型，使用 V & string 这一形式，就确保了最终符合条件的类型参数 V 一定会满足 string | never 这个类型，因此可以被视为合法的索引签名类型。   

infer 结构还可以是 Promise 结构。    

```ts
type PromiseValue<T> = T extends Promise<infer V> ? V : T;

type PromiseValueResult1 = PromiseValue<Promise<number>>; // number
type PromiseValueResult2 = PromiseValue<number>; // number，但并没有发生提取
```

像条件类型可以嵌套一样，infer 关键字也经常被使用在嵌套的场景中，包括对类型结构深层信息地提取，以及对提取到类型信息的筛选等。   

比如上面的 PromiseValue，如果传入了一个嵌套的 Promise 类型就失效了：   

```ts
type PromiseValueResult3 = PromiseValue<Promise<Promise<boolean>>>; // Promise<boolean>，只提取了一层
```

这时就需要进行嵌套地提取了：    

```ts
type PromiseValue<T> = T extends Promise<infer V>
  ? V extends Promise<infer N>
    ? N
    : V
  : T;
```

也可以使用递归来处理任意嵌套深度：     

```ts
type PromiseValue<T> = T extends Promise<infer V> ? PromiseValue<V> : T;
```

### 分布式条件类型

分布式条件类型也称条件类型的分布式特性，只不过是条件类型在满足一定情况下会执行的逻辑而已。   

```ts
type Condition<T> = T extends 1 | 2 | 3 ? T : never;

// 1 | 2 | 3
type Res1 = Condition<1 | 2 | 3 | 4 | 5>;

// never
type Res2 = 1 | 2 | 3 | 4 | 5 extends 1 | 2 | 3 ? 1 | 2 | 3 | 4 | 5 : never;
```

仔细观察这两个类型别名的差异会发现，唯一的差异就是在 Res1 中，进行判断的联合类型被作为泛型参数传入给另一个独立的类型别名，而 Res2 中直接对这两者进行判断。   

记住第一个差异：是否通过泛型参数传入。   

```ts
type Naked<T> = T extends boolean ? 'Y' : 'N';
type Wrapped<T> = [T] extends [boolean] ? 'Y' : 'N';

// "N" | "Y"
type Res3 = Naked<number | boolean>;

// "N"
type Res4 = Wrapped<number | boolean>;
```

现在都是通过泛型参数传入了，但诡异的事情又发生了，为什么第一个还是个联合类型？   

第二个倒是好理解一些，元组的成员有可能是数字类型，显然不兼容于 [boolean]。   

再仔细观察这两个例子会发现，它们唯一的差异是条件类型中的泛型参数是否被数组包裹了。    

同时你会发现在 Res3 的判断中，其联合类型的两个分支，恰好对应于分别使用 number 和 boolean 去作为条件类型判断时的结果。    

把上面的线索理一下大致得到了条件类型分布式起作用的条件:   

* 类型参数需要是一个联合类型。   
* 类型参数需要通过泛型参数的方式传入。   
* 条件类型中的泛型参数不能被包裹。   

条件类型分布式特性会产生的效果也很明显了，即将这个联合类型拆开来，每个分支分别进行一次条件类型判断，再将最后的结果合并起来（如 Naked 中）。   

官方的解释：对于属于裸类型参数的检查类型，条件类型会在实例化时期自动分发到联合类型上。    

这里的自动分发可以这么理解：   

```ts
type Naked<T> = T extends boolean ? 'Y' : 'N';

// (number extends boolean ? "Y" : "N") | (boolean extends boolean ? "Y" : "N")
// "N" | "Y"
type Res3 = Naked<number | boolean>;
```

这里的裸类型参数，其实指的就是泛型参数是否完全裸露，上面使用数组包裹泛型参数只是其中一种方式，比如还可以这么做：   

```ts
export type NoDistribute<T> = T & {};

type Wrapped<T> = NoDistribute<T> extends boolean ? "Y" : "N";

type Res1 = Wrapped<number | boolean>; // "N"
type Res2 = Wrapped<true | false>; // "Y"
type Res3 = Wrapped<true | false | 18>; // "N"
```

需要注意的是，并不是只会通过裸露泛型参数，来确保分布式特性能够发生。   

在某些情况下也会需要包裹泛型参数来禁用掉分布式特性。最常见的场景也许还是联合类型的判断，即不希望进行联合类型成员的分布判断，而是希望直接判断这两个联合类型的兼容性判断。    

就像在最初的 Res2 中那样：   

```ts
type CompareUnion<T, U> = [T] extends [U] ? true : false;

type CompareRes1 = CompareUnion<1 | 2, 1 | 2 | 3>; // true
type CompareRes2 = CompareUnion<1 | 2, 1>; // false
```

通过将参数与条件都包裹起来的方式对联合类型的比较就变成了数组成员类型的比较，在此时就会严格遵守类型层级一文中联合类型的类型判断。   

另外一种情况则是，当想判断一个类型是否为 never 时，也可以通过类似的手段：   

```ts
type IsNever<T> = [T] extends [never] ? true : false;

type IsNeverRes1 = IsNever<never>; // true
type IsNeverRes2 = IsNever<'wangxiaobai'>; // false
```

这里的原因其实并不是因为分布式条件类型。当条件类型的判断参数为 any，会直接返回条件类型两个结果的联合类型。   

而在这里其实类似，当通过泛型传入的参数为 never，则会直接返回 never。   

需要注意的是这里的 never 与 any 的情况并不完全相同，any 在直接作为判断参数时、作为泛型参数时都会产生这一效果：   

```ts
// 直接使用，返回联合类型
type Tmp1 = any extends string ? 1 : 2;  // 1 | 2

type Tmp2<T> = T extends string ? 1 : 2;
// 通过泛型参数传入，同样返回联合类型
type Tmp2Res = Tmp2<any>; // 1 | 2

// 如果判断条件是 any，那么仍然会进行判断
type Special1 = any extends any ? 1 : 2; // 1
type Special2<T> = T extends any ? 1 : 2;
type Special2Res = Special2<any>; // 1
```

而 never 仅在作为泛型参数时才会产生：   

```ts
// 直接使用，仍然会进行判断
type Tmp3 = never extends string ? 1 : 2; // 1

type Tmp4<T> = T extends string ? 1 : 2;
// 通过泛型参数传入，会跳过判断
type Tmp4Res = Tmp4<never>; // never

// 如果判断条件是 never，还是仅在作为泛型参数时才跳过判断
type Special3 = never extends never ? 1 : 2; // 1
type Special4<T> = T extends never ? 1 : 2;
type Special4Res = Special4<never>; // never
```

这里的 any、never 两种情况都不会实际地执行条件类型，而在这里通过包裹的方式让它不再是 never，也就能够去执行判断了。   

## 内置工具类型

### 分类

内置的工具类型大致划分为以下几类：   

* 属性修饰工具类型：对属性的修饰，包括对象属性和数组元素的可选/必选、只读/可写。  
* 结构工具类型：对既有类型的裁剪、拼接、转换等，比如使用对一个对象类型裁剪得到一个新的对象类型，将联合类型结构转换到交叉类型结构。  
* 集合工具类型：对集合（即联合类型）的处理，即交集、并集、差集、补集。  
* 模式匹配工具类型：基于 infer 的模式匹配，即对一个既有类型特定位置类型的提取，比如提取函数类型签名中的返回值类型。    

#### 属性修饰工具类型

访问性修饰工具类型包括以下三位：   

```ts
type Partial<T> = {
    [P in keyof T]?: T[P];
};

type Required<T> = {
    [P in keyof T]-?: T[P];
};

type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
```

Partial 与 Required 可以认为是一对工具类型，它们的功能是相反的，而在实现上，它们的唯一差异是在索引类型签名处的可选修饰符，Partial 是 ?，即标记属性为可选，而 Required 则是 -?，相当于在原本属性上如果有 ? 这个标记，则移除它。    

Partial 也可以使用 +? 来显式的表示添加可选标记：   

```ts
type Partial<T> = {
	[P in keyof T]+?: T[P];
};
```

可选标记不等于修改此属性类型为 原类型 | undefined ，如以下的接口结构：     

```ts
interface Foo {
	optional: string | undefined;
	required: string;
}
```

如果声明一个对象去实现这个接口，它仍然会要求你提供 optional 属性：   

```ts
interface Foo {
	optional: string | undefined;
	required: string;
}
// 类型 "{ required: string; }" 中缺少属性 "optional"，但类型 "Foo" 中需要该属性。
const foo1: Foo = {
  required: '1',
};

const foo2: Foo = {
  required: '1',
  optional: undefined
};
```

这是因为对于结构声明来说，一个属性是否必须提供仅取决于其是否携带可选标记。   

即使使用 never 也无法标记这个属性为可选：   

```ts
interface Foo {
	optional: never;
	required: string;
}

const foo: Foo = {
	required: '1',
	// 不能将类型“string”分配给类型“never”。
	optional: '',
};
```

类似 +?，Readonly 中也可以使用 +readonly：    

```ts
type Readonly<T> = {
	+readonly [P in keyof T]: T[P];
};
```

虽然 TypeScript 中并没有提供它的另一半，参考 Required 很容易想到这么实现一个工具类型 Mutable，来将属性中的 readonly 修饰移除：    

```ts
type Mutable<T> = {
	-readonly [P in keyof T]: T[P];
};
```

#### 结构工具类型

结构工具类型可以分为两类，结构声明和结构处理。    

结构声明工具类型即快速声明一个结构，比如内置类型中的 Record：   

```ts
type Record<K extends keyof any, T> = {
	[P in K]: T;
};
```

K extends keyof any 即为键的类型，这里使用 extends keyof any 标明，传入的 K 可以是单个类型，也可以是联合类型，而 T 即为属性的类型。    

```ts
// 键名均为字符串，键值类型未知
type Record1 = Record<string, unknown>;
// 键名均为字符串，键值类型任意
type Record2 = Record<string, any>;
// 键名为字符串或数字，键值类型任意
type Record3 = Record<string | number, any>;
```

Record<string, unknown> 和 Record<string, any> 是日常使用较多的形式，通常使用这两者来代替 object 。    

在一些工具类库源码中还存在类似的结构声明工具类型，如：    

```ts
type Dictionary<T> = {
	[index: string]: T;
};

type NumericDictionary<T> = {
	[index: number]: T;
};
```

Dictionary （字典）结构只需要一个作为属性类型的泛型参数即可。    

对于结构处理工具类型，在 TypeScript 中主要是 Pick、Omit：   

```ts
type Pick<T, K extends keyof T> = {
	[P in K]: T[P];
};

type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

Pick 接受两个泛型参数，T 即是进行结构处理的原类型（一般是对象类型），而 K 则被约束为 T 类型的键名联合类型。    

由于泛型约束是立即填充推导的，即为第一个泛型参数传入 Foo 类型以后，K 的约束条件会立刻被填充，在输入 K 时会获得代码提示。    

```ts
interface Foo {
	name: string;
	age: number;
	job: JobUnionType;
}

type PickedFoo = Pick<Foo, "name" | "age">
```

Pick 会将传入的联合类型作为需要保留的属性，使用这一联合类型配合映射类型，上面的例子等价于：      

```ts
type Pick<T> = {
	[P in "name" | "age"]: T[P];
};
```

联合类型的成员会被依次映射，并通过索引类型访问来获取到它们原本的类型。    

Omit 类型，它是 Pick 的反向实现：Pick 是保留这些传入的键，比如从一个庞大的结构中选择少数字段保留，需要的是这些少数字段，而 Omit 则是移除这些传入的键，也就是从一个庞大的结构中剔除少数字段，需要的是剩余的多数部分。    

```ts
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

Omit 是基于 Pick 实现的，这也是 TypeScript 中成对工具类型的另一种实现方式。上面的 Partial 与 Required 使用类似的结构，在关键位置使用一个相反操作来实现反向，而这里的 Omit 类型则是基于 Pick 类型实现，也就是反向工具类型基于正向工具类型实现。     

首先接受的泛型参数类似，也是一个类型与联合类型（要剔除的属性），在将这个联合类型传入给 Pick 时多了一个 Exclude，Exclude<A, B> 的结果就是联合类型 A 中不存在于 B 中的部分：    

```ts
type Tmp1 = Exclude<1, 2>; // 1
type Tmp2 = Exclude<1 | 2, 2>; // 1
type Tmp3 = Exclude<1 | 2 | 3, 2 | 3>; // 1
type Tmp4 = Exclude<1 | 2 | 3, 2 | 4>; // 1 | 3
```

Exclude<keyof T, K> 就是 T 的键名联合类型中剔除了 K 的部分，将其作为 Pick 的键名，就实现了剔除一部分类型的效果。     

#### 集合工具类型

对于两个集合来说，通常存在交集、并集、差集、补集几种情况。    

* 并集：两个集合的合并，合并时重复的元素只会保留一份。      
* 交集：两个集合的相交部分，即同时存在于这两个集合内的元素组成的集合。     
* 差集：对于 A、B 两个集合来说，A 相对于 B 的差集即为 A 中独有而 B 中不存在的元素 的组成的集合，或者说 A 中剔除了 B 中也存在的元素以后，还剩下的部分。    
* 补集：补集是差集的特殊情况，此时集合 B 为集合 A 的子集，在这种情况下 A 相对于 B 的差集 + B = 完整的集合 A。   

内置工具类型中提供了交集与差集的实现：    

```ts
type Extract<T, U> = T extends U ? T : never;

type Exclude<T, U> = T extends U ? never : T;
```

当 T、U 都是联合类型（视为一个集合）时，T 的成员会依次被拿出来进行 extends U ? T1 : T2 的计算，然后将最终的结果再合并成联合类型。   

交集 Extract ，其运行逻辑是这样的：    

```ts
type AExtractB = Extract<1 | 2 | 3, 1 | 2 | 4>; // 1 | 2

type _AExtractB =
| (1 extends 1 | 2 | 4 ? 1 : never) // 1
| (2 extends 1 | 2 | 4 ? 2 : never) // 2
| (3 extends 1 | 2 | 4 ? 3 : never); // never
```

差集 Exclude 类似，差集存在相对的概念，即 A 相对于 B 的差集与 B 相对于 A 的差集并不一定相同，而交集则一定相同。     

```ts
type SetA = 1 | 2 | 3 | 5;

type SetB = 0 | 1 | 2 | 4;

type AExcludeB = Exclude<SetA, SetB>; // 3 | 5
type BExcludeA = Exclude<SetB, SetA>; // 0 | 4

type _AExcludeB =
| (1 extends 0 | 1 | 2 | 4 ? never : 1) // never
| (2 extends 0 | 1 | 2 | 4 ? never : 2) // never
| (3 extends 0 | 1 | 2 | 4 ? never : 3) // 3
| (5 extends 0 | 1 | 2 | 4 ? never : 5); // 5

type _BExcludeA =
| (0 extends 1 | 2 | 3 | 5 ? never : 0) // 0
| (1 extends 1 | 2 | 3 | 5 ? never : 1) // never
| (2 extends 1 | 2 | 3 | 5 ? never : 2) // never
| (4 extends 1 | 2 | 3 | 5 ? never : 4); // 4
```

实现并集与补集：   

```ts
// 并集
export type Concurrence<A, B> = A | B;

// 交集
export type Intersection<A, B> = A extends B ? A : never;

// 差集
export type Difference<A, B> = A extends B ? never : A;

// 补集
export type Complement<A, B extends A> = Difference<A, B>;
```

补集基于差集实现，只需要约束集合 B 为集合 A 的子集即可。    

在基于分布式条件类型的工具类型中，也存在着正反工具类型，但并不都是简单地替换条件类型结果的两端，如交集与补集就只是简单调换了结果，但二者作用却完全不同。      

#### 模式匹配工具类型

主要使用条件类型与 infer 关键字，infer 其实代表了一种模式匹配的思路。     

对函数类型签名的模式匹配：    

```ts
type FunctionType = (...args: any) => any;

type Parameters<T extends FunctionType> = T extends (...args: infer P) => any ? P : never;
type ReturnType<T extends FunctionType> = T extends (...args: any) => infer R ? R : any;
```

根据 infer 的位置不同，就能够获取到不同位置的类型，在函数这里则是参数类型与返回值类型。     

只匹配第一个参数类型：   

```ts
type FirstParameter<T extends FunctionType> = T extends (arg: infer P, ...args: any) => any ? P : never;

type FuncFoo = (arg: number) => void;
type FuncBar = (...args: string[]) => void;

type FooFirstParameter = FirstParameter<FuncFoo>; // number
type BarFirstParameter = FirstParameter<FuncBar>; // string
```

内置工具类型中还有一组对 Class 进行模式匹配的工具类型：    

```ts
type ClassType = abstract new (...args: any) => any;

type ConstructorParameters<T extends ClassType> = T extends abstract new (...args: infer P) => any ? P : never;
type InstanceType<T extends ClassType> = T extends abstract new (...args: any) => infer R ? R : any;
```

Class 的模式匹配思路类似于函数，或者说这是一个通用的思路，即基于放置位置的匹配。放在参数部分，就是构造函数的参数类型，放在返回值部分，就是 Class 的实例类型了。   

## 上下文类型

举一个最常见的例子：  

```ts
window.onerror = (event, source, line, col, err) => {};
```

在这个例子里，虽然并没有为 onerror 的各个参数声明类型，但是它们也已经获得了正确的类型。   

这是因为 onerror 的类型声明已经内置了：  

```ts
interface Handler {
  // 简化
  onerror: OnErrorEventHandlerNonNull;
}

interface OnErrorEventHandlerNonNull {
    (event: Event | string, source?: string, lineno?: number, colno?: number, error?: Error): any;
}
```

实现一个函数签名，效果是一样的：  

```ts
type CustomHandler = (name: string, age: number) => boolean;

// 也推导出了参数类型
const handler: CustomHandler = (arg1, arg2) => true;
```

除了参数类型，返回值类型同样会纳入管控：   

```ts
declare const struct: {
  handler: CustomHandler;
};
// 不能将类型“void”分配给类型“boolean”。
struct.handler = (name, age) => {};
```

在这里，参数的类型基于其上下文类型中的参数类型位置来进行匹配，arg1 对应到 name ，所以是 string 类型，arg2 对应到 age，所以是 number 类型。    

这就是上下文类型的核心理念：基于位置的类型推导。    

在上下文类型中，实现的表达式可以只使用更少的参数，而不能使用更多，这是因为上下文类型基于位置的匹配，一旦参数个数超过定义的数量，那就没法进行匹配了。    

```ts
// 正常
window.onerror = (event) => {};
// 报错
window.onerror = (event, source, line, col, err, extra) => {};
```

上下文类型也可以进行”嵌套“情况下的类型推导:   

```ts
declare let func: (raw: number) => (input: string) => any;

// raw → number
func = (raw) => {
  // input → string
  return (input) => {};
};
```

在某些情况下，上下文类型的推导能力也会失效:   

```ts
class Foo {
  foo!: number;
}

class Bar extends Foo {
  bar!: number;
}

let f1: { (input: Foo): void } | { (input: Bar): void };
// 参数“input”隐式具有“any”类型。
f1 = (input) => {};
```

预期的结果是 input 被推导为 Foo | Bar 类型，也就是所有符合结构的函数类型的参数，但却失败了。这是因为 TypeScript 中的上下文类型目前暂时不支持这一判断方式。   

直接使用一个联合类型参数的函数签名：   

```ts
let f2: { (input: Foo | Bar): void };
// Foo | Bar
f2 = (input) => {};
```

如果联合类型中将这两个类型再嵌套一层，此时上下文类型反而正常了：   

```ts
let f3:
  | { (raw: number): (input: Foo) => void }
  | { (raw: number): (input: Bar) => void };

// raw → number
f3 = (raw) => {
  // input → Bar
  return (input) => {};
};
```

任何接收 Foo 类型参数的地方，都可以接收一个 Bar 类型参数，因此推导到 Bar 类型要更加安全。    

### void 返回值类型下的特殊情况

上下文类型同样会推导并约束函数的返回值类型，但存在特殊情况，当内置函数类型的返回值类型为 void 时：   

```ts
type CustomHandler = (name: string, age: number) => void;

const handler1: CustomHandler = (name, age) => true;
const handler2: CustomHandler = (name, age) => 'wangxiaobai';
const handler3: CustomHandler = (name, age) => null;
const handler4: CustomHandler = (name, age) => undefined;
```

这时函数实现返回值类型变成了五花八门的样子，而且还都不会报错。    

同样的，这也是一条世界底层的规则，上下文类型对于 void 返回值类型的函数，并不会真的要求它什么都不能返回。   

虽然这些函数实现可以返回任意类型的值，但对于调用结果的类型，仍然是 void：    

```ts
const result1 = handler1('wangxiaobai', 18); // void
const result2 = handler2('wangxiaobai', 18); // void
const result3 = handler3('wangxiaobai', 18); // void
const result4 = handler4('wangxiaobai', 18); // void
```

## 函数类型层级（TODO）

给出三个具有层级关系的类，分别代表动物、狗、柯基。   

```ts
class Animal {
  asPet() {}
}

class Dog extends Animal {
  bark() {}
}

class Corgi extends Dog {
  cute() {}
}
```

对于一个接受 Dog 类型并返回 Dog 类型的函数，可以这样表示：  

```ts
type DogFactory = (args: Dog) => Dog;
```

简化为：Dog -> Dog 的表达形式。   

对于函数类型比较，实际上要比较的即是参数类型与返回值类型（也只能是这俩位置的类型）。   

对于 Animal、Dog、Corgi 这三个类，如果将它们分别可重复地放置在参数类型与返回值类型处，就可以得到以下这些函数签名类型：   

* Animal -> Animal   
* Animal -> Dog   
* Animal -> Corgi   
* Dog -> Dog   
* Dog -> Animal   
* Dog -> Corgi   
* Corgi -> Animal   
* Corgi -> Dog   
* Corgi -> Corgi    

引入一个辅助函数：它接收一个 Dog -> Dog 类型的参数：   

```ts
function transformDogAndBark(dogFactory: DogFactory) {
  const dog = dogFactory(new Dog());
  dog.bark();
}
```

如果一个值能够被赋值给某个类型的变量，那么可以认为这个值的类型为此变量类型的子类型。    

如一个简单接受 Dog 类型参数的函数：   

```ts
function makeDogBark(dog: Dog) {
  dog.bark();
}
```

它在调用时只可能接受 Dog 类型或 Dog 类型的子类型，而不能接受 Dog 类型的父类型：    

```ts
makeDogBark(new Corgi()); // 没问题
makeDogBark(new Animal()); // 不行
```

这是因为派生类（即子类）会保留基类的属性与方法，因此说其与基类兼容，但基类并不能未卜先知的拥有子类的方法。   

transformDogAndBark 函数会实例化一只狗狗，并传入 Factory（就像宠物美容），然后让它叫唤两声。这个函数同时约束了此类型的参数与返回值。   

"首先，我只会传入一只正常的狗狗，但它不一定是什么品种。其次，你返回的必须也是一只狗狗，我并不在意它是什么品种"。   

对于这两条约束依次进行检查：

* 对于 Animal/Dog/Corgi -> Animal 类型：无论它的参数类型是什么，它的返回值类型都是不满足条件的。因为它返回的不一定是合法的狗狗，即我它不是 Dog -> Dog 的子类型。    

* 对于 Corgi -> Corgi 与 Corgi -> Dog：其返回值满足了条件，但是参数类型又不满足了。这两个类型需要接受 Corgi 类型，可能内部需要它腿短的这个特性。但可没说一定会传入柯基，如果传个德牧，程序可能就崩溃了。   

* 对于 Dog -> Corgi、Animal -> Corgi、Animal -> Dog：首先它们的参数类型正确的满足了约束，能接受一只狗狗。其次，它们的返回值类型也一定会能汪汪汪。    

如果去掉了包含 Dog 类型的例子，会发现只剩下 Animal -> Corgi 了，也即是说，(Animal → Corgi) ≼ (Dog → Dog) 成立（A ≼ B 意为 A 为 B 的子类型）。   

结论：    

* 参数类型允许为 Dog 的父类型，不允许为 Dog 的子类型。    
* 返回值类型允许为 Dog 的子类型，不允许为 Dog 的父类型。    

## 类型编程进阶  

### 属性修饰进阶

#### 深层属性修饰

```ts
type PromiseValue<T> = T extends Promise<infer V> ? PromiseValue<V> : T;
```

在条件类型成立时，再次调用这个工具类型形成递龟。在某一次递龟到条件类型不成立时，就会直接返回这个类型值。   

对于 Partial、Required，也可以进行这样地处理：   

```ts
export type DeepPartial<T extends object> = {
  [K in keyof T]?: T[K] extends object ? DeepPartial<T[K]> : T[K];
};
```

简单起见使用 object 作为泛型约束与条件，这意味着也有可能传入函数、数组等类型。   

实现其他进行递归属性修饰的工具类型，展示如下：   

```ts
export type DeepPartial<T extends object> = {
  [K in keyof T]?: T[K] extends object ? DeepPartial<T[K]> : T[K];
};

export type DeepRequired<T extends object> = {
  [K in keyof T]-?: T[K] extends object ? DeepRequired<T[K]> : T[K];
};

// 也可以记作 DeepImmutable
export type DeepReadonly<T extends object> = {
  readonly [K in keyof T]: T[K] extends object ? DeepReadonly<T[K]> : T[K];
};

export type DeepMutable<T extends object> = {
  -readonly [K in keyof T]: T[K] extends object ? DeepMutable<T[K]> : T[K];
};
```

从联合类型中剔除 null | undefined 的工具类型 NonNullable：   

```ts
type NonNullable<T> = T extends null | undefined ? never : T;
```

实现一个 DeepNonNullable 来递归剔除所有属性的 null 与 undefined：   

```ts
type NonNullable<T> = T extends null | undefined ? never : T;

export type DeepNonNullable<T extends object> = {
  [K in keyof T]: T[K] extends object
    ? DeepNonNullable<T[K]>
    : NonNullable<T[K]>;
};
```

DeepNonNullable 也有自己的另一半：DeepNullable：   

```ts
export type Nullable<T> = T | null;

export type DeepNullable<T extends object> = {
  [K in keyof T]: T[K] extends object ? DeepNullable<T[K]> : Nullable<T[K]>;
};
```

#### 已知属性部分修饰

要让一个对象的三个已知属性为可选的，那只要把这个对象拆成 A、B 两个对象结构，分别由三个属性和其他属性组成。然后让对象 A 的属性全部变为可选的，和另外一个对象 B 组合起来，不就行了吗？    

* 拆分对象结构，那不就是结构工具类型，即 Pick 与 Omit？   
* 三个属性的对象全部变为可选，那不就是属性修饰？   
* 组合两个对象类型，也就意味着得到一个同时符合这两个对象类型的新结构，那不就是交叉类型？   

MarkPropsAsOptional 会将一个对象的部分属性标记为可选：  

```ts
type MakePropsAsOptional<T extends object, K extends keyof T = keyof T> = Flatten<Partial<Pick<T, K>> & Omit<T, K>>;
```

T 为需要处理的对象类型，而 K 为需要标记为可选的属性。K 必须为 T 内部的属性，将其约束为 keyof T，即对象属性组成的字面量联合类型。   

为了让它能够直接代替掉 Partial，为其指定默认值也为 keyof T，这样在不传入第二个泛型参数时，它的表现就和 Partial 一致，即全量的属性可选。    

Partial<Pick<T, K>> 为需要标记为可选的属性组成的对象子结构，Omit<T, K> 则为不需要处理的部分，使用交叉类型将其组合即可。    

验证下效果：   

```ts
type MarkPropsAsOptionalStruct = MarkPropsAsOptional<
  {
    foo: string;
    bar: number;
    baz: boolean;
  },
  'bar'
>;
```

<img src="https://oweqian.oss-cn-hangzhou.aliyuncs.com/ts/img_05.png" alt="" width="700" />  

引入一个辅助的工具类型，称其为 Flatten，对于这种交叉类型的结构，Flatten 能够将它展平为单层的对象结构。而它的实现也很简单，就是复制一下结构：   

```ts
export type Flatten<T> = { [K in keyof T]: T[K] };

export type MarkPropsAsOptional<
  T extends object,
  K extends keyof T = keyof T
> = Flatten<Partial<Pick<T, K>> & Omit<T, K>>;
```

<img src="https://oweqian.oss-cn-hangzhou.aliyuncs.com/ts/img_06.png" alt="" width="700" />  

实现其它类型的部分修饰：   

```ts
type MakePropsAsRequired<T extends object, K extends keyof T = keyof T> = Flatten<Required<Pick<T, K>> & Omit<T, K>>;

type MakePropsAsReadonly<T extends object, K extends keyof T = keyof T> = Flatten<Readonly<Pick<T, K>> & Omit<T, K>>;

type MakePropsAsNullable<T extends object, K extends keyof T = keyof T> = Flatten<Nullable<Pick<T, K>> & Omit<T, K>>;

type MakePropsAsNonNullable<T extends object, K extends keyof T = keyof T> = Flatten<NonNullable<Pick<T, K>> & Omit<T, K>>;
```