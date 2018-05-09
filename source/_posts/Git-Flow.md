---
title: Git Flow
date: 2018-05-09 13:11:31
categories: 版本控制
tags:
  - git
---

#### 一、Git Flow 简介

git flow是一种围绕项目开发对分支管理进行的一种规范,便于多人高效的协作。常见的有三种模型。

－ Git flow

![image](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015122302.png)

项目始终存在两个并行的分支，开发分支和主分支,主分支用于对外版本发布，开发分支主要进行日常的阶段开发，围绕，这个开发分支，存在三种短期的分支
```
**功能分支（feature branch）**
**补丁分支（hotfix branch）**
**预发分支（release branch）**
```

这种分支管理方式，很清晰的明了，但不利于持续发布。

- Github flow
第一种flow不利于持续发布，在github这种分支管理中，优化改进了该缺点。
该种分支管理只存在一个主分支

具体流程如下:

1. 根据需求，从master拉出新分支，不区分功能分支或补丁分支。

2. 新分支开发完成后，或者需要讨论的时候，就向master发起一个pull request（简称PR）

3. Pull Request是一个通知,采用会话机制

4. Pull Request被接受，合并进master，重新部署后，原来你拉出来的那个分支就被删除

github 这种分支管理模型符合了持续集成的需要


#### Git Commit Message规范
在整个 Git Flow 中，commit message 也是必不可少的一部分；一个良好且统一的 commit message 有助于代码审计以及 review 等

规范参考[Angular 社区规范](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit#heading=h.greljkmo14y0)

#### git commit  message工具使用
针对 Git 的 commit message 目前已经有了成熟的生成工具，比较有名的为 [commitizen-cli](https://github.com/commitizen/cz-cli) 工具，其采用 node.js 编写，执行 git cz 命令能够自动生成符合 Angular 社区规范的 commit message；不过由于其使用 node.js 编写，所以安装前需要安装 node.js，因此可能不适合其他非 node.js 的项目使用；这里推荐一个基于 shell 编写的 [Git-toolkit](https://cimhealth.github.io/git-toolkit/)，安装此工具后执行 git ci 命令进行提交将会产生交互式生成 Angular git commit message 格式的提交说明

以上工具主要是为了规范项目提交的message。具体的使用说明，请自行参考项目文档展开进行学习。

