# Shell基础-test命令



| **类别**          | **测试条件**                             | **描述**                                    | **示例**                                              |
|-------------------|------------------------------------------|---------------------------------------------|-------------------------------------------------------|
| **文件测试**      | `-e filename`                            | 检查文件是否存在                           | `test -e filename` 或 `[ -e filename ]`              |
|                   | `-d filename`                            | 检查文件是否是目录                         | `test -d filename` 或 `[ -d filename ]`              |
|                   | `-f filename`                            | 检查文件是否是常规文件                     | `test -f filename` 或 `[ -f filename ]`              |
|                   | `-r filename`                            | 检查文件是否可读                           | `test -r filename` 或 `[ -r filename ]`              |
|                   | `-w filename`                            | 检查文件是否可写                           | `test -w filename` 或 `[ -w filename ]`              |
|                   | `-x filename`                            | 检查文件是否可执行                         | `test -x filename` 或 `[ -x filename ]`              |
|                   | `-s filename`                            | 检查文件是否为空（文件大小是否大于零）      | `test -s filename` 或 `[ -s filename ]`              |
|                   | `file1 -nt file2`                        | 检查 `file1` 是否比 `file2` 更新            | `test file1 -nt file2` 或 `[ file1 -nt file2 ]`      |
|                   | `file1 -ot file2`                        | 检查 `file1` 是否比 `file2` 旧              | `test file1 -ot file2` 或 `[ file1 -ot file2 ]`      |
| **字符串比较**    | `str1 = str2`                            | 检查两个字符串是否相等                     | `test "$str1" = "$str2"` 或 `[ "$str1" = "$str2" ]`  |
|                   | `str1 != str2`                           | 检查两个字符串是否不相等                   | `test "$str1" != "$str2"` 或 `[ "$str1" != "$str2" ]`|
|                   | `-z str`                                 | 检查字符串是否为空（长度为零）              | `test -z "$str"` 或 `[ -z "$str" ]`                  |
|                   | `-n str`                                 | 检查字符串是否非空（长度大于零）            | `test -n "$str"` 或 `[ -n "$str" ]`                  |
| **数字比较**      | `num1 -eq num2`                          | 检查两个数字是否相等                       | `test "$num1" -eq "$num2"` 或 `[ "$num1" -eq "$num2" ]`|
|                   | `num1 -ne num2`                          | 检查两个数字是否不相等                     | `test "$num1" -ne "$num2"` 或 `[ "$num1" -ne "$num2" ]`|
|                   | `num1 -lt num2`                          | 检查 `num1` 是否小于 `num2`                | `test "$num1" -lt "$num2"` 或 `[ "$num1" -lt "$num2" ]`|
|                   | `num1 -le num2`                          | 检查 `num1` 是否小于或等于 `num2`          | `test "$num1" -le "$num2"` 或 `[ "$num1" -le "$num2" ]`|
|                   | `num1 -gt num2`                          | 检查 `num1` 是否大于 `num2`                | `test "$num1" -gt "$num2"` 或 `[ "$num1" -gt "$num2" ]`|
|                   | `num1 -ge num2`                          | 检查 `num1` 是否大于或等于 `num2`          | `test "$num1" -ge "$num2"` 或 `[ "$num1" -ge "$num2" ]`|
| **复合条件**      | `condition1 && condition2`               | 当 `condition1` 和 `condition2` 都为真时，返回真  | `test -f file1 && test -r file1` 或 `[ -f file1 ] && [ -r file1 ]`|
|                   | `condition1 \|\| condition2`               | 当 `condition1` 或 `condition2` 为真时，返回真  | `test -f file1 \|\| test -f file2` 或 `[ -f file1 ] \|\| [ -f file2 ]`|
|                   | `! condition`                            | 当条件为假时返回真                         | `test ! -f file1` 或 `[ ! -f file1 ]`                |

