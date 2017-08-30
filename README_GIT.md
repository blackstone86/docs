# GIT配置使用

1. 配置个人信息

    ``` shell
        git config --global user.name hjw
        git config --global user.email junwen_huang@nuoli.cn
    ```

    或

    ``` shell
        git config user.name hjw
        git config user.email junwen_huang@nuoli.cn
    ```

1. 常用命令缩写
    
    设置系统命令简写

    ``` shell
        alias gs='git status'
        alias ga='git add'
        alias gb='git branch'
        alias gc='git commit'
        alias gd='git diff'
        alias go='git checkout'
        alias gk='gitk --all&'
    ```

    设置git命令简写

    ``` shell
        git config --global alias.co checkout
        git config --global alias.ci commit
        git config --global alias.st status
        git config --global alias.br branch
        git config --global alias.pl pull
        git config --global alias.ps push
        git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"
        git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
        git config --global alias.unstage 'reset HEAD'
        git config --global alias.type 'cat-file -t'
        git config --global alias.dump 'cat-file -p'
    ```

1. 分支操作规范

    - 一个任务一条开发分支，一个总任务下的子任务可以公用一个开发分支。
    - 所有开发分支从master分支最新的commit位分出来。（特殊情况特殊处理）
    - sandbox分支不能合并到master分支。
    - coding在开发分支上进行，提测前合并到sandbox分支。
    - 不允许直接在sandbox, master分支提交代码。
    - 开发分支如果需要相互合并，原则上合并分支必须是已经合并到master并发布到生产环境。

    ### 分支介绍
    
    - Feature 分支（newfunc, remould）如：feature_0906_521_kolon_memberapi

        这个分支主要是用来开发一个新的功能，一旦开发完成，我们合并回Develop分支进入下一个Release
        Hotfix分支（bugfix）  如：hotfix_0722_522_gialen_massreport
        当我们在Production发现新的Bug时候，我们需要创建一个Hotfix, 完成Hotfix后，我们合并回Master和Develop分支，所以Hotfix的改动会进入下一个Release

    - master 分支
        
        当前生产环境运行的分支
    
    - sandbox 分支
      
        当前提交测试的sandbox环境运行分支
        开发分支命名规则
        前缀_4位日期_jira任务号_功能描述
      
    ### git flow介绍  
    - [git flow介绍](http://www.cnblogs.com/cnblogsfans/p/5075073.html)

1. 回滚

    - 提交到本地后
        
        ```bash
        // 本地回滚到倒数第四次提交
        git reset HEAD~3 --mixed
        ```

        参数说明

        - `mixed` 回滚index，工作目录保留变更，不暂存变更 **（默认参数）**
        - `soft`  回滚index，工作目录保留变更，暂存变更
        - `hard` 回滚index，工作目录不保留变更，不暂存变更

        > 注：变更指的是倒数3次提交一共的修改

    - 提交到服务器后

        ```bash
        // 撤销最近4次提交
        git revert HEAD~3
        ```

    - `reset` 和 `revert` 区别
        - `reset` 删除撤销的提交记录
        - `revert` 保留撤销的提交记录，再追加一个新的提交记录