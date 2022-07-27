# golang开发流程

## 一、数据库

### 1.设计数据库E-R图

使用dbdiagram.io在线数据库模型设计工具设计出E-R图，并导出相应建表SQL语句。

### 2.部署数据库

使用数据库docker镜像，部署一个数据库容器(mysql, postgresql)。

使用dbeaver, navicat等数据库管理工具连接部署好的数据库。

### 3.数据库迁移

安装golang migrate(具体见github，https://github.com/golang-migrate/migrate)

golang migrate CLI使用文档(https://github.com/golang-migrate/migrate/tree/master/cmd/migrate)

