---
title: remix本地安装
date: 2023-04-06 20:07:54
tags: 
categories:
  - - Solidity
auto_excerpt: "true"
---
## **概论**

1. remix分为后端remix-projrct和前端remixd，后端负责编译等事务的实现，前端负责连接网页与本地文件
2. 先安装前端再安装后端
3. remix-project项目地址：https://github.com/ethereum/remix-project
4. remixd项目地址：https://github.com/ethereum/remixdb

---

## **部署**

1. 用docker部署remix-project，先安装：
   1. 正常来说是这样的命令：docker pull remixproject/remix-ide:latest
   2. 对mac，比较特殊，要加一点platform信息，运行这条即可：docker run -p 8080:80 --platform linux/amd64 remixproject/remix-ide:latest
2. 启动后台remix-project：docker run -p 8080:80 remixproject/remix-ide:latest
3. 此时能通过localhost:8080访问remixIDE，但是为了连接本地服务器需要安装前端remixd
4. npm安装remixd，安装过程会有几号之后只能用https的警告，只是npm的官方通知不用理会：npm install remixd -g
5. 启动remixd，其中中间-s的属性值是本地文件目录可以自行修改，-ide的属性值是访问网址，其中端口号要和安装remix-project时指定的端口相同：remixd -s /Users/dal/Downloads/solidity --remix-ide http://localhost:8080
6. 尽情使用吧，本地编辑完合约，然后打开网页部署，记得可以在docker客户端里检查remix-project的运行情况确保在后台运行才能打开IDE

---

## **运行**

1. 先去docker客户端里运行remixproject/remix-ide:latest
2. 然后启动remix，其中注意本地文件夹目录是可以修改的，其实要修改网址和端口号也是可以的，但是要在安装remix-project那步的时候准备好：remixd -s /Users/dal/Downloads/solidity --remix-ide http://localhost:8080
3. 访问remix本地客户端：http://localhost:8080
