# git 问题汇总

## 1.中文文件名乱码

编码设置

```bash

# 查看编码设置
git config --global i18n.logOutputEncoding
git config --global i18n.commitEncoding

# 设置编码设置

git config --global i18n.logOutputEncoding UTF-8
git config --global i18n.commitEncoding UTF-8

```


```bash

# git status
new file:   "java/java\345\237\272\347\241\200-lambda\350\241\250\350\276\276\345\274\217.md"
new file:   "java/java\345\237\272\347\241\200-\344\277\256\351\245\260\347\254\246.md"

# 使用命令
git config --global core.quotepath false
```