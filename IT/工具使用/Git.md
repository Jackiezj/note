# Git本地操作

- 将项目clone到本地

  ```bash
  git clone  https://github.com/xxxx.git
  ```

- 创建git

  ```bash
  git init  # 创建本地仓库
  ```

- 查看git文件状态

  ```bash
  git status
  ```

- 提交暂存区

  ```bash
  git add xxx  # 将xxx文件提交到暂存
  git add .  # 将所有文件提交到暂存
  ```

- 提交到仓库

  ```bash
  git commit -m '立项'
  ```

- 查看日志

  ```bash
  git log
  ```

- 回退版本

  ```bash
  git reset --hard HEAD^  # 回退到上个版本
  ```

  > HEAD 当前版本
  >
  > HEAD^  上个版本
  >
  > HEAD^^  上上个版本
  >
  > HEAD0~10 当前版本到前10个版本
  >
  > 通过log查看版本编号, git reset --hard 版本号 回退到特定版本



# Git远程操作

- 设置用户信息

  ```bash
  git config user.name 'dev'
  git config user.email 'dev@163.com'
  
  ```

- 推送到远程

  ```bash
  git push
  ```

- pull到本地

  ```bash
  git pull
  ```

- 大版本 打包

  ```bash
  git tag -a v1.0 -m 'version 1.0'  # 本地打包
  git push origin v1.0  # 将标签推送到远端
  ```

- 分支

  ```bash
  git branck  # 查看当前分支
  git checkout -b dev  # 创建并切换分支
  git checkout dev  # 切换分支
  git push -u origin dev  # 将本地分支及文件推送到远端
  
  git checkout master
  git merge dev  # 将dev分支的代码合并到master分支
  ```

  

# Git忽略文件

- `.gitignore`

  ```python
  .idea/
  *.py[cod]
  ```

  