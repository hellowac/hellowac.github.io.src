# hellowac.github.io 个人网站

基于hexo

## 开发 hexo server

## 发布 hexo deploy

发布到远程github仓库, <https://hellowac.github.io>

具体配置参考官网文档: <https://hexo.io/zh-cn/docs/one-command-deployment.html#Git>

## Yarn 和 NPM 国内快速切换镜像

NPM

```shell
# 查询当前镜像
npm get registry 

# 设置为淘宝镜像
npm config set registry https://registry.npm.taobao.org/

# 设置为官方镜像
npm config set registry https://registry.npmjs.org/
```

YARN

```shell
# 查询当前镜像
yarn config get registry

# 设置为淘宝镜像
yarn config set registry https://registry.npm.taobao.org/

# 设置为官方镜像
yarn config set registry https://registry.yarnpkg.com
```
