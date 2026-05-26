---
layout: post
title: conda与pip+virtualenv的环境管理对比
date: 2017-10-20 09:00:00
tags: python, conda, pip
categories: Python
---

最近发现[PyScaffold](https://pyscaffold.readthedocs.io/en/v2.5.8/features.html)可以辅助创建项目结构，很想试一下。

可是不知道为什么校园网突然不能够访问[清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/)，这是国内目前唯一有[Anaconda](https://mirrors.tuna.tsinghua.edu.cn/anaconda/)的源，里面居然还有[conda-forge](https://conda-forge.org/)的镜像（为他们疯狂打call）。

之前只用过`conda`来管理环境，用`conda`非常简单方便，`conda create`加上参数`-n`后面跟上环境的名字，在后面加上创建后想安装的packages。

```
conda create -n NewEnv pip
```

使用`conda`，环境默认都创建在`$home\Anaconda3\envs`下面，方便管理，当然有好多参数，可以为所欲为。

不能用`conda`了，有很想试一下`PyScaffold`，怎么创建新环境呢，那就试试传统的`virtualenv`呗。

```
cd $ENV_BASE_DIR
virtualenv $ENVIRONMENT_NAME
```

真是一个参数也没有啊。

还是`conda`的[官方文档](https://docs.anaconda.com/_downloads/conda-pip-virtualenv-translator.html)里有一个对比表格:

| Task | Conda package and environment manager command | Pip package manager command | Virtualenv environment manager command |
|:--------------------------------------:|:-----------------------------------------------------:|:-----------------------------------------------------------------:|:-----------------------------------------------------:|
| Install a package | conda install $PACKAGE_NAME | pip install $PACKAGE_NAME | X |
| Update a package | conda update --name $ENVIRONMENT_NAME $PACKAGE_NAME | pip install --upgrade $PACKAGE_NAME | X |
| Update package manager | conda update conda | Linux/OSX: pip install -U pip Win: python -m pip install -U pip | X |
| Uninstall a package | conda remove --name $ENVIRONMENT_NAME $PACKAGE_NAME | pip uninstall $PACKAGE_NAME | X |
| Create an environment | conda create --name $ENVIRONMENT_NAME python | X | cd $ENV_BASE_DIR; virtualenv $ENVIRONMENT_NAME |
| Activate an environment | source activate $ENVIRONMENT_NAME | X | source $ENV_BASE_DIR/ $ENVIRONMENT_NAME/bin/activate |
| Deactivate an environment | source deactivate | X | deactivate |
| Search available packages | conda search $SEARCH_TERM | pip search $SEARCH_TERM | X |
| Install package from specific source | conda install --channel $URL $PACKAGE_NAME | pip install --index-url $URL $PACKAGE_NAME | X |
| List installed packages | conda list --name $ENVIRONMENT_NAME | pip list | X |
| Create requirements file | conda list --export | pip freeze | X |
| List all environments | conda info --envs | X | Install virtualenv wrapper, then lsvirtualenv |
| Install other package manager | conda install pip | pip install conda | X |
| Install Python | conda install python=x.x | X | X |
| Update Python | conda update python * | X | X |

还是`conda`好。。。