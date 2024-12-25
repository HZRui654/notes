# Typescript

什么是 TypeScript ？

- 添加了类型系统的 JavaScript ，适用于任何规模的项目；
- 是一门静态类型、弱类型的语言；
- 是完全兼容 JavaScript 的，它不会修改 JavaScript 运行时的特性；
- 可以编译为 JavaScript，然后运行在浏览器、Node.js 等任何能运行 JavaScript 的环境中；
- 拥有很多编译选项，类型检查的严格程度由你决定；
- 可以和 JavaScript 共存，这意味着 JavaScript 项目能够渐进式的迁移到 TypeScript ；
- 增强了编辑器（IDE）的功能，提供了代码补全、接口提示、跳转到定义、代码重构等能力；
- 拥有活跃的社区，大多数常用的第三方库都提供了类型声明；
- 与标准同步发展，符合最新的 ECMAScript 标准（stage 3）。



## 安装编译

- **安装**

  `npm install -g typescript`

- **编译**

  `tsc hello.ts`

Typescript 只会在编译时对类型进行静态检查，如果发现错误，编译时就会报错。在运行时，与普通 Javascript 文件一样，不会对类型进行检查。**如果需要保证运行时的参数类型，还是得在代码中手动对类型进行判断**。

Typescript 编译报错时也会正常生成编译加过，可以在 tsconfig 设置 (`noEmitOnError`) 使其报错的时候终止 js 文件的生成。



## 基础

### 原始数据类型

- **boolean**

  ``` typescript
  let isDone: boolean = false
  ```
  
  注意：
  
  - 使用构造函数 `Boolean` （e.g `new Boolean(1)` ）创建的对象**不是 boolean 类型**，返回的是一个 `Boolean` 对象；
  - 而直接调用 `Boolean` （e.g `Boolean(1)`）返回的是一个 `boolean` 类型。
  
  > 在 TypeScript 中，`boolean` 是 Javascript 中的基本类型，而 `Boolean` 是 JavaScript 中的构造函数。其它基本类型（除了`null` 和`undefined` ）一样，不再赘述。

- **number** 

  ``` typescript
  let num: number = 6
  ```

- **string**

  ``` typescript
  let myName: string = 'Draven'
  ```

- **void**

  Javascript 没有空值（`void`）的概念，在 Typescipt 中可以用 `void` 代表没有任何返回值。
  
  ``` typescript
  function alerName(): void {
    alert('Draven')
  }
  ```
  
  声明一个 `void` 类型的变量没有什么用，因为最多**只能赋值** `undefined` 和 `null` （ tsconfig 未指定 `--strictNullChecks` ）。

- **null 和 undefined**

  ``` typescript
  let u: undefined = undefined
  let n: null = null
  ```
  
  与 `void` 的区别在于，`undefined` 和 `null` 是**所有类型的子类型**（ tsconfig 中 `strict` 为 false ，即非严格模式下 ）。
  ``` typescript
  // OK
  let num: number = undefined
   
  // Type 'void' is not assignable to type 'number'.
  num = void
  ```



### 任意值（ any ）

如果是一个普通类型，在赋值过程中改变类型是不被允许的。

但如果是任意值（ **`any`** 类型 ），则允许被赋值为任意类型。

可以理解为 `any` 类型完全不被类型检查。

- 在任意值上访问任何属性都是允许的，也允许调用任何方法。

  可以认为，**声明一个变量为任意值以后，对它的任何操作，返回的类型都是任意值**。

- **变量如果在声明的时候，没有赋值且未指定其类型，那么它会被识别为任意值类型**。



### unknow

类型安全的 `any` 。

与 `any` 相比，不可以赋值给其他变量，赋值给其他变量得先**检查类型**或者**类型断言**。

```typescript
let u: unknow
let s: string

// 检查类型
if (typeof u === 'string') {
  s = u
}

// 类型断言
s = u as string
s = <string>u
```

所以**有不确定类型的变量优先使用 `unknow` ，尽量避免使用 `any`  **。



### never

永不存在值。

never 是任何类型的子类型，也可以赋值给任何类型（非严格模式），然而，没有类型是 never 的子类型（除了其本身）。

变量也可能为 never，当它被永不为真的类型保护所约束。

一般用于函数，表示永远不会返回结果。

```typescript
// 程序执行到该函数就报错了，故函数不会有返回结果
function fn(): never {
  throw new Error('报错了！')
}

function fn2() {
   while(true) {}
}

```

故一般用抛出错误之类的函数，可以理解为 void 是返回了个 undefined ，而 never 是什么都没有返回。



### 类型推论

如果定义变量赋值时没有明确的指定类型，那么 TypeScript 会依照类型推论（ Type Interface ）的规则推断出一个类型。

``` typescript
let myString = 'my string'
// 等价于：let myString: string = 'my string'
```



### 联合类型

联合类型（ Union Types ）表示取值可以为多种类型中的一种。

使用 `|` 分隔每个类型。

``` typescript
let myNumber: string | number
myNumber = '1'
myNumber = 1
```

- **访问联合类型的属性或方法**：当 TypeScript 不确定到底是哪个类型的时候，**只能访问此联合类型所有类型的公共属性或方法**。

  ``` typescript
  function getString(something: string | number): string {
    return something.toString();
  }
  ```

- 联合类型的变量在被赋值时，会根据类型推论的规则推断出一个类型。



### 对象的类型（ interface ）

在 TypeScript 中，使用接口（ `interface` ）来定义对象的类型。

在面向对象语言中，接口是一个很重要的概念，它是对行为的抽象，而具体如何行动需要由类（ `class`  ）去实现（ `implement` ）。

但是在 TypeScript 中，接口是一个灵活的概念，除了对类的抽象以外，也常用于对 `对象的形状（Shape）` 进行描述。

- **简单例子**

  ``` typescript
  interface IPerson {
    name: string
    age: number
  }
   
  let tom: IPerson = {
    name: 'Tom',
    age: 26
  }
  ```
  
  接口一般首字母大写，**有的编程语言会建议接口的名称加上 `I`前缀 **。
  
  **赋值的时候，变量的形状必须与接口的形状保持一致，多与少一些属性都是不允许的**。

- **可选属性**

  ``` typescript
  interface IPerson {
    name: string
    age?: number
  }
   
  let tom: IPerson = {
    name: 'Tom'
  }
  ```
  
  可选属性代表该属性可以**不存在**，但是仍然**不允许添加未定义的属性**。

- **任意属性**

  ``` typescript
  interface IPerson {
    name: string
    age?: number
    [propName: string]: string ｜ number
  }
  
  let tom: IPerson = {
    name: 'Tom',
    gender: 'male'
  }
  ```
  
  使用 `[propName: string]: string | number` 定义了任意属性取 string | number 类型的值。
  
  **一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的子集**。比如此时 interface 新增一个属性，值类型为 boolean ，那么会报错。
  
  **一个接口只能定义一个任意属性**。如果想要在接口中有多个类型的属性，只能在任意属性中使用联合类型，使得包含多种类型。

