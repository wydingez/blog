<div align="right">主目录: 开始 | <a title="评论系统" href="https://github.com/nexmoe/hexo-theme-nexmoe/blob/master/WIKI/comment.md">评论系统</a> | <a title="开始使用" href="https://github.com/nexmoe/hexo-theme-nexmoe/blob/master/WIKI/use.md">开始使用</a> | <a title="注意事项" href="https://github.com/nexmoe/hexo-theme-nexmoe/blob/master/WIKI/notice.md">注意事项</a> | <a title="English" href="https://github.com/nexmoe/hexo-theme-nexmoe/blob/master/WIKI/en/start.md">English</a></div>

# 欢迎使用 Nexmoe

## 目录

- [介绍](#%E4%BB%8B%E7%BB%8D)
- [下载](#%E4%B8%8B%E8%BD%BD)
- [安装nexmoe](#%E5%AE%89%E8%A3%85nexmoe)
- [启用nexmoe](#%E5%90%AF%E7%94%A8nexmoe)
- [运行nexmoe](#%E8%BF%90%E8%A1%8Cnexmoe)
- [更新nexmoe](#%E6%9B%B4%E6%96%B0nexmoe)

## 介绍

Hexo 是基于 [Node.js](https://nodejs.org/) 的高效静态站点生成框架。你可以在 [Hexo 的官方文档](https://hexo.io/zh-cn/docs/) 了解如何安装 Hexo、生成一个站点。
>在这篇文档中，我们假定你已经成功安装了 Hexo，并使用 Hexo 提供的命令创建了一个站点。

在 Hexo 中，通常有两份配置文件，一个是站点根目录下的 `_config.yml`；另外一个是主题目录下的 `_config.yml`。 为了描述方便，在以下说明中，将前者称为 `站点配置文件`，后者称为 `主题配置文件`。

在使用 Nexmoe 之前，请仔细阅读 [Hexo 的官方文档](https://hexo.io/zh-cn/docs/)，完成对 Hexo 的安装，并完成对 `站点配置文件` 的基本配置（标题、介绍、作者、时区、语言等）。

## 下载

## [下载最新 Release 版本](https://github.com/nexmoe/hexo-theme-nexmoe/releases/latest)

>最新的释出版本，适合大部分用户。

## [下载其他版本](https://github.com/nexmoe/hexo-theme-nexmoe/releases)

>你可以自己决定想要使用的版本；部分版本可能不再提供技术支持。

选择你所需要的版本，在新打开的页面中找到 Assets 区域，下载 Source Code (zip) 到本地。
解压所下载的压缩包至站点的 `themes` 目录下， 并将 解压后的文件夹名称 更改为 `nexmoe`（推荐）。

## [使用 Git](https://github.com/nexmoe/hexo-theme-nexmoe)

你可以自己决定想要使用的分支；使用 Git 下载nexmoe以后还可以使用 `git pull` 更新nexmoe。
```
cd themes
git clone https://github.com/nexmoe/hexo-theme-nexmoe.git nexmoe
cd nexmoe
git checkout {branch/tags name/commit hash}
```

## 安装nexmoe

其他主题不同，安装nexmoe需要额外的步骤；主题默认已经集成了文章【字数统计】、【阅读时长】统计功能，且暂时无法关闭。如果没有安装 hexo-wordcount 插件，先安装该插件：
```
npm i --save hexo-wordcount
```
**Node 版本 7.6.0 之前,请安装 2.x 版本 (Node.js v7.6.0 and previous) ，安装命令如下：**
```
npm install hexo-wordcount@2 --save
```
同时你需要把nexmoe主题目录下的 `_config.example.yml` 复制一份并把副本重命名为 `_config.yml`。
```
cp -i _config.example.yml _config.yml
```
因为在主题的开发迭代过程中，`主题的配置文件` 很有可能会有变动。所以我们把 `_config.yml` 添加到了 `.gitignore` 之中，这样可以避免使用 `git pull` 更新主题的用户出现冲突。

## 启用nexmoe

在 `站点配置文件` 中，修改 `theme` 的值为 `nexmoe`。
>如果在安装时你将主题文件夹重命名为其它名字，就修改为你重命名后文件夹的名字

## 运行nexmoe

在站点根目录下运行下面的命令在本地启动一个 Hexo Server。

```
hexo s --debug
```

>在服务启动的过程，注意观察命令行输出是否有任何异常信息，如果你碰到问题，这些信息将帮助他人更好的定位错误。

当命令行输出下述内容时说明 Hexo 已经监听在本机的 4000 端口，使用浏览器访问 [http://localhost:4000](http://localhost:4000) ，检查站点是否正确运行。

```
INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.
```

>如果你在使用过程中遇到问题，请尝试在文档中进行搜索，或者在 GitHub 上 提出 [issue](https://github.com/nexmoe/hexo-theme-nexmoe/issues/new)

## 更新nexmoe

如果你是在 `版本发布页` 下载并安装的nexmoe，那么你需要备份你的 `主题备份文件`，然后将旧的主题文件夹命名为 `nexmoe-old`，将下载的新版本nexmoe重命名为 `nexmoe`，根据更新日志的指导迁移旧的 `主题配置文件` 到新的 `主题配置文件` 中。测试通过后你可以将 `nexmoe-old` 删除。

如果你使用 Git 安装的nexmoe，你可以直接在主题文件夹下运行 git pull 更新主题，并把备份之前的 主题配置文件 重命名为 `_config.old.yml`，复制一份 `_config.example.yml` 并重命名为 `_config.yml`。从 `_config.old.yml` 迁移你的配置到新的 `_config.yml`测试通过后你可以将 `_config.old.yml` 删除。
