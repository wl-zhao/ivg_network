# 实验室连接服务器指南

> created by @赵文亮 and @于旭敏

## 简介

为了提高网络安全性，目前实验室服务器网络做了如下改动

1. 改为使用跳板机连接，即网关服务器不再开放端口转发，而是通过 ssh 的 proxy jump 来连接内部的服务器
2. 对服务器设置白名单，只有经过检查后确认没有挖矿程序的服务器才开放访问权限

## 连接方式

### 服务器授权

请服务器管理员将服务器的 root 账号和密码私信给管理员 @赵文亮/@于旭敏，管理员检查服务器是否包含挖矿程序，检查合格后将该服务器的 ip 添加到白名单

### SSH 客户端授权与配置

同学们需要将 ssh key 发送给管理员，管理员会将其加入到跳板机的 authorized_keys 中

#### MacOS/Linux

如果之前没有生成过 ssh key，可以通过如下命令生成

```
ssh-keygen
```

一路按回车，最后在 `~/.ssh` 中生成 ssh key，将公钥复制出来

```
cat ~/.ssh/id_rsa.pub
```

私信给管理员即可


接下来有两种方法可以帮助连接服务器/主机
##### 1、配置Config文件(推荐)

需要在 `~/.ssh/config` 配置跳板机，打开该文件，添加如下配置（以 229 服务器为例）

```
Host 192.168.2.* 
    ProxyJump ivg_jump

Host ivg_jump
    Hostname ivg-lab.dynv6.net
    Port 2222
    User ivg_jump

Host server229
    Hostname 192.168.2.229
    User zwl
    ProxyJump ivg_jump
```

即可通过 `ssh server229` 来从本地访问服务器, 同时可以通过`scp -r $FILENAME server229:/path/to/dst`进行文件传输，如果是服务器之间的传输，和以前的用法无异。

##### 2、不配置Config文件
每次可以通过
```
ssh -J ivg_jump@ivg-lab.dynv6.net:2222 $USER@192.168.2.229
scp -r -J ivg_jump@ivg-lab.dynv6.net:2222 $FILENAME $USER@192.168.2.229:/path/to/dst
```
来从本地电脑访问服务器和传输文件


#### Windows
为了避免不必要的工作量，我们强烈推荐 Windows 用户使用 [MobaXterm](https://mobaxterm.mobatek.net/) 进行配置。在 MobaXterm 中打开 local terminal 后的配置方法与 MacOS/Linux 上完全相同。