- **只读属性**

  ``` typescript
  interface IPerson {
    readonly id: number
    name: string
    age?: number
    [propName: string]: string ｜ number
  }
  
  let tom: IPerson = {
    id: 1,
    name: 'Tom',
    gender: 'male'
  }
  ```
  
  **字段只能在创建的时候被赋值**。



### 数组类型

数组有多种定义方式。

- **类型 + 中括号 `type[]` **

  ``` typescript
  let arr: number[] = [1, 1, 2, 3]
  ```
  
  数组的项中**不允许**出现其他类型。

- **数组泛型（ Array Generic ）`Array<type>`** 

  ``` typescript
  let arr: Array<number> = [1, 1, 2, 3]
  ```

- **接口（ interface）**

  ``` typescript
  interface INumberArray {
    [index: number]: number
  }
   
  let arr: INumberArray = [1, 1, 2, 3]
  ```
  
  `INumberArray` 表示只要索引的类型是数字时，那么值的类型必须是数字。
  
  一般不用这个方式来描述数组，因为比前两种复杂很多。只有一种情况例外，就是用来表示类数组。

- **类数组（ Array-like Object ）**

  不是数组类型，例如 `arguments` ：
  
  ``` typescript
  function sum() {
    // Error
    // let args: number[] = arguments
  
    // OK
    let args: {
      [index: number]: number
      length: number
      callee: Function
    } = arguments
  }
  ```
  
  这个例子中，约束了当索引的类型是数字时，值的类型必须是数字以外，还约束了有 `length` 和 `cakkee` 两个属性。
  
  事实上，**常用的类数组 TypeScript 都已经定义好了类型**，例如：`IArguments` ，`NodeList` ，`HTMLCollection` 等。



### 函数类型

在 JavaScript 中，有两种常见的定义函数的方式：

- 函数声明（ Function Declaration ）
- 函数表达式（ Function Expression）

``` javascript
// 函数声明（ Function Declaration ）
function sum(x, y) {
  return x + y
}

// 函数表达式（ Function Expression）
let mySum = function (x, y) {
  return x + y
}
```

Typescript 对函数进行约束时，需要把输入和输出都考虑到。

**输入多余的（或者少于要求的）参数，是不被允许的**。

- **函数声明**

  函数声明的类型定义比较简单。
  
  ``` typescript
  function sum(x: number, y: number): number {
    return x + y
  }
  ```

- **函数表达式**

  ``` typescript
  let mySum: (x: number, y: number) => number = function (x, y) {
    return x + y
  }
  ```

- **接口定义函数的形状**

  ``` typescript
  interface IMySum {
    (x: number, y: number): number
  }
  
  let mySum: IMySum = function (x, y) {
    return x + y
  }
  ```

- **可选参数**

  与接口的可选属性类似，使用 `?` 表示可选参数：
  
  ``` typescript
  function buildName(firstName: string, lastName?: string): string {
    return lastName ? firstName + lastName : firstName
  }
  
  let tomCat = buildName('Tom', 'cat')
  let tom = buildName('Tom')
  ```
  
  **可选参数后面不允许再出现必需参数**。

- **剩余参数**

  ES6 可以使用 `...rest` 的方式获取函数中的剩余参数（ rest 参数 ）：
  
  ``` typescript
  function push(arr: any[], ...items: any[]) {
    items.forEach((item) => {
      arr.push(item)
    })
  }
  ```
  
  **rest 参数只能是最后一个参数。**
  
  当可选参数和剩余参数同时出现的时候，需要可选参数写在前面，并加一个 undefined 默认值，否则可选参数会变必需参数。

- **重载**

  重载允许一个函数接受不同数量或类型的参数时，作出不同的处理。
  
  利用联合类型（ Union Types ）其实也可以做到，但是有一个缺点，当输出是联合类型时，表达不够精确。例如想要在入参是 number 时，输出也限制是 number ，这种情况下联合类型就无法满足。可以使用重载：
  
  ``` typescript
  function reverse(x: number): number
  function reverse(x: string): string
  function reverse(x: number | string): number | string | void {
    if (typeof x === 'number') {
      return Number(x.toString().split('').reverse().join(''))
    } else if (typeof x === 'string') {
      return x.split('').reverse().join('')
    }
  }
  ```
  
  上面的例子中，重复定义了 reverse 函数，前几次都是函数定义，最后一次是函数实现。
  
  **TypeScript 会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面**。



### 类型断言

类型断言（ Type Assertion ）可以用来手动指定一个值的类型。

**类型断言只能够欺骗 TypeScript 编译器，无法避免运行时的错误，所以不要滥用。**

- **语法**

  - `值 as 类型`
  - `<类型>值`
  
  在 tsx 语法中，必须使用前者。`<类型>` 在 ts 也还可以表示一个泛型。所以在使用类型断言时，建议**统一使用 `值 as 类型` 的语法**。

- **用途**

  - **将联合类型断言为其中一个类型**
  
    联合类型只能访问此联合类型所有类型的公共属性或方法，使用类型断言可以在**还不确定类型的时候就访问其中一个类型特有的属性或方法**。
  
    ``` typescript
    interface Cat {
      name: string;
      run(): void;
    }
    interface Fish {
      name: string;
      swim(): void;
    }
    
    function isFish(animal: Cat | Fish) {
      if (typeof (animal as Fish).swim === 'function') {
        return true;
      }
      return false;
    }
    ```
  
  - **将一个父类断言为更加具体的子类**
  
    一般是在 TS 的接口类型下使用，如果是 JS 的类，用 `instanceof` 更合适。
  
    ``` typescript
    interface ApiError extends Error {
      code: number;
    }
    interface HttpError extends Error {
      statusCode: number;
    }
     
    function isApiError(error: Error) {
      if (typeof (error as ApiError).code === 'number') {
        return true;
      }
      return false;
    }
    ```
  
  - **将任何一个类型断言为 any**
  
    **它极有可能掩盖了真正的类型错误，所以如果不是非常确定，就不要使用 `as any`** 。
  
    将一个变量断言为 `any` 可以说是解决 TypeScript 中类型问题的最后一个手段。
  
    我们需要在**类型的严格性和开发的便利性之间掌握平衡**，才能发挥出 TypeScript 最大的价值。
  
    假设需要在 window 中临时引入新变量 foo ，但是 TypeScript 编译时会报错，提示属性不存在，可以临时使用 `as any` 将 window 断言为 any 类型。
  
    ``` typescript
    (window as any).foo = 1
    ```
  
  - **将 `any` 断言为一个具体的类型**
  
    在日常开发中，由于历史遗留或其他人编写的烂代码等，不可避免的要处理 `any` 类型的变量。
  
    遇到 `any` 类型，可以选择无视，人有它滋生更多 `any` ，也可以使用类型断言，把类型为  `any` 的变量断言成一个更精确的类型。

