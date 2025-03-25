# git-基础使用

## 1.初始化仓库(在现有文件夹下创建仓库)

该命令将创建一个名为 `.git` 的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这些文件是 Git 仓库的骨干。

```bash

git init
```

## 2.添加文件

```bash

# 请先新建文件
# echo "hello" >> readme.md

git add readme.md
```

## 3.提交文件

```bash

git commit -m "add readme.md"

# 用于修改最近一次提交（commit），可以更改提交信息，也可以把新的更改合并进上一次提交。
git commit --amend
```

## 4.克隆仓库(现有仓库)

```bash

# ssh
git clone git@github.com:libuv/libuv.git

# https
git clone https://github.com/libuv/libuv.git

# 自定义本地仓库名

git clone git@github.com:libuv/libuv.git mylibuv

```

## 5.文件状态

查看状态

```bash

git status
```

#### 输出示例

```bash

On branch dev
Your branch is up to date with 'origin/dev'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   spring-zookeeper/pom.xml
	new file:   spring-zookeeper/src/main/java/com/hello/demo/spring/zookeeper/config/AppConfig.java
	new file:   spring-zookeeper/src/main/java/com/hello/demo/spring/zookeeper/services/StatService.java
	new file:   spring-zookeeper/src/main/java/com/hello/demo/spring/zookeeper/services/ZNodeService.java
	new file:   spring-zookeeper/src/main/java/com/hello/demo/spring/zookeeper/utils/JsonUtils.java
	new file:   spring-zookeeper/src/main/resources/log4j2.xml
	new file:   spring-zookeeper/src/main/resources/zookeeper.properties
	new file:   spring-zookeeper/src/test/java/com/hello/demo/spring/zookeeper/test/StatDemo.java
	new file:   spring-zookeeper/src/test/java/com/hello/demo/spring/zookeeper/test/ZNodeDemo.java

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   .gitignore
	modified:   .idea/encodings.xml
	modified:   pom.xml
	modified:   spring-zookeeper/pom.xml
	modified:   spring-zookeeper/src/main/java/com/hello/demo/spring/zookeeper/services/StatService.java
	modified:   spring-zookeeper/src/main/java/com/hello/demo/spring/zookeeper/services/ZNodeService.java
	modified:   spring-zookeeper/src/main/java/com/hello/demo/spring/zookeeper/utils/JsonUtils.java
	modified:   spring-zookeeper/src/main/resources/zookeeper.properties
	modified:   spring-zookeeper/src/test/java/com/hello/demo/spring/zookeeper/test/StatDemo.java
	modified:   spring-zookeeper/src/test/java/com/hello/demo/spring/zookeeper/test/ZNodeDemo.java

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	.idea/inspectionProfiles/
	spring-zookeeper/src/main/java/com/hello/demo/spring/zookeeper/ZKDemo.java
```

### 按工作流程划分：
Git 管理代码的三个区域：

1. **工作区（Working Directory）**  
   👉 你正在编辑/修改的真实文件目录。

2. **暂存区（Staging Area / Index）**  
   👉 你使用 `git add` 之后，文件进入这里，准备提交。

3. **版本库（Repository）**  
   👉 执行 `git commit` 之后，文件正式进入版本库。

---

### 文件的具体状态类型：

| 状态 | 含义 |
|-------|-----------------------------|
| **Untracked（未跟踪）** | 文件在工作区中，Git 不知道它的存在（没执行过 `git add`）。 |
| **Tracked（已跟踪）** | 文件已被 Git 管理（即曾 `add` 过），包括下面几种： |
| └─ **Unmodified（未修改）** | 文件内容未变。 |
| └─ **Modified（已修改）** | 文件被修改了，但还未 `git add` 到暂存区。 |
| └─ **Staged（已暂存）** | 已使用 `git add`，修改内容已进入暂存区，等待提交。 |
| **Deleted（删除）** | 文件被删除，可是还没提交。 |


## 6.`.gitignore 文件`

