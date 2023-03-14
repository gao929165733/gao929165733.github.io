# 更新Github + Hugo博客 + 博客部署迁移


# 更新Github + Hugo博客 + 博客部署迁移
记录一下更新博客内容的步骤，以及相关git命令的含义。

## 步

### 1. 创建新博客文件

- 进入博客所在目录

  `cd G:\MyBlog`

- 创建一篇新的博客

  `hugo new posts/bolg_name.md`

  编写博客内容时，需要设置`draft:false`，否则博客的内容将会以草稿的形式保存，不会正式发布。

### 2.将博客内容上传至Github

- 编译

  `hugo`

  该命令用于编译网站的内容，同时生成`public`文件夹，里面保存了静态网页所需要的所有内容，我们需要把`public`上传至我们第一步创建的Github仓库中，通过github.io发布博客。

- 推送

  1. 进入目录
  
  ```
  cd public
  ```
  
  2. 初始化
  
  ```
  git init
  ```
  
  3. 删除本地指定的远程地址
  
  ```
  git remote rm origin
  ```
  
  4. 填写自己的域名
  
  ```
  git remote add origin git@github.com:gao929165733/gao929165733.github.io
  ```
  
  5. 添加当前目录下的所有文件到暂存区
  
  ```
  git add .
  ```
  
  6. 初将暂存区内容添加到本地仓库中
  
  ```
  git commit -m "hugo"
  ```
  
  7. 将本地的master分支推送到origin主机的master分支
  
  ```
  git push -u origin master
  ```

### 3. 将博客部署迁移到其他电脑

- 环境安装（Hugo+Git）

- 源代码复制和修改

  - 将博客源代码文件复制到需要配置的电脑
  - 删除主题的.git配置，如果你有多个主题文件夹的话，需要都删除。`rm -rf ./themes/next/.git/` 用这条命令来删除你对应主题文件夹里的.git 文件夹，注意 .git 是一个隐藏文件夹。
  - `.gitignore` 文件夹在 Hexo 的根目录下可能已经存在了，可以打开和该文中的忽略事项比对一下，如果一样，接着 `git init` 初始化 git 仓库就好；如果没有该 `.gitignore` 文件夹就新建一个添加相应忽略事项再 `git init` 初始化。

- git设置

  - 重新在git设置一下身份的名字和邮箱（最好跟之前的电脑一致）
    - `git config --global user.name "yourname"`
    - `git config --global user.email "your@email.com"`
  - 删除.ssh文件夹（直接搜索该文件夹）下所有文件(手动删除即可，不需要git）()，该文件在C盘用户目录下
  - git输入命令生成密匙（一直回车即可）
    - `ssh-keygen -t rsa -C "your@email.com"`（请填你设置的邮箱地址）
  - 打开https://github.com/，登陆你的账户，进入设置-->**“SSH and GPG keys”**-->**"New SSH key"**，将C盘用户目录下.ssh文件夹中的**“id_rsa.pub”**中内容复制到**"New SSH key"**中

- 开始写博客前，把远程私有仓库的最新博客源文件拉到本地

  - **`git pull`**
  - **`git fetch --all`**   #将远程git仓库上最新的内容拉取到本地,将本地库所关联的远程库更新至最新

- 重复第二步

  

### 4.一些坑和参考

- `git remote add origin https://github.com/xxx/xxx.github.io #填写自己的域名`

  该命令一直报错time out，替换为`git remote add origin git@github.com:xxx/xxx.github.io`即可

- `git remote rm origin` # 删除本地指定的远程地址

- [loveIt主题](https://hugoloveit.com/zh-cn/theme-documentation-basics/#26-%E6%9E%84%E5%BB%BA%E7%BD%91%E7%AB%99)

- [github+hugo配置教程](https://dongxu-zheng.github.io/zh-cn/01_hugo_building/)

- [最安全的hexo多台电脑同步博客解决方案--非新建分支](https://www.alankeene.com/2019/0102/hexo-perfect-synchronize.html)

- [git遇到的问题之“Please make sure you have the correct access rights and the repository exists.”](https://blog.csdn.net/jingtingfengguo/article/details/51892864)

- [解决Please make sure you have the correct access rights and the repository exists 问题.](https://blog.csdn.net/qq_43705131/article/details/107965888)

- [基于Hugo + Github Pages + Netlify搭建LoveIt主题个人博客](https://dongxu-zheng.github.io/zh-cn/01_hugo_building/)

- [GitHub报错master->master（fetch first)的解决方法](https://blog.csdn.net/ITxiaodong/article/details/51399333)

<br>
<font size="6">加油吧，骚年！</font>


