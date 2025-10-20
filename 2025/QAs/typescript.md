

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