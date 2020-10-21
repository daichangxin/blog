title: Git工作流
date: 2020-10-05 18:28:28
tags: ["Git"]
---
啥是”工作流“？其实就是怎么使用一个东西，或者这个东西一般都是怎么被运用的。
说起Git的工作流就不得不提起非常出名的git-flow，这里有一个非常详细的说明：https://www.git-tower.com/learn/git/ebook/cn/command-line/advanced-topics/git-flow/ 。
# git-flow定义了完整的工作流程和规范，具体包含以下几点：
### 分支管理
常驻分支有两个：develop和master，为啥没有release分支？release在git-flow中是在发布release版本时创建的临时分支，创建后会自动打上版本号tag，随着发布成功，release分支会被立刻删除。而master分支永远保持着是软件的最终版本（也就是说永远是线上的最新发布版本）。
### 开发新功能
所有新功能都是基于develop分支开发，develop上的新功能会逐个逐个的增加，当开始发release版本的时候，就从develop分支创建release的临时分支，打上release的tag，然后将release合并到master中，再把release分支删除。
### 生产修复
当遇到生产（线上）问题，我们需要从生产上的分支开出新分支进行修复，所以会从master分支上开出分支，并修复问题，修复后执行git-flow指定，会自动将修复的分支合并到develop和master分支上。

这就是git-flow主要的流程，都包含在了git-flow的命令工具中，但是如果一个团队真的去应用git-flow会发现，这是一个非常繁琐复杂的流程，对于团队协作来说，多出的任何一个git指令，对于团队管理来说，就是成倍的教育成本。虽然我们可以选择招聘到对git非常擅长的人才，但是一个简洁的git工作流，可以减少开发人员在协作上遇到的冲突，也降低了使用git的上手难度。

# 这里介绍一个非常简单的git工作流：
### 分支管理
常驻分支有两个：master和release。这里的master相当于git-flow中的develop，而release相当于git-flow中的master，只是我们不会像git-flow中那样把release当做临时分支删除。
### 开发新功能
新功能基于master开发，并逐个合并进入master，待需要发版本时，将master合并到release
### 生产修复
从release开出分支修复问题，修复的问题直接合入release，通过git ci等集成工具，合入release的所有提交都会自动合并回master。
### 比较
看似并没有太大的改善，但是开发者在开发时不用关心自己当前是在什么分支流程上，git-flow过于严格，如果是生产修复（hotfix），那么就会自动发布release和合并到develop&master，但是对于日常开发来说，线上的问题可能不是那么紧急，也可能在修复的过程中发现必须要跟随着下个release版本发布才行，这就对hotfix有很大的灵活需求，我们完全可以在hotfix修复，合并到master即可。如果需要立刻上线，那么就把这个hotfix的分支cherrypick出来再合并到release上。