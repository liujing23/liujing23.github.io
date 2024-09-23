---
title: hexo部署gitee
date: 2020-12-05 17:50:23
tags: [hexo, 博客, gitee]
categories:
- [技术, 博客]
---

1. 访问[官网](https://hexo.io/zh-cn/index.html)，获取hexo源码

2. 在gitee网站，新建仓库

   ```
   git config --global user.email "你的邮箱"
   git config --global user.name "用户名"
   ```

3. 获取SSH公钥

   ```
   // 通过命令 ssh-keygen 生成 SSH Key
   ssh-keygen -t rsa -C "你的邮箱"
   // 读取公钥文件
   cat ~/.ssh/id_rsa.pub
   ```

4. 将公钥复制，在gitee官网设置中，添加公钥

5. 通过 `ssh -T` 测试，输出 SSH Key 绑定的**用户名**

   ```
   $ ssh -T git@gitee.com
   Hi USERNAME! You've successfully authenticated, but GITEE.COM does not provide shell access.
   ```

6. 安装，完成后可以部署到gitee

   ```
   npm install hexo-cli -g
   
   npm install hexo-cli
   npm install
   npm install hexo-deployer-git
   ```

7. 生成网页

   ```
   hexo g
   ```

8. 本地运行，预览网页

   ```
   hexo s
   ```

9. 部署博客

   ```
   hexo d
   ```