- **限制**

  并不是任何一个类型都可以被断言为任何另一个类型。
  
  要使得 `A` 能够被断言为 `B` ，只需要 `A` 兼容 `B` 或 `B` 兼容 `A` 即可。
  
  **TypeScript 是结构类型系统，类型的对比只会比较它们最终的结构，而会忽略它们定义时的关系**。
  
  假设 A 为 B 的父类，由上方用途可得，A 可以被断言为 B，又 B 作为 A 的子类，一定包含 A 的所有属性，所以 B 也可以断言为 A 。

- **双重断言**

  使用双重断言 `as unknow as 类型` ，可以打破类型断言的限制。
  
  但是若使用了这种断言，十有八九是非常错误的，很可能导致运行时错误。
  
  **除非迫不得已，千万别用双重断言**。



### 声明文件

我们通常会把声明语句放到一个单独的文件（`*.d.ts`）中，这就是声明文件。

声明文件里的内容在编译结果中会被删除。

**声明文件必须以 `.d.ts` 为后缀**。

- **书写声明文件**

  当第三方库没有提供声明文件时或者自己有拓展的需求，就需要自己写声明文件。
  
  如果声明文件没有生效，请检查 `tsconfig.json` 中的 `files`、`include` 和 `exclude` 属性。
  
  声明语句中只定义类型，切勿在声明语句中定义具体的实现。
  
  使用关键字 `declare` 进行声明（ `interface` 和 `type`  不需要 ）。
  
  为了**防止命名冲突**，应该要减少全局变量或全局类型的数量，最好将他们放到 `namespace` 下。
  
  - **全局变量**：`declare var/let/const`
  
    ``` typescript
    declare let jQuery: (selector: string) => any
    ```
  
  - **全局方法**：`declare function`
  
    函数型的声明语句，也支持重载。
  
    ``` typescript
    declare function jQuery(selector: string): any
    declare function jQuery(calback: () => any): any
    ```
  
  - **全局类**：`declare class`
  
    ``` typescript
    declare class Animal {
      name: string
      constructor(name: string)
      sayHi(): string
    }
    ```
  
  - **全局枚举类型**：`declare enum`
  
    与其它全局变量的类型声明一致，仅用来定义类型，而不是具体的值。
  
    ``` typescript
    declare enum Directions {
      Up,
      Down,
      Left,
      Right
    }
    ```
  
  - **命名空间（含有子属性的全局对象）**：`declare namespace`
  
    `namespace` 是 TS 早期为了解决模块化而创造的关键字。TS 一开始使用 `module` 关键字，但是后来 ES6 也使用 `module` ，就改为使用 `namespace` 。随着现在 ES6 的广泛使用，不再需要学习 `namespace`  的使用了。
  
    **`namespace` 被淘汰了，不建议再使用，推荐使用 ES6 的模块化方案**。
  
    但是**在声明文件中，`namespace` 还是比较常用的**，用来表示一个全局变量是一个对象，包含很多子属性。
  
    ``` typescript
    // 在 declare namespace 内部，声明不需要再加 declare 关键字。
    declare namespace jQuery {
      function ajax(url: string, settings?: any): void
      const version: number
  
      // 嵌套命名空间
      namespace fn {
        function extend(object: any): void
      }
    }
    ```
  
    ``` typescript
    // 假设 jQuery 下仅有 fn 属性，则不需要嵌套命名空间。
    declare namespace jQuery.fn {
      function extend(object: any): void
    }
    ```
  
  - **全局类型**：`interface` / `type`
  
  - **声明合并**：
  
    假设 jQuery 既是一个函数，又是一个拥有子属性的对象，那么我们可以**组合多个声明语句**，他们会不冲突的组合起来。
  
    ```typescript
    // jQuery('#foo') jQuery.ajax()
    declare function jQuery(selector: string): any
    declare namespace jQuery {
      function ajax(url: string, settings?: any): void
    }
    ```
  
    **直接拓展全局变量**
  
    ``` typescript
    interface String {
      prependHello(): string
    }
  
    'foo'.prependHello()
    ```
  
  - **ES6 默认导出**：`export default`
  
  - **commonjs 模块导出**：`export =`
  
  - **UMD 库全局变量**：`export as namespace`
  
  - **拓展全局变量**：`declare global`
  
  - **拓展模块**：`declare module`
  
    **模块插件**：有时通过 `import` 导入一个模块插件，可以改变另一个原有模块的结构。此时如果原有模块已经有类型声明文件，但是插件没有，会导致类型不完整。此时可以使用 TS 提供的 `declare module` 语法来拓展原有模块的类型。
  
    如果需要拓展原模块，只需要在类型声明文件中引用原模块，再使用 `declare module` 拓展。
  
    ``` typescript
    // types/moment-plugin/index.d.ts
  
    import * as moment from 'moment'
  
    declare module 'moment' {
      export function foo(): moment.Calendarkey
    }
    ```
  
    也可用于在一个文件中一次性声明多个模块的类型。
  
    ``` typescript
    // types/foo-bar.d.ts
  
    declare module 'foo' {
      export interface Foo {
        fop: string
      }
    }
  
    declare module 'bar' {
      export function bar(): string
    }
    ```
  
  - **三斜线指令**：`/// <reference />`
  
    与 `namespace` 类似，三斜线指令也是 TS 在早期版本为了描述模块之间依赖关系而创造的。随着 ES6 的广泛应用，已经**不再建议使用**。
  
    但是在声明文件中，还有一定用武之地。
  
    注意：**三斜线指令必须放在文件的最顶端，前面只允许单行或多行注释**.
  
    与 `import` 类似，但是在以下几个场景下，需要使用三斜线指令替代：
  
    - **当书写一个全局变量的声明文件**：
  
      在书写全局变量的声明文件时，不允许出现 `import` 和 `export` 关键字，否则会被视为 npm 包或 UMD 库。
  
      ``` typescript
      // types/jquery-plugin/index.d.ts
  
      // <reference types='jquery' />
  
      declare function foo(options: jQuery.AjaxSettings): string
      ```
  
    - **需要依赖一个全局变量的声明文件**：
  
      由于全局变量不支持通过 `import` 引入，所以必须使用三斜线指令引入。
  
      ``` typescript
      // types/node-plugin/index.d.ts
  
      // <referenc types="node" />
  
      export function foo(p: NodeJS.Process): string
      ```
  
    - **拆分声明文件**：
  
      当全局变量的声明文件太大，可以拆分为多个文件，然后在一个入口文件统一引入，提高代码的可维护性。比如 jQuery 的声明文件。
  
      ``` typescript
      // node_modules/@types/jquery/index.d.ts
      
      /// <reference types="sizzle" />
      /// <reference path="JQueryStatic.d.ts" />
      /// <reference path="JQuery.d.ts" />
      /// <reference path="misc.d.ts" />
      /// <reference path="legacy.d.ts" />
      
      export = jQuery
      ```
  
      其中用到了 `types` 和 `path` 两种指令。它们的区别：`types` 用于声明对另一个库的依赖，而 `path` 用于声明对另一个文件的依赖。

