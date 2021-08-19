# wyding's blog
基于hexo的博客
   
> env：请使用node@12.x / node@13.x的版本  
> 最新的node@15.x版本自动打包生成的静态文件会变成空文件，[参考](https://github.com/hexojs/hexo/issues/4267#issuecomment-619394907)

### npm run dev
启动博客本地开发预览环境

### npm run build
基于博客生成静态文件

### npm run commit
自动提交本地修改后的源码到github的主干上，提交后会自动触发travis的自动部署脚本，部署到[服务器](https://www.wyd94.top)
