---
title: git上传文件（超过100MB）
date: 2021-12-23 10:37:09
tags: [gitee, JAVA, 项目管理]
categories:
- [技术, JAVA]
---

### **问题：git无法上传超过100mb的文件**

### 解决方法：

#### 1.安装lfs（windows10）

进入[git-lfs官网](https://git-lfs.com/)下载安装包，双击安装程序按提示安装即可。

#### 2.开启lfs功能

```
git lfs install
```

#### 3.选择文件类型（）这里选择的是zip类型

```
git lfs track "*.zip"
```

#### 4.然后配置远程仓库

执行完上面的命令后，会生成一个.gitattributes文件，要将其上传到远程gitee仓库。这里我把.gitattributes和大文件分开上传。

```
$ git add .gitattributes
$ git commit -m '上传文件'
$ git push origin master（如果提交不了，后面可以加一个-f）
```

####  5.上传大文件

```
git add mysql-5.7.36-win64.zip
git commit -m "上传MySQL安装包"
git push origin master
```

####  6.解决报错

这里的报错主要有两个

WARNING: Authentication error: Authentication required: LFS only supported repository in paid enterprise.
batch response: LFS only supported repository in paid enterprise.

#####  6.1.第一个错误的解决方式是

git config lfs.https://gitee.com/{your_gitee}/{your_repo}.git/info/lfs.locksverify false
命令中的{your_gitee}/{your_repo}是你的远程仓库地址，根据自己情况替换。

##### 6.2.第二个错误的解决方式

```vbscript
batch response: LFS only supported repository in paid enterprise.
```

删除`./git/hooks/pre-push`文件

```bash
rm .git/hooks/pre-push
```

#### 7.上传文件

```
git push orgin master
```

