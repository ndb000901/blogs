# c++基础-bitset

## 1. `std::bitset`

`std::bitset<N>` 是一个**固定大小的位序列容器**，它在内存中以**位级别**高效表示和操作布尔值集合。

适合用于：
- 位图（bit map）表示；
- 内存优化（布尔值压缩存储）；
- 位运算操作（掩码匹配、高性能特性筛选）；

---

## 2. 基本语法

```cpp
#include <bitset>
#include <iostream>

std::bitset<8> bits("10101010");

std::cout << bits[0];       // 访问最低位（右边）
std::cout << bits.to_string(); // 转成字符串
```

---

## 3. 构造方式

```cpp
std::bitset<8> a;                     // 默认 00000000
std::bitset<8> b(42);                // 00101010，十进制 -> 二进制
std::bitset<8> c("1100");           // 00001100，字符串右对齐
```

---

## 4. 常用操作

| 方法 | 说明 |
|------|------|
| `set()` | 全部置 1 |
| `set(pos)` | 指定位 置 1 |
| `reset()` | 全部置 0 |
| `reset(pos)` | 指定位 置 0 |
| `flip()` | 所有位取反 |
| `flip(pos)` | 指定位取反 |
| `test(pos)` | 判断某位是否为 1 |
| `any()` | 是否有任何一位是 1 |
| `none()` | 是否全部为 0 |
| `count()` | 返回 1 的个数 |
| `size()` | 返回位数 |
| `to_ulong()` | 转为 unsigned long |
| `to_ullong()` | 转为 unsigned long long |
| `to_string()` | 转为字符串 |
| `operator[]` | 访问指定位置 |

---

## 5. 示例

```cpp
#include <bitset>
#include <iostream>

int main() {
    std::bitset<8> b("10101010");

    b.set(0);      // 最低位设为1 => 10101011
    b.reset(1);    // 第二位设为0 => 10101001
    b.flip(2);     // 第三位取反 => 10101101

    std::cout << "bitset: " << b << "\n";
    std::cout << "bit count: " << b.count() << "\n";
    std::cout << "any? " << b.any() << "\n";
    std::cout << "to_ulong: " << b.to_ulong() << "\n";
}
```

输出：

```
bitset: 10101101
bit count: 5
any? 1
to_ulong: 173
```

---

## 6.支持的运算符（类似整型）

可以用位运算符：

```cpp
std::bitset<8> a("10101010");
std::bitset<8> b("01010101");

auto c = a & b;  // 按位与
auto d = a | b;  // 按位或
auto e = a ^ b;  // 异或
auto f = ~a;     // 按位取反
```

还可以：
```cpp
a <<= 2;  // 左移 2 位
a >>= 1;  // 右移 1 位
```

---

## 7. 注意事项

| 注意点 | 说明 |
|--------|------|
| 固定大小 | `bitset<N>` 长度固定，不能动态扩容 |
| `to_ulong` 越界 | 超过 `unsigned long` 范围会抛异常 |
| 不能存 bool 数组 | `bitset` 是压缩的二进制位表示，不是 bool[] |
| 不能与动态 `vector<bool>` 混用 | `bitset` 是静态容器，`vector<bool>` 是动态位容器 |


