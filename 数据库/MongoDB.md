[TOC]

# 1.Mac本机启动

1. cd /usr/local/bin 

2. mongod --dbpath /usr/local/var/mongodb --logpath /usr/local/var/log/mongodb/mongo.log --fork

   mongod ———启动服务器

   - ​		**--dbpath** 设置数据存放目录
   - ​		**--logpath** 设置日志存放目录
   - ​		**--fork** 在后台运行

3. 启动mongodb的数据端
   
   - 再运行mongo启动客户端就正常了

# 2.MongoDB命令集合

- 显示所有的数据库表:show dbs
- 查看当前所在的数据库：db
- 切换数据库表 use  数据库表名