[常用gitignore配置](https://github.com/github/gitignore)

用于忽略项目中不想git管理的文件

### `.gitignore` 文件位置  
- 通常放在 **项目根目录**（可以有多个 `.gitignore`，每个子目录也可以有自己的）。
- Git 会从当前目录开始向上查找 `.gitignore` 文件来决定忽略规则。

---

### 文件内容格式  
常见的写法如下：

```gitignore
# 注释：以 # 开头
*.log             # 忽略所有 .log 文件
*.class           # 忽略所有 .class 文件
target/           # 忽略整个 target 目录
.idea/            # 忽略 JetBrains 项目的配置目录
!keep.me          # 例外规则：即使被上面规则忽略，keep.me 仍然被跟踪
```

---

### 通配符说明：
| 写法 | 含义 |
|------|-----------------------------|
| `*`  | 匹配任意字符串 |
| `?`  | 匹配任意单个字符 |
| `**/` | 匹配任意深度目录（Git 2.0+） |
| `/`  | 指定目录路径 |
| `!`  | 取反，**强制包含某文件或目录** |

示例：
```gitignore
*.log            → 所有.log文件
/build/          → 忽略根目录下build目录
config/*.yml     → 忽略config目录下所有.yml文件
!/config/prod.yml → 但包含config/prod.yml
```

## 7.显示差异

```bash

# 工作区 vs 暂存区 查看你修改了哪些内容 但还没有 git add 的
git diff

# 暂存区 vs HEAD（上一次提交） 查看已经 git add 到暂存区的内容 但还没有 commit 的部分
git diff --staged
git diff --cached

```

## 8.移除文件

```bash


git rm README.md

# 如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 -f

git rm -f README.md

# 让文件保留在磁盘，但是并不想让 Git 继续跟踪

git rm --cached README.md
```


## 9.移动文件

```bash

git mv README.md readme.md

# 等价于
mv README.md readme.md
git rm README.md
git add readme.md
```

## 10.查看提交历史

```bash

git log
```

## 11.取消暂存文件

若不小心将readme.md文件放入暂存区(如使用`git add .`)，可以使用reset命令取消

```bash

git reset HEAD readme.md
```

## 12.撤销文件修改

```bash

# 高危操作，会导致修改进度丢失，除非你确认不需要
git checkout -- readme.md
```

## 13.远程仓库

### 查看远程仓库

```bash

git remote -v
```

### 添加远程仓库

```bash

# origin 是clone时默认远程仓库名字
# (origin 可用此标识代替url)
git remote add origin https://github.com/hello/hello.git

git fetch origin
```

### 从远程仓库拉取

```bash

# 只拉取，不合并
git fetch origin

# 自动的抓取然后合并远程分支到当前分支
git pull origin master
```

### 推送到远程仓库

```bash

git push origin master
```

### 重命名远程仓库

```bash

git remote rename origin hello
```

### 删除远程仓库

```bash

git remote rm origin
```

## 14.标签

- 轻量标签(lightweight): 只是一个指向提交的“引用”，就像一个分支指针。
- 附注标签(annotated): 一个完整的对象（tag object），可以包含：标签名、作者、时间、注释、甚至 GPG 签名。

### 查看标签

```bash

git tag
```

### 创建标签

```bash

git tag v1.0.0

# GPG签名
git tag -s v1.0.0 -m "发布版本 v1.0.0"
```

### 共享标签

`git push` 不会将标签推到远程

```bash

# 将本地标签推到远程
# git push origin [tagname]
git push origin v1.0.0
```

### 给过去记录打标签

```bash

git log --pretty=oneline

# 使用历史记录hash
git tag -a v1.2 9fceb02(hash)
```

## 15.分支

### 查看分支

```bash

git branch
```

### 新建分支

```bash

# git branch fix-xxx

# 创建一个新的分支
git checkout -b fix-xxx

# 等价 git checkout -b fix-xxx
git branch fix-xxx
git checkout fix-xxx
```

### 删除分支

```bash

git branch -d dev
```

### 切换分支

```bash

git checkout review
```

### 合并分支

```bash

# 切换到review分支
git checkout review

# 将dev分支合并到当前分支
git merge dev
```

### 变基

千万不要对已经 push 给别人的分支 rebase，否则会导致冲突或协作混乱。

```bash

git checkout dev
git rebase review

# git rebase [basebranch] [topicbranch]
git rebase review dev
```

### 追踪分支

Tracking branch（跟踪分支） 是一个 本地分支（local branch），它和一个 远程分支（remote branch） 建立了关联关系。

本地分支知道它“追踪”的是哪个远程分支。

这样可以让你：

- `git pull` 自动拉取更新

- `git push` 自动推送到对应远程分支

```bash
git checkout -b dev origin/dev

git checkout --track origin/dev

# 设置已有本地分支跟踪远程分支
git branch -u origin/dev
```

### 拉取分支

```bash

git fetch

# 等价 git fetch + git merge
git pull
```

### 删除远程分支

```bash

git push origin --delete dev
```



