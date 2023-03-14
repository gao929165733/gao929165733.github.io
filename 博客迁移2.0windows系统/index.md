# Hugo博客从一台电脑迁移到另外一台电脑（windows系统）


# Hugo博客迁移部署2.0

## 问题描述

- 因为学习环境变化，更换了电脑，所以想把之前部署好的博客迁移到新电脑上，折腾了半天，在此记录一下，以免之后有同样的需求。

## 步骤

### 1. 环境配置

- git环境配置
- hugo环境配置
  - 下载
    - Hugo的安装也很简单，只需要去[Hugo官网](https://github.com/gohugoio/hugo/releases)下载适合你电脑的安装包，直接解压到你想安装的路径，最后将该路径添加到PATH环境变量即可。建议一步到位，直接安装`hugo_extended`版本，因为LoveIt主题中有一些Features的实现只有extended版本的Hugo支持。我使用的是Win10系统64bit版本，因此选择[hugo_extended_0.79.1_Windows-64bit.zip](https://github.com/gohugoio/hugo/releases/download/v0.79.1/hugo_extended_0.79.1_Windows-64bit.zip)即可。其它系统的安装详见[Hugo官网介绍](https://gohugo.io/getting-started/installing/)，文档都很详细。
  - 环境变量
    - 系统变量(System variables)中的`PATH`，点击编辑。点击新建(New)，将Hugo解压的地址添加到环境变量，点击OK即可。

### 2. 文件处理

- 文件拷贝：将之前的博客所有文件复制到新电脑环境下

- 文件清理：将旧博客文件夹里面的git环境删除干净

- 文件替换和修改

  - 如果需要更换博客主题，以`CodeIt`主题为例

    - 下载CodeIt主题（注意是在`myblog`路径下面进行）

      ```
      git init
      git submodule add https://github.com/sunt-programator/CodeIT.git themes/CodeIt
      ```

    - 文件替换，替换配置文件。首先进入`myblog\themes\CodeIT\exampleSite`目录下，复制`config.toml`文件，将其粘贴至`myblog`下以替换原有的`config.toml`文件，使用文本编辑器打开`myblog`目录下的`config.toml`文件，修改`baseURL`，`themeDir`以及`enableGitinfo`三行内容如下：

      ```
      baseURL = "https://example.com"   ## 修改为你的github.io地址，格式为：https://yourusername.github.io
      #themesDir = "../.." ## 注释掉该行
      enableGitInfo = false ## 由true改为false
      ```


### 3. 设置github

- git设置

  - 重新在git设置一下身份的名字和邮箱（最好跟之前的电脑一致）
    - `git config --global user.name "yourname"`
    - `git config --global user.email "your@email.com"`
  - 删除.ssh文件夹（直接搜索该文件夹）下所有文件(手动删除即可，不需要git）()，该文件在C盘用户目录下
  - git输入命令生成密匙（一直回车即可）
    - `ssh-keygen -t rsa -C "your@email.com"`（请填你设置的邮箱地址）
  - 打开https://github.com/，登陆你的账户，进入`Settings`-->`SSH and GPG keys`-->`New SSH key`，将C盘用户目录下`.ssh`文件夹中的`id_rsa.pub`中内容复制到**"New SSH key"**中
- 删除`github`原来的`repository`
- 新建一个`repository`，设置`github page`。**“Settings”**-->**"Pages"**-->**“master”**-->**"docs"**

### 4. 将博客内容上传至Github

- 编译`hugo`

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

## 参考

- [Hugo博客部署到GitHub无法显示CSS、JS踩坑](https://blog.csdn.net/qq_45050480/article/details/105325541)
- [Hugo博客从本地托管到github](https://www.daniao.org/5349.html)
- [基于Hugo + Github Pages + Netlify搭建LoveIt主题个人博客](https://dongxu-zheng.github.io/zh-cn/01_hugo_building/#step-4-%E5%AE%89%E8%A3%85git)

<br>
<font size="6">加油吧，骚年！</font>