- **npm 包**

  一般我们通过 `import foo from 'foo'` 导入一个 npm 包，这是符合 ES6 模块化规范的。
  
  当尝试给一个 npm 包创建声明文件之前，需要先看看它的声明文件是否存在。一般来说可能存在于两个地方：
  
  - 与 npm 包绑定在一起。判断依据是 `package.json` 中有 `types` 字段，或者有一个 `index.d.ts` 声明文件。这种模式下不需要安装其它包，是最推荐的。所以在我们自己创建 npm 包的时候，最好也**将声明文件与 npm 包绑定在一起**。
  
  - 发布到 `@types` 里，我们只需要**安装一下对应的 `@types` 包就知道是否存在该声明文件**。这种模式一般是 npm 包的维护者没有提供声明文件，所以只能由其他人将声明文件发布到 `@types` 里了。
  
    ``` bash
    npm i @/types/jquery -D
    ```
  
  假如以上两种方式都没有找到对应的声明文件，那么我们就需要自己写声明文件了。由于是通过 `inport` 语句导入的模块，所以声明文件存放的位置也有所约束，一般有两种方案：
  
  - 创建一个 `node_modules/@types/jquery/index.d.ts` 文件，存放 `jquery` 模块的声明文件。这种模式不需要额外的配置，但是 `node_modules` 目录不稳定，代码也没有保存到仓库中，有被删除的风险，**故不建议用这种方案**，一般**只用作临时测试**。
  
  - 创建一个 `types` 目录，专门用来管理自己写的声明文件。将 `jquery` 的声明文件放到 `types/jquery/index.d.ts` 中。这种方式需要配置下 `tsconfig.json` 中的 `paths` 和 `baseUrl` 字段。 
  
    `tsconfig.json`：
  
    ``` json
    {
      "complierOptions": {
        "module": "commonjs",
        "baseUrl": "./",
        "paths": {
          "*": ["types/*"]
        }
      }
    }
    ```
  
    如此配置后，通过 `import` 导入后，也会去 `types` 目录下寻找对应模块的声明文件了。
  
    可以简单地声明一个模块去解决编辑器的报错提示：
  
    ``` typescript
    declare module 'jquery'
    ```

  **npm 包声明文件语法**：
  
  - **`export`**
  
    npm 包的声明文件与全局变量的声明文件有很大的区别。在  npm 包的声明文件中，使用 `declare` 不会再声明一个全局变量，而是在当前文件声明一个局部变量。**只有在声明文件中使用 `export` 导出，然后在使用方 `import` 导入后，才会应用这些类型声明**。
  
    `export` 语法与普通 TS 中的语法类似，区别仅在禁止定义具体实现。
  
    ``` typescript
    // types/foo/index.d.ts
  
    export const name: sring
    export function getName(): string
    ```
  
    ``` typescript
    // 导入
    import { name, getName } from 'foo'
    ```
  
    也可以使用 `declare` 声明多个变量后，再 `export` 一次性导出。
  
    ``` typescript
    declare const name: sring
    declare function getName(): string
  
    export { name, getName }
    ```
  
  - **`export default`**
  
    在 ES6 模块系统中，使用 `export default` 可以导出一个默认值，使用方可以用 `import foo from 'foo'` 而不是 `import { foo } from 'foo'` 来导入这个默认值。
  
    **只有 `function`， `class` 和 `interface` 可以直接默认导出，其它的变量需要先定义完再默认导出**。
  
    针对这种默认导出，我们一般会**将导出语句放在整个声明文件的最前面**。
  
  - **`export =  `**
  
    在 commonjs 规范中，按一下方式导出一个模块：
  
    ``` typescript
    // 整体导出
    module.exports = foo
  
    // 单个导出
    exports.bar = bar
    ```
  
    在 TS 中，针对这种模块导出，有以下几种导入方式：
  
    ``` typescript
    // const ... = require
  
    // 整体导入
    const foo = require('foo')
  
    // 单个导入
    const bar = require('foo').bar
    ```
  
    ``` typescript
    // import ... from
  
    // 整体导入，需要使用 import * as 
    import * as foo from 'foo'
  
    // 单个导入
    import { bar } from 'foo'
    ```
  
    ``` typescript
    // TS 官方推荐
    // import ... require 
  
    // 整体导入
    import foo = require('foo')
  
    // 单个导入
    import bar = require('foo').bar
    ```
  
    对于 commonjs 规范的库，书写声明文件就需要使用 `export = ` 。
  
    ``` typescript
    // types/foo/index.d.ts
  
    export = foo
  
    declare function foo(): string
    declare namespace foo {
      const bar: number
    }
    ```
  
    准确的讲，`export = ` 不仅可以用在声明文件，也可以用在普通的 TS 文件中。实际上 `import ... require` 和 `export = ` 都是 TS 为了兼容 AMD 规范和 commonjs 规范而创立的新语法，**不常用也不推荐使用**。
  
    由于很多第三方库是 commonjs 规范，所以不得不用，但还是**更推荐使用 ES6 标准**。
  
  - **`export as namespace`**
  
    **UMD 库**：即可以通过 `<script>` 标签引入，又可以使用 `import` 引入的库。相比于 npm 包的类型声明文件，需要额外声明一个全局变量，为了实现这种方式，ts 提供了一个新语法 `export as namespace` 。
  
    一般使用时，都是先有了 npm 包的声明文件，在此基础上添加一条 `export as namespace` 语句，即可将声明好的一个变量声明为全局变量。
  
    ``` typescript
    // types/foo/index.d.ts
  
    export as namespace foo
    export = foo
  
    declare function foo(): string
    declare namespace foo {
      const bar: number
    }
    ```
  
  - **`declare global`**
  
    可以在 npm 包或者 UMD 库的声明文件中拓展全局变量的类型。
  
    由于 npm 包或者 UMD 库的声明文件只有在被导入时才会生效，不能直接拓展全局变量，所以当导入此库后需要拓展全局变量时，需要使用另一种语法在声明文件中拓展。
  
    ``` typescript
    // types/foo/index.d.ts
    
    declare global {
      interface String {
        prependHello(): string
      }
    }
    
    export {}
    ```
  
    注意，**即时此声明文件不需要导出任何东西，仍然需要导出一个空对象，用来告诉编译器这是一个模块的声明文件，而不是一个全局变量的声明文件**。

