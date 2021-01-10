[TOC]

# 1. git代码提交规范

[阮一峰提交规范文档](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)

[简书](https://www.jianshu.com/p/575430146302)

[参考](https://developer.aliyun.com/article/645092)  

##  1.目的 

- 让代码更容易维护

## 2.Angular规范

1. Git提交也有其规范，业内做的比较好的，比较具有参考价值的就是Angular的提交

   - Angular提交规范:

     ```js
     <type>(<scope>): <subject> #header
     // 空一行
     <body>
     // 空一行
     <footer> 
     ```
     
     ```js
     feat:新增财务报表
     ```
     
     

## 3. 格式讲解

1. header:包括三个字段：type（必需）、scope（可选）和subject（必需）
   - 关键就是header这部分，至于<body>和<footer>可省略

- #### type

  用于说明本次commit的类别，只允许使用下面7个标识

  - `feat`：新功能（feature）
  - `fix`：修补bug
  - `docs`：文档（documentation）
  - `style`： 格式（不影响代码运行的变动）
  - `refactor`：重构（即不是新增功能，也不是修改bug的代码变动）
  - `test`：增加测试
  - `chore`：构建过程或辅助工具的变动

