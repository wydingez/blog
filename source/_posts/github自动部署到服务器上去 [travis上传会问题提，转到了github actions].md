title: Github可持续集成服务 【Travis上传会问题提，转到了Github Actions】
author: wyding
tags:
  - Linux
  - SSH
  - 自动化部署
categories: []
cover: 'https://cdn.wyd94.top/82d25598828c702b5f85a725bcd09b60.jpg'
date: 2020-09-04 14:25:00
---
> `github` + `travis` / `github action`可持续化集成

<!-- more -->

## 1. **SSH**远程免密登录

1. 本地`ssh-keygen`生成密钥
2. 本地`cd ~/.ssh`，拷贝公钥`id_rsa.pub`
3. 登录服务器，将2中的公钥拷贝到`~/.ssh/authorized_keys`中去
4. 最后可以使用`ssh root@服务器IP`登录不需要填写密码即操作成功

## 2. **travis**

### 2.1 **Travis**命令行工具将**id_rsa.pub**加密，同时将环境变量传至**Travis**

```shell
# 安装travis命令行工具，如无法使用gem指令须先安装ruby
gem install travis
# --auto自动登录github帐号
travis login --auto
# 此处的--add参数表示自动添加脚本到.travis.yml文件中
travis encrypt-file ~/.ssh/id_rsa --add
# 这个命令会自动把 id_rsa 加密传送到 .git 指定的仓库对应的 travis 中去
```

### 2.2 在**.travis.yml**里的**before_install**周期中自动多了下面这2行

```shell
- openssl aes-256-cbc -K $encrypted_97d432d3ed20_key -iv $encrypted_97d432d3ed20_iv
  -in id_rsa.enc -out ~\/.ssh/id_rsa -d
```

### 2.3 **after_success**周期中，添加上传服务器的指令即可

```shell
# 没有修改过端口的，可以用这个，上传目录要加 -r 参数
- scp -o stricthostkeychecking=no -r 要上传的文件或目录 用户@域名或IP:/路径
# 由于我修改了默认的port，所以在这里也进行了加密处理
- scp -o stricthostkeychecking=no -P $PORT -r 要上传的文件或目录 用户@域名或IP:/路径
```

### 2.4 发现**scp**上传文件老是上传完后文件目录为空
[travis失败可能性原因（**Warning: Permanently added '119.45.214.222' (ECDSA) to the list of known hosts.**）](https://stackoverflow.com/questions/58422338/travis-ci-only-uploads-empty-files-from-the-build-script)

## 3. **Github action**
**Github actions** 配置：
```yml
# This is a basic workflow to help you get started with Actions

name: Deploy

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the master branch
on:
  push:
    branches: [ master ]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build-and-deploy:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - name: Checkout
        uses: actions/checkout@v2
      
      - name: Setup Node.js environment
        uses: actions/setup-node@v2.1.0
        with:
          node-version: '12.x'

      # Runs a single command using the runners shell
      - name: Build
        run: npm i && npm run build

      # Runs a set of commands using the runners shell
      - name: Deploy to Server
        uses: hengkx/ssh-deploy@v1.0.1
        with: # 以下为参数
          USERNAME: ${{ secrets.DEPLOY_USER }} 
          PASSWORD: ${{ secrets.DEPLOY_PASSWORD }}
          HOST: ${{ secrets.DEPLOY_HOST }}
          SOURCE: 'public'
          TARGET: '/home/wyd/project/blog/'
```

其中的**secrets.DEPLOY_USER** / **secrets.DEPLOY_PASSWORD** / **secrets.DEPLOY_HOST**为一些密钥，可以在**Repository** => **Settings** => **Secrets** 中设置。

## 4. **Github action**使用**SSH**自动登录部署失败后的修改
配置如下，需要将生成的私钥添加到**Settings => Secrets**中
```yml
- name: Deploy to Server
        uses: easingthemes/ssh-deploy@v2.1.5
        with: # 以下为参数
          SSH_PRIVATE_KEY: ${{ secrets.SERVER_SSH_KEY }}
          ARGS: "-rltgoDzvO --delete"
          SOURCE: "public/"
          REMOTE_HOST: ${{ secrets.DEPLOY_HOST }}
          REMOTE_USER: ${{ secrets.DEPLOY_USER }}
          TARGET: ${{ secrets.REMOTE_TARGET }}
```

### ① 创建一个新的 **ssh key pair**（任意一台机器都行）
```bash
cd ~/.ssh
ssh-keygen -f github-deploy-key
```

### ② 把 **public key** 加入到 **authorized_keys** 里面
```bash
cat ~/.ssh/github-deploy-key.pub >> ~/.ssh/authorized_keys
```

### ③ 修改 **ssh folder** 权限
```bash
sudo chmod 700 -R ~/.ssh
```

### ④ 设置 **ssh** 配置文件，打开密钥登录功能
```bash
sudo nano /etc/ssh/sshd_config

RSAAuthentication yes
PubkeyAuthentication yes
```

### ⑤ 重启 **ssh** 服务
```bash
service sshd restart
```

### ⑥ 复制 **private key 到 GitHub repo -> setttings -> secrets**
```bash
cat ~/.ssh/github-deploy-key
```

## 参考

- [1] [Luna Tech | 使用 GitHub Action 自动部署博客到远程服务器](https://blog.lunawen.com/posts/20200628-luna-tech-github-action-blog-autodeployment/)
- [2] [使用 GitHub Actions 实现博客自动化部署](https://frostming.com/2020/04-26/github-actions-deploy)