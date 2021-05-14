# 关于项目结构、导入路径与setuptools

### 1. 我习惯使用的项目结构

​	一个干净标准的项目结构可以帮我们躲过很多莫名其妙浪费时间的问题，关于python项目的项目结构，我参考了[Structuring Your Project](https://docs.python-guide.org/writing/structure/)这里给出的建议：

```sh
README.rst
LICENSE
setup.py
requirements.txt
sample/__init__.py
sample/core.py
sample/helpers.py
sample/main.py
docs/conf.py
docs/index.rst
tests/test_basic.py
tests/test_advanced.py
venv
```

项目的核心代码全部放在sample文件夹下，测试代码全在和sample文件夹同级的test文件夹下。

### 2. python导入路径、module、package

​	要理解python的模块导入，首先要弄明白python里的`sys.path`，如果启动python，打印`sys.path`，可以发现这个变量实际上是一个包含了一些路径字符串的列表：

```sh
>>> import sys
>>> from pprint import pprint
>>> pprint(sys.path)
['',  # 注意这个路径，这个路径表示当前文件所在的目录
 '/Users/xx/.pyenv/versions/3.8.5/lib/python38.zip',
 '/Users/xx/.pyenv/versions/3.8.5/lib/python3.8',
 '/Users/xx/.pyenv/versions/3.8.5/lib/python3.8/lib-dynload',
 '/Users/xx/.pyenv/versions/3.8.5/lib/python3.8/site-packages']
```

当python解释器看到你在某个文件中使用`import`时，它首先在该文件所在的目录下寻找你所想导入的模块，如果没有找到到`sys.path`中所有的路径中去寻找，如果还没找到则会报`ModuleNotFoundError`，这些都比较好理解，问题是当程序启动时它会把哪些路径添加进入`sys.path`？使用python执行的那个文件（入口文件）所在的目录肯定会加进去，这样按照之前项目结构构建的项目所有sample下面的文件应该都可以正常引用。但是tests下面的测试用例怎么正常找到sample呢?

### 3. 利用setuptools将脚本打包

​	按照之前的项目结构，可以给出一个用setup.py打包模版：

```python
from setuptools import setup, find_packages

setup(
    name="xxx",
    version="0.1",
    packages=find_packages(),
    include_package_data=True,
    platforms="any",
    url="",
    author="xxx",
    author_email="xxx@xxx.com",
    install_requires=['prettytable', 'protobuf', 'requests', 'pyyaml'],
    entry_points={
        "console_scripts": [
            "xxx = xxx.main:cli"
        ]
    }
)
```

利用`python setup.py build`就可以把脚本打包成压缩包了

