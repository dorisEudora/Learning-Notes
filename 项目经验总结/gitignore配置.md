# 配置gitignore的语法

## 前言：

.gitignore用来忽略git项目中一些文件，使得这些文件git识别和跟踪；

简单来说就是在gitignore添加某个文件之后，这个文件就不会上传到github

## .gitignore文件的格式规范如下：

- 所有空行或者#开头的行都会被git忽略
- 可以使用glob模式匹配
- 匹配模式可以以`/`开头防止递归
- 匹配模式可以以`/`结尾指定目录
- 要忽略指定模式以外的文件或者目录，可以在模式前加`!`取反

## 所谓的glob模式是指shell所使用的简化了的正则表达式

- 星号`*`匹配零个或多个任意字符
- [abc]匹配任何一个列在方括号当中的字符（这个例子要命匹配一个a，要么匹配b或者c）
- 问号（？）只匹配一个任意字符
- 如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到 9 的数字）。
- 使用两个星号（`**`) 表示匹配任意中间目录，比如`a/**/z`可以匹配 a/z, a/b/z 或 a/b/c/z

## 常用规则：

```.gitignore
/mtk/
#过滤整个mtk文件夹
*.zip
#过滤所有.zip文件
/mtk/do.c
#过滤/mtk/do.c文件

fd1/*
#忽略目录fd1下的全部内容

/fd1/*
#忽略根目录下的/fd1/目录的全部内容

!/fw/bin/
!/fw/sf/
#不忽略根目录下的 /fw/bin/ 和 /fw/sf/
```

## git 在添加.gitignore之前就push了项目

（避免冲突就可以先同步下远程仓库git pull）

1. 本地删除暂存区内容：git rm -r --cached
2. 新建.gitignore文件，并且添加过滤规则
3. git add .
4. git commit -m ""
5. git push origin

## 注意事项：

- 命令和注释别在同一行，如`*.txt #注释txt`这样会导致这一行无法识别
- git add .之前如果有改动.gitignore文件一定要执行git rm -r --cached
- 合理使用.gitignore可以避免无用文件的上传，也可以防止重要配置信息的泄露