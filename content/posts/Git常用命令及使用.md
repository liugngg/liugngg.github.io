---
date: 2025-05-28
tags:
  - 标签1
  - 标签2
title: Git常用命令及使用
slug: 10:05
share: true
description: 
series: 系列
lang: cn
author:
  - liug
dir: posts
keywords:
  - markdown
  - typora
---

[Open: Pasted image 20250528100505.png](/images/49b6597466e1a181da329b9aa73ceaa2_MD5.jpeg)
![49b6597466e1a181da329b9aa73ceaa2_MD5.jpeg](/images/49b6597466e1a181da329b9aa73ceaa2_MD5.jpeg)
**1.常用**
--------

    git remote add origin git@github.com:yeszao/dofiler.git         # 配置远程git版本库
    git pull origin master                                          # 下载代码及快速合并 
    git push origin master                                          # 上传代码及快速合并
    git fetch origin                                                # 从远程库获取代码
    ​
    git branch                                                      # 显示所有分支
    git checkout master                                             # 切换到master分支
    git checkout -b dev                                             # 创建并切换到dev分支
    git commit -m "first version"                                   # 提交
    ​
    git status                                                      # 查看状态
    git log                                                         # 查看提交历史
    ​
    git config --global core.editor vim                             # 设置默认编辑器为vim（git默认用nano）
    git config core.ignorecase false                                # 设置大小写敏感
    git config --global user.name "YOUR NAME"                       # 设置用户名
    git config --global user.email "YOUR EMAIL ADDRESS"  # 设置邮箱
    

**2.别名 alias**
--------------

    git config --global alias.br="branch"                 
    git config --global alias.co="checkout"               
    git config --global alias.cb="checkout -b"            
    git config --global alias.cm="commit -m"              
    git config --global alias.st="status"                 
    git config --global alias.pullm="pull origin master"  
    git config --global alias.pushm="push origin master"  
    git config --global alias.log="git log --oneline --graph --decorate --color=always" 
    git config --global alias.logg="git log --graph --all --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(bold white)— %an%C(reset)%C(bold yellow)%d%C(reset)' --abbrev-commit --date=relative" 
    

**3.创建版本库**
-----------

    git clone <url>                 
    git init                        
    

**4.修改和提交**
-----------

    git status                      
    git diff                        
    git add .                       
    git add <file>                  
    git mv <old> <new>              
    git rm <file>                   
    git rm --cached <file>          
    git commit -m “commit message”  
    git commit --amend              
    

**5.查看历史**
----------

    git log                         
    git log -p <file>               
    git blame <file>                
    

**6.撤销**
--------

    git reset --hard HEAD           
    git reset --hard <version>      
    git checkout HEAD <file>        
    git checkout -- <file>          
    git revert <commit>             
    

**7.分支与标签**
-----------

    git branch                      
    git checkout <branch/tag>       
    git branch <new-branch>         
    git branch -d <branch>          
    git tag                         
    git tag <tagname>               
    git tag -a "v1.0" -m "一些说明"  
    git tag -d <tagname>            
    ​
    git checkout dev                
    git cherry-pick 62ecb3
    

**8.合并与衍合**
-----------

    git merge <branch>              
    git merge --abort               
    git merge dev -Xtheirs          
    git rebase <branch>             
    

**9.远程操作**
----------

    git remote -v                   
    git remote show <remote>        
    git remote add <remote> <url>   
    git remote remove <remote>      
    git fetch <remote>              
    git pull <remote> <branch>      
    git push <remote> <branch>      
    git push <remote> :<branch/tag-name> 
    git push --tags                 
    

**10.打包**
---------

    git archive --format=zip --output ../file.zip master    
    git archive --format=zip --output ../v1.2.zip v1.2      
    git archive --format=zip v1.2 > ../v1.2.zip  
    

**11.全局和局部配置**
--------------

    全局配置保存在：$Home/.gitconfig
    本地仓库配置保存在：.git/config
    

**12.远程与本地合并**
--------------

    git init                              # 初始化本地代码仓
    git add .                             # 添加本地代码
    git commit -m "add local source"      # 提交本地代码
    git pull origin master                # 下载远程代码
    git merge master                      # 合并master分支
    git push -u origin master             # 上传代码
    

1.常用

**安装包，默认会安装最新的版本**

    npm install gulp
    

**安装指定版本**

    npm install gulp@3.9.1
    

**\-S, --save 安装包信息将加入到dependencies（生产阶段的依赖）**

    npm install gulp 
    

**\-D, --save-dev 安装包信息将加入到devDependencies（开发阶段的依赖），所以开发阶段一般使用它**

    npm install gulp 
    

npm uninstall 卸载模块
------------------

基础语法

    npm uninstall [<@scope>/]<pkg>[@<version>]... [-S|--save|-D|--save-dev|-O|--save-optional]
    ​
    aliases: remove, rm, r, un, unlink
    

npm update 更新模块
---------------

    npm update [-g] [<pkg>...]
    

npm ls 查看安装的模块
--------------

    npm ls [[<@scope>/]<pkg> ...]
    ​
    aliases: list, la, ll
    

查看全局安装的模块及依赖

    npm ls -g 
    

npm init 在项目中引导创建一个package.json文件
---------------------------------

    npm init [-f|--force|-y|--yes]
    

npm help 查看某条命令的详细帮助
--------------------

    npm help <term> [<terms..>]
    

**npm root** 查看包的安装路径
---------------------

    npm root [-g]
    

npm start 启动模块
--------------

基础语法

    npm start [
    

npm stop 停止模块
-------------

    npm stop [-- <args>]
    

**Yarn 常用命令**

    npm install === yarn —— install安装是默认行为
     
    npm install taco --save === yarn add taco —— taco包立即被保存到 package.json 中。
     
    npm uninstall taco --save === yarn remove taco
     
    npm install taco --save-dev === yarn add taco --dev
     
    npm update --save === yarn upgrade
     
    npm install taco@latest --save === yarn add taco
     
    npm install taco --global === yarn global add taco —— 一如既往，请谨慎使用 global 标记。
    

**Yarn 独有的命令**

    yarn licenses ls —— 允许你检查依赖的许可信息
    yarn licenses generate —— 自动创建依赖免责声明 license
    yarn why taco —— 检查为什么会安装 taco，详细列出依赖它的其他包
    yarn why vuepress —— 检查为什么会安装 vuepress，详细列出依赖它的其他包