- **自动生成声明文件**

  如果库的源码本身就是 TS 写的，那么在编译（ `tsc` ）时，添加 `declaration` 选项，就可以同时生成 `.d.ts` 声明文件了。
  
  可以在命令行添加 `--declaration`（ 简写 `-d` ），或者在 `tsconfig.json` 添加配置。
  
  ``` json
  {
   "compilerOptions": {
       "module": "commonjs",
       "outDir": "lib",
       "declaration": true,
   }
  }
  ```
  
  上例中，`outDir` 代表编译结果输出到 `lib` 目录。设置 `declaration` 为 `true` ，表示会自动生成 `.d.ts` 声明文件到 `lib` 目录下。
  
  自动生成的声明文件基本保持了源码的结构，而讲具体实现去掉了，生成了对应的类型声明。
  
  每个 ts 文件都会单独对应 `.d.ts` 声明文件。这样的好处在于，使用方不仅可以使用 `import foo from 'foo'` 导入默认模块时获得提示，也可以使用 `import bar from 'foo/lib/bar'` 导入子模块时也可以获得提示。 
  
  还有几个选项与自动生成声明文件有关：
  
  - `declarationDir` ：设置生成 `.d.ts` 文件的目录；
  - `declarationMap` ：对每个 `.d.ts` 文件，都生成对应的 `.d.ts.map`（ sourcemap ）文件；
  - `emitDeclarationOnly` ：仅生成 `.d.ts` 文件，不生成 `.js` 文件。



### 内置对象

JS 有很多内置对象，它们可以直接在 TS 中当作定义好了的类型。

内置对象是指根据标准在全局作用域（ Global ）上存在的对象。这里的标准是指 ECMAScript 等的标准。

- **ECMAScript**

  ECMAScript 标准提供的内置对象有：
  
  `Boolean`、 `Error`、 `Date`、 `RegExp` 等。

- **DOM 和 BOM**

  `Documnet`、 `HTMLElement`、 `Event`、 `NodeList` 等。

TypeScript 核心库已经定义了以上所有浏览器环境需要用到的类型，并且预置在 TypeScript 中的。

**TypeScript 核心库的定义不包含 Node.js 部分，所以想用 TS 写 Node ，需要引入第三方声明文件。**

``` bash
npm install @types/node -D
```



## 进阶

### 类型别名

用来给一个类型起个新名字，**使用 `type` 定义**。

``` typescript
type Name = string
type NameResolve = () => string
type NameOrResolve = Name | NameResolve
function getName(n: NameOrResolve): Name {
  return typeof n === 'string' ? n : n()
}
```



### 字符串字面量类型

用来约束取值只能是某几个字符串中的一个，**使用 `type` 定义**。

``` typescript
type EventNames = 'click' | 'scroll' | 'mousemove'
function handleEvent(ele: Element, event: EventNames) {
  // do something
}

handleEvent(document.getElementById('hello'), 'scroll') // OK
handleEvent(document.getElementById('world'), 'doclick') // Error
```



### const

`as const` 或 `<const>` ，将变量的类型设置为其值的字面量。

``` typescript
const s = 'test' // s is 'test'
let s = 'test' // s is string
let s = 'test' as const // s is 'test'

const Color = {
  Read: "r",
  Write: "w",
  Execute: "x"
} as const

type ColorKey = keyof typeof Color // 'read' | 'write' | 'execute'
type ColorValue = typeof Color[colorKey] // 'r' | 'w' | 'x'


let a = ['white', 'yellow'] as const
typeof a[number] // 'white' | 'yellow' 得到对应的字面量联合类型
```



### 元组（ Tuple ）

数组合并了相同类型的元素，而元组合并了不同类型的元素。

``` typescript
let tom: [string, number] = ['Tom', 25]
```

**当添加越界元素时，它的类型会被限制为元组中每个类型的联合类型。**



### 枚举（ Enum ）

用于取值被限定在一定范围内的场景。

``` typescript
// 枚举成员会被赋值为从 0 开始递增的数字，同时也会对枚举值到枚举名进行反向映射。
enum Days {
  Sun,
  Mon,
  Tue,
  Wed,
  Thu,
  Fri,
  Sat
}
console.log(Days['Sun'] === 0) // true
console.log(Days[0] === 'Sun') // true

// 事实上，上面例子会被编译如下
var Days
;(function(Days) {
  Days[Days['Sun'] = 0] = 'Sun'
  Days[Days['Mon'] = 1] = 'Mon'
  Days[Days['Tue'] = 2] = 'Tue'
  Days[Days['Wed'] = 3] = 'Wed'
  Days[Days['Thu'] = 4] = 'Thu'
  Days[Days['Fri'] = 5] = 'Fri'
  Days[Days['Sat'] = 6] = 'Sat'
}(Days || Days = {}))
```

- **手动赋值**

  ``` typescript
  enum Days {
    Sun = 3,
    Mon = 1,
    Tue,
    Wed,
    Thu = 1.5,
    Fri,
    Sat = <any>'S'
  }
  
  // 未手动赋值的枚举项会接着上一个枚举项递增
  console.log(Days['Sun'] === 3) // true
  console.log(Days['Wed'] === 3) // true
  
  // 如果未手动赋值的枚举项与手动赋值的重复，TS 不会察觉
  // Days[3] 值先是 'Sun' ，然后被 'Wed' 覆盖
  console.log(Days[3]) // 'Wed'
  
  // 可以为小数或负数，手续未手动赋值的项递增步长依旧为 1 
  console.log(Days['Fri']) // 2.5
  
  // 可以不是数字，但是可能需要使用类型断言（实际测试应该不用）
  console.log(Days['Sat']) // 'S'
  ```

- **常数项和计算所得项**

  枚举项有两种类型：常数项（ constant member）和计算所得项（ computed member ）。
  
  ``` typescript
  // Color.Blue 就是一个计算所得项
  enum Color {
    Red,
    Green,
    Blue = 'blue'.length
  }
  ```
  
  **如果紧接在计算所得项后面的是未手动赋值的项，那么它就会因为无法获取初始值而报错**。

- **常数枚举（ `const enum` ）**

  ``` typescript
  const enum Directions {
    Up,
    Down,
    Left,
    Right
  }
  
  // 事实上，上面例子会被编译如下
  var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */]
  ```
  
  与普通枚举的区别：**会在编译阶段被删除，不能包含计算成员**。

