# TypeScript

- <https://www.typescriptlang.org/>
- <https://wangdoc.com/typescript/intro>

TypeScript 由 Microsoft 开发，是 ES 的超集，TS 包含了 ES 的所有特性，同时还扩展了一些新的功能，如静态类型、接口、枚举、泛型等等。TS在编写代码时提供了更好的类型检查和代码提示功能，从而提高了代码的可维护性和可读性。

> Angular2.0 和 Vue 3.0 都是基于 TypeScript 的

TypeScript 在运行前需要先编译为 JavaScript，而在编译阶段就会进行类型检查，可以理解为静态类型的 JS

```shell
npm install -g typescript

# typescript 的命令是：tsc
# 源码后缀为：.ts
# 用ts编写的React源码后缀为：.tsx
tsc hello.ts
```

```ts
function sayHello(person: string) {  // 冒号用于指定变量类型
    return 'Hello, ' + person;
}

let user = 'Tom';
console.log(sayHello(user));
```

## 数据类型

- 任意类型

```ts
let myFavoriteNumber: any = 'seven';  // 也可以直接声明为任意类型
myFavoriteNumber = 7;  // 编译时不会报错

// 变量声明时未指定类型，且没有赋值，那么它会被识别为任意值类型
let something;
something = 'seven';
something = 7;  // 编译时不会报错

// 变量声明时未指定类型，会推测出一个类型，即ts的类型推论
let myFavoriteNumber = 'seven';
myFavoriteNumber = 7;  // 编译时会报错
```

- 布尔值

```ts
let isDone: boolean = false;
```

- 数值

```ts
let decLiteral: number = 6;
let binaryLiteral: number = 0b1010;  // 二进制数值
let hexLiteral: number = 0xf00d;  // 十六进制数值
let infinityNumber: number = Infinity;  // 无限
let notANumber: number = NaN;  // 非数值
```

- 字符串

```ts
let sentence: string = `Hello, my name is ${myName}.`;
```

- 空值

```ts
let n: null = null;  // 空值
let u: undefined = undefined;  // 未定义

// void表示不是任何类型，常用于函数中表示没有返回值
function alertName(): void {
    alert('My name is Tom');
}
```

- 联合类型

Union Types 表示取值可以为多种类型中的一种，可以简单的理解为 or 的概念

```ts
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

## 数组和元组

```ts
// 数组合并了相同类型的对象
let fibonacci: number[] = [1, 1, 2, 3, 5];  // 数值型数组

// 元组（Tuple）合并了不同类型的对象
let tom: [string, number] = ['Tom', 25];  // tom[0]='Tom'
```

## 枚举

```ts
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};

// 常量枚举
const enum Directions {Up, Down, Left, Right}
```

## 对象类型

- 使用类型别名定义对象

```ts
type MyObject = {
  name: string;
  age: number;
  gender: string;
}

const person: MyObject = {
  name: "John",
  age: 30,
  gender: "male"
};
```

- 使用接口定义对象

```ts
// 接口一般首字母大写，有的编程语言中会建议接口的名称加上前缀：I
interface Person {
    readonly id: number;  // 只读属性，只需要在第一次实例化时赋值
    name: string;  // 普通属性，实例化时必需赋值
    age?: number;  // 可选属性，实例化时可以不赋值
    [propName: string]: any;  // 任意字符串类型属性: 任意类型属性值
}

// 实例化
let tom: Person = {
    id: 7,
    name: 'Tom',
    // age: 25,
    gender: 'male'
};
```

## 函数

```ts
// 函数声明（Function Declaration）
function sum(x, y) {
    return x + y;
}

// 函数表达式（Function Expression）
let mySum = function (x, y) {
    return x + y;
};

// 对输入和输出进行约束
function buildName(firstName: string = 'Tom', lastName?: string) {
    // firstName默认值为Tom
    // lastName后的?表示这是一个可选参数
    return firstName;
}
```

## 箭头函数 =>

箭头函数是 ES6 引入的新语法，它简化了函数定义的书写方式，特别是对于只有一个参数和单行返回语句的函数。

箭头函数的语法为：`(arguments) => expression`

- arguments 为参数列表
- expression 为函数体，可以是单行表达式或者用花括号包裹的多行语句块

## 类

```ts
// class定义类
class Animal {
    public name;
    // 构造函数
    constructor(name) {
        this.name = name;
    }
    sayHi() {
        return `My name is ${this.name}`;
    }
}

// new实例化
let a = new Animal('Jack');
console.log(a.sayHi()); // My name is Jack
```

抽象类

```ts
// 不能被实例化
abstract class Animal {
    pass;
}
```

## 类型别名

```ts
type Name = string;
type NameResolver = () => string;
// 类型别名常用于联合类型
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    } else {
        return n();
    }
}

// 在类型别名中 => 表示函数类型的定义，左边为参数，右边为返回值
// 表示接收两个参数 x 和 y，类型均为 number，并返回一个类型为 number 的值
type MyFuncType = (x: number, y: number) => number;
```

## 泛型

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

允许程序员在强类型程序设计语言 中编写代码时定义一些可变部分，这些部分在使用前必须作出指明。

`<T>`，我们称之为泛型变量（又称为类型变量、泛型参数），它是一种特殊的变量，声明时表示任何输入的类型，使用时进行指定数据类型，然后完成类型检测。

> 泛型常用 T、U、V 表示，通常用 T，T 是 Type 的简写

```ts
Array<any>  // 数组泛型

// 使用 extends 关键字实现类型的约束
// T 在没有明确指定时，被解析为 Animal 接口的类型（区别于默认值）
T extends Animal

// 表示 fn 可以接收任意数量和类型的参数
fn: (...args: T)
```
