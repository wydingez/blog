<div align="right">主目录: <a title="开始" href="https://github.com/nexmoe/hexo-theme-nexmoe/blob/master/WIKI/start.md">开始</a> | <a title="评论系统" href="https://github.com/nexmoe/hexo-theme-nexmoe/blob/master/WIKI/comment.md">评论系统</a> | <a title="开始使用" href="https://github.com/nexmoe/hexo-theme-nexmoe/blob/master/WIKI/use.md">开始使用</a> | 注意事项 | <a title="English" href="https://github.com/nexmoe/hexo-theme-nexmoe/blob/master/WIKI/en/notice.md">English</a></div>

# 注意事项

## 目录

- [代码高亮](#%E4%BB%A3%E7%A0%81%E9%AB%98%E4%BA%AE)
- [使用 Git 管理 Hexo](#%E4%BD%BF%E7%94%A8%20Git%20%E7%AE%A1%E7%90%86%20Hexo)

## 代码高亮
主题内置代码高亮且自动开启，为了避免冲突，请在 `站点配置文件` 中禁用 highlight
```
highlight:
  enable: false
```

## 使用 Git 管理 Hexo
正如在 开始 中所说，为了避免冲突，Nexmoe中只有 `_config.example.yml`，`主题配置文件` 需要将 `_config.example.yml` 复制一份并**重命名**为 `_config.yml`。

为了防止开发中 `_config.yml` 被推送进 Repo 中，我们将 `_config.yml` 写入了 `.gitignore`。这意味着在使用 Git 管理 Hexo 站点文件时，`主题配置文件` 并不会被 Push 到 Repo 中。此时你需要将 `_config.yml` 从 `.gitignore` 中**删除**，注意这有可能会造成使用 git pull 更新时造成冲突。