- **外部枚举（ `declare enum` ）**

  ``` typescript
  declare enum Directions {
    Up,
    Down,
    Left,
    Right
  }
  
  // 事实上，上面例子会被编译如下
  var directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right]
  ```
  
  `declare` 定义的类型**只会用于编译时的检查，编译结果会被删除**。
  
  同时使用 `declare` 和 `const` 也是可以的：`declare const enum`

**注意**：

如果可以的话，建议使用`联合类型`或者`常数枚举`，使用这种两种方式编译后不会有冗余代码；

如果不行，建议使用 `{} as const` 代替枚举，至少比起枚举少了反向代理的部分并且有迭代需求时更加方便。



### 类

传统方法中，JS 通过构造函数实现类的概念，通过原型链实现继承。而在 ES6 中，迎来了 `class` 。

TS 实现了所有 ES6 中类的功能，还添加了一些新用法。

- **概念**

  - 类（ Class ）：定义了一件事物的抽象特点，包含它的属性和方法。
  - 对象（ Object ）：类的实例，通过 `new` 生成。
  - 面向对象（ OOP - Object-oriented programming ）的三大特性：封装、继承、多态。
  - 封装（ Encapsulation ）：将数据的操作细节隐藏起来，只暴露对外的接口。外界调用端不需要（也不可能）知道细节，就能通过对外提供的接口来访问该对象，同时也保证了外界无法任意更改对象内部的数据。
  - 继承（ Inheritance ）：子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性。
  - 多态（ Polymorphism ）：由继承而产生了相关的不同的类，对同一个方法可以有不同的响应。
  - 存取器（ getter & setter ）：用以改变属性的读取和赋值行为。
  - 修饰符（ Modifiers ）：修饰符是一些关键字，用于限定成员或类型的性质。
  - 抽象类（ Abstract Class ）：抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现。
  - 接口（ Interface ）：不同类之间公有的属性或方法，可以抽象成一个接口。接口可以被类实现（ implements ）。一个类只能继承自另一个类，但是可以实现多个接口。 

- **ES6 中类的用法**

  - **属性和方法**
  
    使用 `class` 定义类，使用 `constructor` 定义构造函数。
  
    通过 `new` 生成新实例的时候，会自动调用构造函数。
  
    ``` javascript
    class Animal {
      name
      constructor(name) {
        this.name = name
      }
      sayHi() {
        return `My name is ${this.name}`
      }
    }
  
    let a = new Animal('Jack')
    console.log(a.sayHi()) // My name is Jack
    ```
  
  - **继承**
  
    使用 `extends` 关键字实现继承，子类中使用 `super` 关键字来调用父类的构造函数和方法。
  
    ``` javascript
    class Cat extends Animal {
      constructor(name) {
        super(name)
        console.log(this.name)
      }
      sayHi() {
        return `Meow, ${super.sayHi()}`
      }
    }
  
    let c = new Cat('Tom')
    console.log(c.sayHi()) // Meow, My name is Tom
    ```
  
  - **存取器**
  
    使用 `getter` 和 `setter` 可以改变属性的赋值和读取行为。
  
    ``` javascript
    class Animal {
      constructor(name) {
        this.name = name
      }
      get name() {
        return 'Jack'
      }
      set name(value) {
        console.log('setter: ' + value)
      }
    }
  
    let a = new Animal('Kitty')
    a.name = 'Tom' // setter: Tom
    console.log(a.name // Jack
    ```
  
  - **静态方法**
  
    使用 `static` 修饰符修饰的方法称为静态方法，它们不需要实例化，而是直接通过类来调用。
  
    ``` javascript
    class Animal {
      static isAnimal(a) {
        return a instanceof Animal
      }
    }
    
    let a = new Animal('Jack')
    Animal.isAnimal(a) // true
    a.isAnimal(a) // TypeError: a.isAnimal is not a function
    ```

- **ES7 中类的用法**

  ES7 中有一些关于类的提案，TS 实现了它们。
  
  - **实例属性**
  
    ES6 中实例的属性只能通过构造函数中的 `this.xxx` 来定义，ES7 提案中可以直接在类里面定义。
  
    ``` typescript
    class Animal {
      name = 'Jack'
  
      constructor() {
        // ...
      }
    }
  
    let a = new Animal()
    console.log(a.name) // Jack
    ```
  
  - **静态属性**
  
    ES7 提案中，可以使用 `static` 定义一个静态属性。
  
    ``` typescript
    class Animal {
      static name = 'Jack'
    
      constructor() {
        // ...
      }
    }
    
    console.log(Animal.name) // Jack
    ```

- **TS 中类的用法**

  TS 中可以使用三种访问修饰符：`public`、 `private` 和 `protected` 。
  
  - `public` 修饰的属性或方法是公有的，可以在任何地方被访问到，**默认所有属性和方法都是 `public` 的**。
  
  - `private` 修饰的属性和方法是私有的，**不能在声明它的类的外部访问**。
  
    在子类中也是不允许访问的。
  
    如果构造函数修饰为 `private` ，则该类不允许被继承或者实例化。
  
    注意：TS 编译之后的代码，并不会限制 `private` 属性在外部的可访问性。
  
  - `protected` 修饰的属性或方法是受保护的，与 `private` 类似，区别是**在子类中允许被访问**。
  
    如果构造函数修饰为 `protected` 时，该类只允许被继承。
  
- **参数属性**

  修饰符和 `readonly` 还可以使用在构造函数参数中，等同于类中定义该属性同时给该属性赋值，使代码更简洁。
  
  ``` typescript
  class Animal {
    // public name: string
    constructor(public name) {
     // this.name = name
    }
  }
  ```
  
  `readonly` ：只读属性关键字，只允许出现在属性声明、索引签名或构造函数中。
  
  如果 `readonly` 和其它访问修饰符同时存在的话，需要写在其后面。
  
  ``` typescript
  class Animal {
  	constructor(public readonly name) {}
  }
  ```

- **抽象类**

  `abstract` 用于定义抽象类和其中的抽象方法。
  
  ``` typescript
  abstract class Animal {
    constructor(public name) {}
    abstract sayHi()
  }
  ```
  
  抽象类不允许被实例化。
  
  抽象类中的抽象方法必须被子类实现。
  
  注意：即使是抽象类，TS 的编译结果中仍然会存在这个类。



### 类与接口

接口（ Interfaces ）除了可以用于对「对象的形状（ Shape）」进行描述，还可以对类的一部分行为进行抽象。

- **类实现接口**

  一般来讲，一个类只能继承自另一个类，有时候不同类之间可以有一些共有的特性。这时候就可以把特性提取成接口（ Interfaces ），用 `implements` 关键字来实现。
  
  一个类可以实现多个接口。
  
  ``` typescript
  interface Alarm {
  	alert(): void
  }
  interface Light {
    lightOn(): void
    lightOff(): void
  }
  
  class Car {}
  class MyCar extends Car implements Alarm, Light {
    alert() {
     console.log('Car alert')
    }
    lightOn() {
     console.log('Car light on')
    }
    lightOff() {
     console.log('Car light off')
    }
  }
  ```

