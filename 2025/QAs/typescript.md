
## TypeScript 中 interface 和 type 的区别吗？
核心区别在于：

1. interface 支持声明合并，同一个接口可以多次定义并自动合并，这在扩展第三方类型时很有用。
2. type 更灵活，可以定义联合类型、元组、映射类型等复杂类型结构。
3. 在扩展方式上，interface 用 extends，type 用交叉类型 &。

"interface 更适合声明和扩展对象结构，type 更适合类型运算和复杂类型。"

## ReturnType 的作用和用法
ReturnType 是一个内置的工具类型，用于提取函数的返回值类型。它可以自动推断并返回函数的返回值类型，无需手动手动编写重复的类型定义
```
const calculate = (a: number, b: number) => a + b;

// 提取返回值类型（number）
type Result = ReturnType<typeof calculate>; // Result = number
```

ReturnType 的核心价值是自动同步函数返回值类型，尤其适合以下场景：

- 函数返回值类型复杂，避免手动重复定义。
- 函数返回值可能频繁修改，通过 ReturnType 确保依赖其类型的地方自动更新。
- 在类型层面复用函数返回值结构，提升代码可维护性。

## extends 条件类型定义
```
type TypeName<T> =
  T extends string ? "string" :
    T extends number ? "number" :
      T extends boolean ? "boolean" :
        "unknown";

let type1: TypeName<string>;  // 类型为 "string"
let type2: TypeName<number>;  // 类型为 "number"
let type3: TypeName<boolean>; // 类型为 "boolean"
let type4: TypeName<object>;  // 类型为 "unknown"
```

## is关键字
is 是 TypeScript 中的一个关键字，用于创建类型保护。在 TypeScript 中，类型保护是一种用于确定变量是否符合某种类型的方法。当我们使用 is 关键字创建一个类型保护时，它会在运行时对变量进行判断，然后返回一个布尔值。
在复杂联合类型或 unknown 类型中尤为重要。
```
function isString(value: unknown): value is string {
  return typeof value === 'string';
}

```
value is string 就是类型谓词（Type Predicate）

表示：如果函数返回 true，TypeScript 会将传入的 value 视为 string 类型

## infer
在 TypeScript 中，infer 是一个用于条件类型中的关键字。它的作用是从待推断的类型中提取特定的类型，并将其赋值给一个类型变量。这个类型变量可以在条件类型的 true 分支中使用。
```
type ParamType<T> = T extends (param: infer P) => any ? P : never;

function foo(arg: number): void {
  // ...
}

type FooParam = ParamType<typeof foo>; // FooParam 的类型是 number
```

## in
在 TypeScript 中，in 是一个运算符，用于检查对象是否具有指定的属性或者类实例是否实现了指定的接口。
```
interface Person {
  name: string;
  age: number;
}

function printPersonInfo(person: Person) {
  if ('name' in person) {
    console.log('Name:', person.name);
  }
  if ('age' in person) {
    console.log('Age:', person.age);
  }
}

let person = { name: 'Alice', age: 25 };
printPersonInfo(person); // 输出: Name: Alice, Age: 25
```
