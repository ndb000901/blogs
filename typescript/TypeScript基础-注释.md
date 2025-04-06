# TypeScript基础-注释


## 1. **单行注释和多行注释**

```ts
// 这是单行注释

/*
  这是多行注释，
  通常用于解释一段逻辑
*/
```

---

## 2. **JSDoc 注释（推荐用于函数、类、接口等）**
### JSDoc 是 TS 中最推荐的注释方式，支持类型提示、代码补全、文档生成

### 函数示例：

```ts
/**
 * 计算两个数的和
 * @param a 第一个数字
 * @param b 第二个数字
 * @returns 两个数字的和
 */
function add(a: number, b: number): number {
  return a + b;
}
```

> VSCode 或 WebStorm 等编辑器会自动读取 `@param`、`@returns` 提示用户函数功能。

---

## 3. **常用 JSDoc 标签说明**

| 标签 | 说明 |
|------|------|
| `@param` | 参数说明 |
| `@returns` 或 `@return` | 返回值说明 |
| `@deprecated` | 表示函数/变量不建议再用（会有警告） |
| `@example` | 给出一个调用例子 |
| `@throws` | 说明函数可能抛出的异常 |
| `@type` | 给变量注解类型（多用于 JS 中） |
| `@typedef` | 定义类型别名（JS 中用的多） |
| `@see` | 提供参考链接或文档 |

---


## 4. **特殊用途注释**
### TypeScript 本身提供一些特殊注释用于控制类型检查行为：

| 注释 | 用途 |
|------|------|
| `// @ts-ignore` | 忽略下一行的 TS 错误（慎用） |
| `// @ts-expect-error` | 明确期望下一行有错误（TS 3.9+） |
| `// @ts-check` | 在 JS 文件中开启 TS 类型检查 |
| `// @ts-nocheck` | 关闭整个文件的类型检查 |
| `// @ts-expect-error` | 如果下一行没有错误，TS 会报错（保证错误存在） |

```ts
// @ts-ignore
const x: number = "this is not a number"; // 不会报错，但不推荐！
```