- **接口继承接口**

  ``` typescript
  interface Alarm {
    alert(): void
  }
  // lightableAlarm 除了拥有 alert 方法外，又多了两个新方法。
  interface lightableAlarm extends Alarm {
    lightOn(): void
    lightOff(): void
  }
  ```

- **接口继承类**

  ``` typescript
  class Point {
  	constructor(public x: number, public y: number) {}
  }
  interface Point3d extends Point {
  	z: number
  }
  
  let point3d: Point3d = {
    x: 1,
    y: 2,
    z: 3
  }
  ```
  
  当声明 `class Point` 时，除了会创建一个名为 `Point` 的类之外，同时也创建了一个名为 `Point` 的类型（实例的类型）。
  
  实例的类型 `Point` 是不包含构造函数、静态属性和静态方法的，只包含实例属性和实例方法。



### is

`is` 类型谓词，用于判断变量属于某个类型。

``` typescript
// 判断参数是否为 string 类型, 返回布尔值
function isString(s: unknown): s is string {
  return typeof s === 'string'
}
```

常用的判断方式还有：

- 对象 `obj` 中是否包含属性 `a` ： ` 'a' in obj`
- 检测构造函数的 `prototype` 属性是否出现在某个实例对象的原型链上：`子类B instanceof 父类A` 



### 泛型（ Generics ）

在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

``` typescript
function createArray<T>(length: number, value: T): Array<T> {
  let result: T[] = []
  for (let i = 0; i < length; i++) {
    result[i] = value
  }
  return result
}

createArray(3, 'x') // ['x', 'x', 'x']
```

- **多个类型参数**

  ``` typescript
  function swap<T, U>(tuple: [T, U]): [U, T] {
    return [tuple[1], tuple[0]]
  }
  
  swap([7, 'seven']) // ['seven', 7]
  ```

- **泛型约束**

  ``` typescript
  // 使用 extends 约束泛型必须满足某个形状
  // 多个类型参数之间也可以互相约束
  function copyFields<T extends U, U>(target: T, source: U): T {
    for (let id in source) {
      target[id] = (<T>source)[id]
    }
    return target
  }
  
  let x = { a: 1, b: 2, c: 3, d: 4 }
  
  copyFields(x, { b, 10, d: 20 })
  ```

- **泛型接口**

  ``` typescript
  interface CreateArrayFunc<T> {
    (length: number, value: T): Array<T>
  }
  
  let createArray: CreateArrayFunc<any>
  createArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = []
    for (let i = 0; i < length; i++) {
      result[i] = value
    }
    return result
  }
  
  createArray(3, 'x') // ['x', 'x', 'x']
  ```

- **泛型类**

  ``` typescript
  class GenericNumber<T> {
    zeorValue: T
    add: (x: T, y: T) => T
  }
  
  let myGenericNumber = new GenericNumber<number>()
  myGenericNumber.zeorValue = 0
  zeorValue.add = (x, y) => x + y
  ```

- **泛型参数的默认类型**

  ``` typescript
  // 可以为泛型中的类型参数指定默认类型，当使用泛型没有直接指定类型参数，以及从实际值也无法推测出时，这个默认类型就会起作用。
  function createArray<T = string>(length: number, value: T): Array<T> {
    let result: T[] = []
    for (let i = 0; i < length; i++) {
      result[i] = value
    }
    return result
  }
  ```



### this

函数中的 this 将作为第一个参数被传入，可以指定 this 的类型。

``` typescript
function testThis(this: any, s: string) {
  console.log(s)
}

testThis('this') // 'this'
```

又或者在以下场景：

父类的某个函数返回值使用了 this ，然后子类继承后进行调用。

在该场景下就可以在父类的函数中指定 this 的类型为父类。



### 声明合并

如果定义了两个相同的函数、接口或类，那么它们会合并成一个类型。

**合并的属性的类型必须是唯一的**。



### 标记提示

`/** */`给类型做标记提示

``` typescript
/** point 3d */
interface Point3d extends Point {
  z: number
}
```



### tsconfig.json

TS 编译器的配置文件，TS 编译器可以根据它的信息来对代码进行编译。

如果是 monorepo ，可以将项目的 tsconfig 提取为以下三个公共配置。

`base.json` ：项目基础配置

``` json
{
  "$schema": "https://json.schemastore.org/tsconfig", // 指定当前 JSON 的规范
  "compilerOptions": { // TS 编译的配置
    "outDir": "dist", // 编译后文件的输出文件夹名称
    "target": "ESNext", // 要编译的目标 JS 环境
    "module": "ESNext", // 当前使用的模块化规范
    "moduleResolution": "node16", // TS 解析模块的策略
    "resolveJsonModule": true, // 支持导入 JSON 文件，以及 JSON 文件的类型检查
    "strict": true, // 开启类型检查的严格模式
    "verbatimModuleSyntax": true, // 如果导入/导出时没有加 type 关键字，都会被编译到最终文件中
    "useDefineForClassFields": true, // 指定在编译时使用 ECMAScript 类的新特性来定义类字段
    "esModuleInterop": true, // 用于控制模块之间的导入和导出的转换方式，在使用不同模块系统（比如 CommonJS 和 ES modules）之间进行互操作时，确保代码能够正确地导入和导出模块
    "allowSyntheticDefaultImports": true, // 与 esModuleInterop: true 配合允许从 commonjs 的依赖中直接按 import XX from 'xxx' 的方式导出 default 模块
    "forceConsistentCasingInFileNames": true, // 引入文件时区分大小写
    "isolatedModules": true, // 可以告诉你，你编写的代码在单文件转译过程中可能会出错
    "sourceMap": false, // 不生成 source map 文件
    "allowJs": false, // 不允许引入 .js 文件
    "noUnusedLocals": true, // 定义的变量没有被使用会报错
    "removeComments": true, // 编译后删除注释
    "noEmit": true, // 不输出编译后的源码、声明、source maps 等文件 
    "skipLibCheck": true, // 假定第三方引入库的声明都是正确的，跳过对引入的库文件的类型检查
    "types": [] // 不设置会引入任何文件层级 @type 下的类型声明，设置后可以指定要引入的声明。置为空数组是为了不乱引入声明
  }
}
```

`vue-app.json` ：vue app 配置

``` json
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "extends": ["./base.json"], // 继承其它配置
  "compilerOptions": {
    "target": "ESNext",
    "lib": ["DOM", "DOM.Iterable", "ES2020"],
    "jsx": "preserve", // 解析 JSX 语法的模式。preserve 代表原封不动的转换成 .jsx 文件，可以用于其它工具下一步解析
    "jsxImportSource": "vue", // 指定 JSX 中使用的 React 函数的导入源
  }
}
```

