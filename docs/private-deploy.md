# 搭建私有网站

> [!WARNING]
> ~~由于项目目前还没有进入稳定状态，所以如果进行了私有部署，请随时关注该项目的最新更新，特别是代理部分的变化，后续将会修改使用策略。~~
>
> ~~或者你可以修改`config/index.ts`中的`AVAILABLE_PROXY_LIST`变量，完全使用自己搭建的节点。~~
>
> ~~另外，目前只有部署到 Deno Deploy 的文档，如果需要部署到其他平台，请在 Issue 中说明。~~
>
> 2024.11.14 更新
>
> 推荐使用 **[公共网站](https://wechat-article-exporter.deno.dev/)** + **私有代理节点** 的形式使用。
>
> 如果你确实需要部署私有网站，可查看下面的部署教程。
>
> 注意：公众网站(也就是 https://wechat-article-exporter.deno.dev/ )的功能是 **最全的**，后续某些 Pro 版功能只会出现在公共网站中，私有部署不支持。

## 部署到 Deno Deploy

1. Fork 该项目

![create a fork][create-a-fork]

2. 点击 [New Project][new-deno-deploy-project] 在 Deno Deploy 上面创建一个项目，选择你刚fork的仓库，如下图所示:

![create deno deploy project][create-deno-deploy-project]

创建之后如下所示:

![deno deploy project result][deno-deploy-project-create-result]

3. 修改github仓库发布配置

启用仓库的 workflows (默认fork的仓库是禁用的):

![enable github workflows][enable-github-workflows]

新建`.github/workflows/deno_deploy.yml`:

![update workflows project][update-workflows-project]
内容如下:
```yaml
name: deno-deploy

on:
  push:
    branches:
      - master
  pull_request:
    branches:
      - master

jobs:
  deploy:
    name: Deploy
    runs-on: ubuntu-latest
    permissions:
      id-token: write # Needed for auth with Deno Deploy
      contents: read # Needed to clone the repository

    steps:
      - name: 拉取代码
        uses: actions/checkout@v4
      - run: corepack enable
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: yarn
      - run: yarn
      - run: yarn build
        env:
          NITRO_PRESET: deno_deploy
      - name: Deploy to Deno Deploy
        uses: denoland/deployctl@v1
        with:
          project: project-name # 此处需要修改为第2步中你自己的项目名
          entrypoint: server/index.ts
          root: .output

```

提交:

![commit changes][commit-changes]

4. 等待发布结果

![deploy success][deploy-success]

![finally website][finally-website]


<!-- Definitions -->

[create-a-fork]: ../assets/deploy/create-fork.png

[new-deno-deploy-project]: https://dash.deno.com/new_project

[create-deno-deploy-project]: ../assets/deploy/create-deno-deploy-project.png

[deno-deploy-project-create-result]: ../assets/deploy/deno-deploy-project-result.png

[enable-github-workflows]: ../assets/deploy/enable-github-workflows.png

[update-workflows-project]: ../assets/deploy/update-workflows-project.png

[commit-changes]: ../assets/deploy/commit-changes.png

[deploy-success]: ../assets/deploy/deploy-success.png

[finally-website]: ../assets/deploy/finally-website.png
