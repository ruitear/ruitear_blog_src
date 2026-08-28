---
title: windows搭建git环境
date: 2018-07-14 00:18:25
tags:
  - git
  - 环境搭建
categories:
  - git使用
---

Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。与常用的版本控制工具 CVS, Subversion等不同，它采用了分布式版本库的方式，不必服务器端软件支持.

##### [](#1-git安装)1. git安装

  下载[git windows](https://gitforwindows.org/)一路安装下来.

##### [](#2-注册github账户)2. 注册github账户

  访问[github](https://github.com/)官网，注册一个`github`账户.

##### [](#3-git-bash添加本地仓)3. git bash添加本地仓

  通过 `git bash` 客户端添加添加一个本地文件夹作为本地仓库；可以通过 `pwd` 命令来查看当前bash窗口的路径。
![image](/images/windows_git/1.png)  此处静载本地f盘创建了一个git的空文夹名为:git，用来作为本地的仓库。然后，cd到git文件夹下，用`git init`命令初始化这个文件夹。![image](/images/windows_git/3.png)  初始化之后，再调用 `git config --global` config 本地仓库关联到远端的 `github` 账户，也就是你注册 `github` 的时候填的用户名和邮箱。![image](/images/windows_git/4.png)  到这里，其实已经基本建好了本地仓库和远程github仓库的连接。但是每次使用的时候都让你输入用户名和密码，很是麻烦。怎么解决呢？添加 `ssh key`， 可以根据官网的 [git help](https://help.github.com/categories/ssh/) 创建ssh秘钥。这里首先调用 `$ ssh-keygen -t rsa -C "liuru@host.com"` 调用创建秘钥的指令。
![image](/images/windows_git/5.png)  如上图所示，使用默然的方式生成，并没有进行设置，密码根据自己的喜好设置。然后一直enter到末尾，有了这个秘钥之后，我们还需要做两件事情：**1、把这个密码和github账户关联起来；2、把它和本地客户端关联起来。** 

- **关联的到github账户**: 在github账户的setting地方有一个ssh key的你添加输入，
生成的秘钥内容（id_rsa.pub文件中保存），到相应的地方即可;

- **关联本地客户端**: 根据上述创建的过程中的提示，我们找到相应的.ssh/id_rsa.pub找到文件。首先确保ssh客户端能用，调用 `evel "$(ssh-agent -s)"` 命令查看, 有 agent id 即为有用； ![image](/images/windows_git/6.png)然后, 调用 `ssh-add ~/.ssh/id_rsa` ![image](/images/windows_git/7.png)把秘钥添加到ssh客户端，这样整个过程就创建完成，就可以享受git带来的便利了。
##### [](#4-从github仓库clone内容)4. 从github仓库clone内容
  在本地创建了一个与github的账号同名的文件夹，比如我的github上有一个仓库叫C-Cplus-plus的仓库，然后调用 `$ git clone git@github.com:ruitear/C-C-plus-plus.git`   (其中ruitear是账户名)
![image](/images/windows_git/8.png)
##### [](#5-在仓库里面创建子文件夹)5. 在仓库里面创建子文件夹
  陆github账户在网页上只能以单个文件的形式进行upload，然而并不符合我们管理文档的习惯，建立文件夹来管理文档和代码是我们必须的，为此，就需要进行添加文件夹的操作，cd到相应的版本仓库的本地文件夹，调用`$ git add Sort/` . 这样就在`C-Cplus-plus`仓库内添加了一个Sort文件夹，当然这并不是最终形式没还需要commit操作才能生效, 也就是保存到本地仓库：`$ git commit -m "相关说明"`
![image](/images/windows_git/9.png)
  最后调用push把添加之后的仓库push到github仓库了 `$git push origin master` ， 到这里就完成了git本地环境的搭建并与github创建了相应的连接。
【注意】，当我们往已经有的仓库中添加新的代码时记得，先pull下来更新代码赖，然后在
push，如果有冲突就解决冲突，没有的话就直接push。

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