`node.json` ： node 配置（不在浏览器环境下运行的代码），需要安装 `@types/node`

``` json
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "extends": ["./base.json"],
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022"],
  	"types": ["node"]
  }
}
```

Vue 项目根目录：

``` json
// tsconfig.json
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "files": [],
  "references": [ // 可以将 ts 项目模块化打包（ tsc --build ），不同的文件使用不同的配置打包
    {
      "path": "./tsconfig.app.json"
    },
    {
      "path": "./tsconfig.node.json"
    }
  ]
}
```

``` json
// tsconfig.app.json
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "extends": ["@draven/ts-config/vue-app.json"],
  "include": ["src/**/*", "src/**/*.vue", "types/*.d.ts"],
  "compilerOptions": {
    "composite": true, // 使用 reference 时必须打开该配置，如果 rootDir 没有明确配置，会只想 tsconfig.json 所在目录
    "baseUrl": ".",
    "tsBuildInfoFile": "./node_modules/.tmp/tsconfig.app.tsbuildinfo", // 相对 tsconfig 所在目录，缓存打包结果，提高打包效率
    "paths": { 
      "@/*": ["./src/*"]
    }
  }
}
```

``` json
// tsconfig.node.json
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "extends": ["@draven/ts-config/node.json"],
  "include": ["vite.config.ts"],
  "compilerOptions": {
    "composite": true,
    "tsBuildInfoFile": "./node_modules/.tmp/tsconfig.node.tsbuildinfo"
  }
}
```



## 常用工具类型（Utility Types）

### Awaited

`Awaited<Type>`  会解析出异步函数的类型。

``` typescript
// type A = string
type A = Awaited<Promise<string>>

// 递归的也会解析到最里层
//  type B = number 
type B = Awaited<Promise<Promise<number>>>

// type C = boolean | number
type C = Awaited<boolean | Promise<number>>
```



### Partial

`Partial<Type>` 将类型的所有属性变成可选。

```typescript
interface User {
  name: string
  age: number
}

const u: Partial<User> = {
  name: 'hello'
}
```



### Required

`Required<Type>` 将类型的所有属性变成必选。

```typescript
interface User {
  name?: string
  age?: number
}

const u: Required<User> = {
  name: 'hello'
} // Error，缺少属性 age 。
```



### Readonly

`Readonly<Type>` 将数组或对象类型的属性值变为只读的。

```typescript
interface User {
  name: string
  age: number
}

const u: Readonly<User> = {
  name: 'hello',
  age: 10
}
u.age = 19 // Error. 初始化后不能再修改 u 的属性值。
```



### Record

`Record<Keys, Type>` 定义键值对的形状，`Keys` 为键的类型，`Types` 为值的类型。

```typescript
type Property = 'user1' | 'user2'
type User = Record<Property, string>

const u: User = {
  user1: 'xuexi',
  user2: 'Draven'
}
```



### Pick

`Pick<Type, Keys>` 从类型 `Type` 中挑出属性为 `Keys` 的键。

```typescript
interface User = {
  name: string
  age: number
  gender: string
}
// type U = { name: string, age: number }
type U = Pick<User, 'name' | 'age'>

const u: U = {
  name: 'hello',
  age: 10
}
```



### Omit

`Omit<Type, Keys>` 从类型中 `Type` 移除类型为 `Keys`的键 。

```typescript
interface User = {
  name: string
  age: number
  gender: string
}
// type U = { name: string }
type U = Omit<User, 'age' | 'gender'>

const u: U = {
  name: 'hello'
}
```



### Exclude

`Exclude<UnionType, ExcludedMembers>` 移除联合类型 `UnionType` 中的类型 `ExcludedMembers` 。

```typescript
// type U = 'number' | 'boolean'
type U = Exclude<'string' | 'number' | 'boolean', 'string'>
```



### Extract

`Extract<Union, Type>`  提取联合类型 `Union` 中的类型 `Type`。

```typescript
// type U = () => void
type U = Extract<string | number | (() => void), Function>
```



### NonNullable

`NonNullable<Type>` 去除类型 `Type` 中的 `null` 和 `undefined` 。

```typescript
// type U = string[]
type U = NonNullable<string[] | null | undefined>
```



### Parameters

`Parameters<Type>` 获取函数 `Type` 的参数类型所组成的元组类型。

```typescript
// type U = [number, string]
type U = Parameters<(a: number, b: string) => void>
```



### ConstructorParameters

`ConstructorParameters<Type>` 获取构造函数 `Type` 的参数类型所组成的元组类型。

```typescript
// type U = [message?: string]
type U = ConstructorParameters<ErrorConstructor>
```



### ReturnType

`ReturnType<Type>`获取函数 `Type` 的返回值类型。

```typescript
// type U = string
type U = ReturnType<(value: string) => string>
```



### InstanceType

`InstanceType<Type>`获构造取函数 `Type` 的实例类型。

```typescript
class C {
  x = 0
  y = o
}

// type U = C
type U = InstanceType<typeof C>
```



## 疑难杂症

### 让对象的值类型根据其键的字面量动态变化

``` typescript
interface TypeX {
  a: number
  b: string
  c: boolean
}

interface Y<T> {
  value: T
}

type TypeXY<T extends keyof TypeX> = {
  [P in T]?: Y<TypeX[P]>
}

function processTypex<T extends keyof TypeX>(typeXObj: TypeXY<T>) {}

// 当 typeXObj 写完 a 时，值就被限定成 Y<Typex['a']> 了
processTypeX({ a: { value: 1 } })
```



### 如果类型是 A ，就绝不可能是 B

``` typescript
type Without<T, U> = {
  [P in Exclude<keyof T, keyof U>]?: never
}
type XOR<T, U> = (T | U) extends object
	? (Without(T, U) & U) | (Without<U, T> & T)
	: T | U
```

延伸一下，可以用于限制以下场景：

- **对象中要不有属性 a ，要不有属性 b ，不能同时有**

  ``` typescript
  // 常见路由场景，如果有 path 就不能有 children
  type Option = { name: string } & XOR<{ path: string }, { children: Option[] }>
  ```

- **对象中某些属性要不都有，要不就都没有**

  ``` typescript
  type Obj = { a: string } & XOR<{}, { b: string, c: number }>
  ```

- **互斥类型大于两个**

  ``` typescript
  type Test = XOR<A, XOR<B, C>>
  ```



### 通用类型保护函数

``` typescript
// 只要 target 中有 prop 属性，那么 target 就是 T 类型
function isOfType<T>(target: unknow, prop: keyof T): target is T {
  return (target as T)[prop] !== undefined
}
```



