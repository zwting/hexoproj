---
title: Git学习
date: 2017-07-28 13:20:24
tags: 工具;版本管理
comment: false
---
### 一. 初始化

+ 初始化当前目录为一个git仓库:  `git init`
+ 设置git账户:

```git
git config --global user.name "You Name"
git config --global user.email "your email address"
```

### 二. 仓库操作

#### 1. 版本管理
+ 添加文件到暂存区: `git add [Files]`
+ 将暂存区的文件提交: `git commit -m ['msg']`

#### 2. 指定不纳入版本管理的文件: 
>在git仓库下打开.gitignore文件(Mac/Linux系统下这个文件是个隐藏的, 可以在终端中用命令调用文本编辑器来打开`vim .gitignore`), 顾名思义, 这个文件名字告诉我们就是用来忽略掉在版本管理中我们不关心的文件, 具体用法如下:
1. 在git中, 每一行代表一个用来匹配的模式
2. 空行不会匹配任何模式, 因此可以用来当做分割线使得.gitignore文件看起来更美观
3. 以#开头的是一个注释
4. 尾部的空格将会被忽略掉, 除非他们用反斜杠 "\" 括起来
5. 前缀"!" 代表一个取反的操作: 会消除前一个模式中指定的忽略的文件. 例如
`\*.swp`
`!lib.swp`
这个模式代表将会忽略掉不是以lib.swp结尾的所有.swp文件


+ 

#### 3. 仓库的创建
+ 克隆一个远端仓库到指定的目录中: `git clone ['url']`
+ 克隆一个远端仓库到已存在的目录中: `git clone ['url'] ['path']`

