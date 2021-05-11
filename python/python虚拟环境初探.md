# python虚拟环境初探

### 1. 前言——到底什么是虚拟环境

​	在我的理解里，python的虚拟环境是为了解决不同版本解释器以及库之间的不兼容性而设计的。不同于其他一些语言，在python中引用外部库时无需指定该库的版本，并且同一个库也只会在你的库目录下装一个版本，那假如我在开发我的脚本项目时使用的是a库的1.1版本，这个版本相对于1.0版多了一个`foo()`函数并且删掉了1.0版中的`bar()`函数，当我把我的项目交给同事A使用时，他的环境里只有a库的1.0版本，并且他自己写的一些脚本工具依赖这个库里的`bar()`函数，这个时候他想要用我的脚本，他要怎么办呢？

​	一个直接的解决思路是他装一个1.1版，并且让python解释器在找这个库时去找1.1版的，这是很多语言例如go的对库多版本管理的解决方案。但是python里头不这么干，它的方式是多整几个库目录，这几个库目录相互隔离，在甲库目录里a可以是1.1的，在乙库目录里a可以是1.0的，并且每个库目录绑定需要的解释器啊或者其他工具比如pip之类的，这样形成了一个完全独立的，不依赖你的系统装的python一套python环境，这个环境也被称为虚拟环境。

​	这里补充一个背景知识，python环境就是指“装个python”里头的这个python，我们装个python的时候实际上除了最核心的解释器外还有自带的库以及一些可执行程序，比如pip。这个环境到底在哪里呢？我以我mac上装的python3为例：

<img src="/Users/xxx/notes/python/python虚拟环境初探.assets/image-20210509220309800.png" alt="image-20210509220309800" style="zoom:50%;" />

我的python3装在usr/local/bin下面，这很好理解，当我去看这个可执行文件的时候发现其实它只是一个链接，链到了其他地方：

<img src="/Users/xxx/notes/python/python虚拟环境初探.assets/image-20210509220525098.png" alt="image-20210509220525098" style="zoom:50%;" />

再去看这个东西，发现它还是一个链接：

<img src="/Users/xxx/notes/python/python虚拟环境初探.assets/image-20210509220812026.png" alt="image-20210509220812026" style="zoom:50%;" />

在继续往下看，终于看到我想要的结果了，python3链到了python3.8上：

<img src="/Users/xxx/notes/python/python虚拟环境初探.assets/image-20210509220939667.png" alt="image-20210509220939667" style="zoom:50%;" />

我不理解这里面这么设置的原因，不过不要紧，先去上层目录看看python里头到底有什么东西：

<img src="/Users/xxx/notes/python/python虚拟环境初探.assets/image-20210509221215603.png" alt="image-20210509221215603" style="zoom:50%;" />

这里头有bin有lib，看起来就是一个普通程序，事实上它确实也只是一个普通程序。解释器知道在哪了，那库呢：

<img src="/Users/xxx/notes/python/python虚拟环境初探.assets/image-20210509224226644.png" alt="image-20210509224226644" style="zoom:50%;" />

我们安装的库一般都在site-packages这个目录下。

### 2. pyenv、virtualenv、venv它们到底有啥区别

​	前文说到，一个python环境可以分为三个部分：解释器、库、其他可执行文件，一个虚拟环境同样包含这三个部分。但是这里还存在一个问题，如果我基于系统python环境创建一个虚拟环境，系统环境是python3.7而我想用python3.6怎么办，难道我要在我的机器上装多个python版本？听起来就是一件很麻烦而且容易出错的事情，pyenv就是为了解决这个事情的，pyenv可以解决多个python版本共存的问题，而且可以很方便的从一个版本切换到另一个版本。

#### 2.1 pyenv

​	在mac上安装pyenv很简单：

```sh
brew install pyenv
# 安装完毕后实际上还需要进行配置
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.profile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.profile
echo 'eval "$(pyenv init --path)"' >> ~/.profile
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
# 重启一下终端，首先安装一个3.6.8版本的python
pyenv install 3.6.8
# 切换到这个python，不再用系统默认的python
pyenv global 3.6.8
# 运行python3，发现现在python的版本已经是3.6.8了，这下我们就可以方便的切换python版本了
```

### 2.2 virtualenv

​	pyenv还只解决了python多版本切换的问题，我们的终极目标是可以创建一个个独立的python环境，这一点pyenv做不到，需要virtualenv，实际上还有个pyenv-virtualenv，可以理解为和virtualenv同一个东西。



