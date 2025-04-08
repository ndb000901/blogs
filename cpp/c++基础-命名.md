# c++基础-命名

## 1. 命名规则

- 一个标识符以字母 A-Z 或 a-z 或下划线 _ 开始，后跟零个或多个字母、下划线和数字（0-9）。

- 区分大小写字母

## 2. 参考

| 类型           | 推荐命名风格      | 示例                            | 说明                                        |
|----------------|-------------------|----------------------------------|---------------------------------------------|
| 变量名         | `camelCase`       | `userName`, `totalCount`        | 小写开头的驼峰式                            |
| 成员变量       | `_camelCase` 或 `mCamelCase` | `_count`, `mValue`         | 常用下划线或前缀 `m` 区分成员变量           |
| 全局变量       | `gCamelCase`      | `gConfigPath`                   | `g` 前缀表明是全局变量                      |
| 常量（变量）   | `kCamelCase` 或 `UPPER_CASE` | `kMaxRetries`, `MAX_BUFFER` | 两种风格常见，Google 更推荐 `kCamelCase`   |
| 枚举值         | `CamelCase` 或 `ALL_CAPS` | `ColorRed`, `RED`            | 依公司规范而异，现代 C++ 倾向前者           |
| 函数名         | `camelCase`       | `parseJson()`, `getName()`      | 动词+名词构成，表达动作                     |
| 类名 / 结构体  | `PascalCase`      | `UserManager`, `DataNode`       | 每个单词首字母大写                          |
| 接口类名       | `IPascalCase` 或 `PascalCase` | `IReader`, `Logger`     | 有些风格加 `I`，有些不加                   |
| 宏定义         | `ALL_CAPS`        | `#define MAX_SIZE 1024`         | 用于明显区分宏，避免命名冲突                |
| 命名空间       | `snake_case` 或 `camelCase` | `net_utils`, `mathOps`     | 多用于逻辑模块划分                          |
| 模板参数       | `PascalCase`      | `template<typename T>`          | 简洁命名：`T`, `U`, `Key`, `Value` 等       |
| 类型别名（using）| `PascalCase`     | `using StringList = std::vector<std::string>` | 像类型一样命名             |

