### 目录

- [redis-cli客户端npm安装](#redis-cli客户端npm安装)
- [rdcli客户端连接](#rdcli客户端连接)



## redis-cli客户端npm安装

```bash
#先安装npm和node.js
brew install npm
brew install nod
```

```bash
#安装 redis-cli 客户端
npm install -g redis-cli

# 安装完成后，命令为 rdcli 
```

## rdcli客户端连接

```bash
# 连接命令
rdcli -h IP地址 -a 密码 -p 端口

#例子：
# IP 192.168.1.2 ，端口 6379 ， 密码 password
rdcli -h 192.168.1.2 -a password -p 6379 
```



