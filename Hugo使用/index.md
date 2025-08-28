+++
date = '2025-05-22T15:53:57+08:00'
draft = true
title = 'Hugo使用'
math = ''
license = ""
description = ""
categories = [
    "开发工具使用"
]
tags = [
    "Hugo"
]
image = "cover.png"
+++

## 启动 Hugo 服务
hugo server -D

## 创建文章
### 命令创建
    命令 : hugo new post/foldername/*.md 
    其模板来自 archetypes/default.md
### 手动创建
    在post目录下，手动创建一个文件夹，在文件夹里创建md文件，并将需要的图片资源放入其中。

## 文章推送
1. git add .
2. git commit -m"update"
3. git push

## github 图床使用

### 创建Github仓库并生成Token

1. 创建仓库
   - 在 GitHub 上新建一个仓库（如 `my-image-repo`），确保仓库是公开的（Public）。
2. 生成 Token
   - 登录 GitHub，进入 **Settings** -> **Developer settings** -> **Personal access tokens**。
   - 点击 **Generate new token**，勾选 `repo` 权限。
   - 生成 Token 后，复制并保存（Token 只会显示一次）。

### 配置typora PicGo

- 打开 Typora 的配置文件：

  - 在 Typora 的图像设置中，点击 **打开配置文件**。

  - 这会打开一个 `picgo.json` 文件（如果没有，可以手动创建）。

  - 编辑 `picgo.json` 文件，添加 GitHub 图床的配置。以下是一个示例配置：

    ```
    {
      "picBed": {
        "current": "github",
        "github": {
          "repo": "你的用户名/你的仓库名", // 例如：your-username/my-image-repo
          "branch": "main", // 或 master
          "token": "你的 GitHub Token", // 生成的 Personal Access Token
          "path": "assets/", // 图片上传到仓库的 assets 目录
          "customUrl": "" // 如果需要自定义域名，可以填写
        }
      },
      "picgoPlugins": {
        "github": true // 确保启用 GitHub 插件
      }
    }
    ```

  - **repo**: 你的 GitHub 仓库名，格式为 `用户名/仓库名`。

  - **branch**: 仓库的分支，通常是 `main` 或 `master`。

  - **token**: 你的 GitHub Personal Access Token（生成方法见下文）。

  - **path**: 图片上传的路径，例如 `assets/`。

  - **customUrl**: 如果需要自定义域名（如 CDN），可以填写。

- 保存 `picgo.json` 文件。
