# git 系列

[TOC]

##  创建github账号

​	https://github.com/

##  建立自己的一个仓库

`New repository`

##  安装git

​	官网下载地址在[这里](https://git-scm.com/downloads)

##  git 配置

1. 设置名字邮箱

   ```sh
   $ git config --global user.name 'Your Name'
   $ git config --global user.email '[Email@example.com](mailto:Email@example.com)'

   $ git config --global  --list ## 查看配置信息
   ```

2. 查看公钥

   ```sh
   ## 进入ssh目录下查看是否已生成公钥
   $ cd ~/.ssh
   ```

3. 创建公钥

    ```sh
    ## 输入完一路回车 会在目录下生成`id_rsa.pub`文件
    $ ssh-keygen -t rsa -C '[youremail@example.com](mailto:youremail@example.com)'

    ```

4. 添加公钥到github上

    右上角点击头像选择`Settings`
    点击`SSH and GPC keys`-> `New SHH key`
    title:随便取
    把`id_rsa.pub`复制到`Key`中

## 提交文件到远程仓库
1. 克隆远程仓库`Clone with SHH`

  ```sh
  $ git clone git@github.com:xx/xx.git
  ```

2. 新增文件

  ```sh
  $ vim test.md ##输入些内容
  ```
3. 推送到远程仓库

  ```sh
  ##添加新的文件到暂存区
  $ git  add . ## git add test.md
  ##从暂存区提交到git管理区
  $ git commit -m "备注描述文字"
  ##从git管理区push到远程仓库
  $ git push origin master
  ##第一次发布的时候要加点东西
  $ git push -u origin master
  ```