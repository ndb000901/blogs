# TypeScript基础-命名

## 1. 限制

- **有效的字符**：
  - 字母（大小写均可）
  - 数字（但不能开头）
  - 下划线 `_`
  - 美元符号 `$`

## 2. 命名风格

| 类型         | 命名风格       | 示例          |
|--------------|------------|--------------|
| 变量         | camelCase  | `userName`, `count` |
| 函数         | camelCase  | `getUserName()` |
| 类           | PascalCase | `UserService` |
| 接口         | PascalCase（I开头可选）| `User`, `IUser` |
| 类型别名     | PascalCase | `UserType`  |
| 枚举         | PascalCase | `UserRole`  |
| 枚举成员     | UPPER_CASE | `USER_READ` |
| 常量         | UPPER_CASE | `MAX_RETRY_COUNT` |
| 泛型参数     | 单个大写字母     | `T`, `K`, `V`, `U` |
| 命名空间     | PascalCase | `MyNamespace` |
| 文件名       | kebab-case（推荐） | `user-service.ts` |

