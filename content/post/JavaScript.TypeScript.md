---
title: "JavaScript.TypeScript"
date: "2017-11-15"
categories:
 - "整理"
tags:
 - "TypeScript"
toc: true
---


# TypeScript

## 参考
- [Wiki - TypeScript](https://zh.wikipedia.org/wiki/TypeScript)
- [TypeScript Handbook（中文版）](https://zhongsp.gitbooks.io/typescript-handbook/content/index.html)
- [官网](http://www.typescriptlang.org/)
- [Microsoft/TypeScript](https://github.com/Microsoft/TypeScript)
- [5分钟上手TypeScript](https://www.tslang.cn/docs/handbook/typescript-in-5-minutes.html)
- [深入浅出ES6（八）：Symbols](http://www.infoq.com/cn/articles/es6-in-depth-symbols)


## 简介
- 微软开发、开源的编程语言，为 JavaScript 的严格超集
- 添加可选的`静态类型`和基于类的面向对象编程
- 安德斯·海尔斯伯格参与设计


## 安装&使用
- 安装
    - `npm install -g typescript`
- 创建 hello.ts
    ```
    function hello(user: string){
        return "Hello"+ user;
    }
    var user = "yao"
    document.body.innerHTML= hello(user)
    ```
- 编译
    - `tsc hello.ts`

## 扩展
### 基础类型
- 类型
    - number
    - bool
    - string
    - Tuple
        - `let x: [string, number]`
    - enum
        - `enum Color {Red, Green, Blue}`
    - any
        - 默认动态类型
    - void
    - null
    - undefined
    - never
        - 总是抛出异常或根本不会返回值的函数表达式
        - 箭头函数表达式的返回值类型
- 示例
    ```
    function Add(left: number, right: number): number {
        return left + right;
    }
    ```
- 细节
    - 类型批注被导出到`声明文件`，便于 javascript 和 TypeScript 脚本中类型信息均可使用
    - 类型断言
        - 清楚确切的类型时，通知编译器进行类型转换，而`不进行特殊数据检查和解构`
        - 写法
            - `let len: number = (<String>val).length;`
            - `let len: number = (val as string).length;`

### 类型推断
- 编译期自动推导出值的数据类型

### 变量声明
- var
    ```
    function f() {
        var a = 1;

        a = 2;
        var b = g();
        a = 3;

        return b;

        function g() {
            return a;
        }
    }

    f(); // returns 2

    for (var i = 0; i < 10; i++) {
        setTimeout(function() { console.log(i); }, 100 * i);
    }
    10* 10 // 每当g被调用时，它都可以访问到f里的a变量。
    ```
- let
    - 块作用域
    - 重定义与屏蔽
- const
    - 与 let 相同的作用域规则
    - 不能重新赋值

### 接口
-
    - `class` class `iplements` interface
    - `interface` interface1 `extends` interface2
    - `interface` interface `extends` class

- 示例
    ```
    interface LabelledValue {
      label: string;
      age?: number                // 可选属性
      readonly val: number        // 只读属性

      (source: string, subString: string): boolean;        // 参数名无需匹配，仅要求各位置参数类型匹配
    }
    let myObj = {size: 10, label: "Size 10 Object"};
    ```

### 类
- 继承
- 修饰符
    - public
        - 默认
    - private
        - 仅在声明类中可用
    - protected
        - 派生类中仍可使用
    - readonly
    - static
- 抽象类
    - abstract
- 构造函数
    - 关键词： constructor
    ```
    class Greeter {
        greeting: string;
        constructor(message: string) {
            this.greeting = message;
        }
    }
    ```

### 函数

### 泛型
```
function identity<T>(arg: T): T {
    return arg;
}
```

### 类型推论
- 根据最佳通用
- 根据上下文类型

### 类型兼容性
- 条件(A= B)
    - 对象
        - B 至少含有 A 中所有属性
    - 函数
        - B 中必需参数`类型` A 中均有满足
        - 允许`忽略参数`
    - 枚举
        - 不同枚举类型之间不兼容
    - 类
        - 仅比较`实例成员`，静态成员和构造函数不比较
        - 私有成员仅可来源于同类的对象的私有成员
    - 泛型
        - 仅定义结构使用类型参数时相同，都可理解为 `any`
        - 当定义泛型类型成员时，则会限制

### 高级类型
- 交叉类型
    - function extend<T, U>( first: T, second: U): T & U { ... }
- 联合类型-
    - function padLeft(value: string, padding: string | number) { ... }
- 类型保护与类型区分
    - if((<Fish>pet).swim)
    - typeof x=== "number"
    - x instanceof number
- 可辨识联合
    - 联合类型 + 属性可辨识 + 类型保护
    ```
    function area(s: Shape) {                                // 类型保护
        type Shape = Square | Rectangle | Circle;            // 联合类型
            interface Square { kind: "square"; size: number;}        // 属性可辨识，各类中均有同一属性
    ```

### Symbols
- 新原生类型
    - ECMAScript 2015 版本产生
    - 唯一且不可改变，理解有点像 UUID 用来定义唯一值
    - 创建
        - Symbol()
        - Symbol.for(string)
        - Symbol.iterator


### 迭代器和生成器
- for (let entry of someArray)
    - 迭代对象键对应值
- for (let i in list)
    - 迭代对象的键列表
```
let list = [4, 5, 6];
for (let i in list) console.log(i); // "0", "1", "2",
for (let i of list) console.log(i); // "4", "5", "6"
```

### 模块
- 默认&推荐 for Node.js 应用
- 导出
    - export class ZipCodeValidator implements StringValidator
    - export const numberRegexp
    - export { ZipCodeValidator as mainValidator };
    - export * from "./StringValidator";
    - export default $;        // 默认导出名称
- 导入
    - import { ZipCodeValidator } from "./ZipCodeValidator";
    - import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
    - import * as validator from "./ZipCodeValidator";

### 命名空间
- 定义
    - 位于全局命名空间下的普通带名字的 JavaScript 对象
    - 可在多文件同时使用，通过 `--outFile` 结合

### 命名空间和模块
```
// 不推荐，不应该对模块使用命名空间
export namespace Shapes {
    export class Triangle { /* ... */ }
}
import * as shapes from "./shapes";
let t = new `shapes.Shapes`.Triangle();

// 推荐
export class Triangle { /* ... */ }
import * as shapes from "./shapes";
let t = new shapes.Triangle();
```

### 模块解析
- 顺序
    - 优先尝试定位导入模块的文件
        - Classic 策略
        - Node 策略
    - 无果时尝试`外部模块声明`
- 方式
    - 相对
        - 以 `/`, `./`, `../` 开头
        - `import Entry from "./components/Entry";`
        - `import { DefaultHeaders } from "../constants/http";`
        - `import "/mod";`
    - 非相对
        - `import * as $ from "jQuery";`
- 策略
    - 介绍
        - 使用 `--moduleResolution` 指定策略
        - 使用 `--module AMD | System | ES2015` 默认为 `Classic`，其它情况为 `Node`
    - 分类
        - Classic
            - TypeScript `默认`解析策略，存在主要为了`向后兼容`
            - 相对导入
                - `/root/src/folder/A.ts` 中使用引用
                    - 引用 `import { b } from "./moduleB"`
                    - 查找
                        - `/root/src/folder/moduleB.ts`
                        - `/root/src/folder/moduleB.d.ts`
            - 非相对
                - 由包含导入文件的目录开始，依次向上级目录遍历
                - `/root/src/folder/A.ts` 使用引用
                    - 引用 `import { b } from "moduleB"`
                    - 查找
                        - /root/src/folder/moduleB.ts
                        - /root/src/folder/moduleB.d.ts
                        - /root/src/moduleB.ts
                        - /root/src/moduleB.d.ts
                        - /root/moduleB.ts
                        - /root/moduleB.d.ts
                        - /moduleB.ts
                        - /moduleB.d.ts
        - Node
            - 同 `Node.js`
            - 相对引用
                - 检查 `/root/src/moduleB.js`
                - 检查 `/root/src/moduleB/package.json`
                    - 如若包含 `{ "main": "lib/mainModule.js" }` 则使用 `/root/src/moduleB/lib/mainModule.js`
                - 检查 `/root/src/moduleB/index.js` ，并隐式当作 `main` 模块
            - 非相对引用
                - 在特殊目录 `node_modules` 中查找模块
                - 依次向上寻找目录下 `node_modules` 中的 `moduleB.tx`,`moduleB.tsx`,`moduleB.d.ts`, `moduleB.js`, `moduleB/package.json`, `moduleB/index.js `

### 声明合并

### JSX

### 装饰器

### Mixins

### 三斜线指令


## 补充
- 静态类型
    - 编译时确认变量类型，编译后类型信息和存储形式确定
- 安德斯·海尔斯伯格
    - C#的首席架构师 创始人
    - Delphi 创始人
    - Turbo Pascal 创始人
- 内部模块= 命名空间， 外部模块= 模块
