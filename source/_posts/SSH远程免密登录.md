title: SSH远程免密登录
author: wyding
tags:
  - Linux
  - SSH
categories: []
date: 2020-09-04 14:25:00
---
> `SSH`远程免密登录

1. 本地`ssh-keygen`生成密钥
2. 本地`cd ~/.ssh`，拷贝公钥`id_rsa.pub`
3. 登录服务器，将2中的公钥拷贝到`~/.ssh/authorized_keys`中去
4. 最后可以使用`ssh root@服务器IP`登录不需要填写密码即操作成功

> `Travis`命令行工具将`id_rsa.pub`加密，同时将环境变量传至`Travis`

1. 安装travis命令行工具，如无法使用gem指令须先安装ruby
`gem install travis`
2. --auto自动登录github帐号
`travis login --auto`
3. 此处的--add参数表示自动添加脚本到.travis.yml文件中, 这个命令会自动把 id_rsa 加密传送到 .git 指定的仓库对应的 travis 中去
`travis encrypt-file ~/.ssh/id_rsa --add`