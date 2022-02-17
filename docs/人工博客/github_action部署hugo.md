# github_action 部署hugo

## 1、github action脚本

```
name: GitHubPages
on:
  push:
    branches:
      - master  # Set a branch to deploy

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          # hugo-version: '${{ steps.hugo-version.outputs.HUGO_VERSION }}'
          hugo-version: '0.92.2'
          extended: true
          
      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          deploy_key: ${{ secrets.ACTIONS_DEPLOY_KEY }}
          publish_branch: gh-pages
          publish_dir: ./public
          cname: hugo.pg.run

```

要点说明

+ secrets.ACTIONS_DEPLOY_KEY指的是访问github的公钥
+ cname用于指定绑定的域名



## 2、困惑的地方

每次触发后，生成的文件中仅仅只有 *.xml，起初以为是hugo的版本或者git下载的问题

再三确认后，对应的解决办法是：**theme中是使用 git clone下载放进去的，导致一个工程中存在多个git 项目，主工程中git冲突，导致theme是空的。所以无法生成 对应的html，解决方案是删除内部的隐藏的 .git文件，重新提交一下就好了。**
