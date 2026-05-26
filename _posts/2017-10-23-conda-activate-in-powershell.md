---
layout: post
title: 在Powershell中使用Conda的activate
date: 2017-10-23 09:00
category: python
tags: python, conda, powershell
---

`conda`在Windows上的默认`shell`是`cmd`，而不是`PowerShell`。在`PowerShell`中使用一般的`conda`命令是可以，但无法使用`activate`、`deactivate`切换环境，这在日常使用中极其不便，为了切换环境，还要单独打开一个cmd。

在[`github`](https://github.com/conda/conda/issues/626)上一直都在讨论这个问题，而官方版本始终没有解决。[BCSharp](https://github.com/BCSharp)给出了`Powershell`脚本[PSCondaEnvs](https://github.com/BCSharp/PSCondaEnvs)解决了这一问题。

可以直接使用`conda`通过他的Anaconda Cloud空间安装。

```
conda install -n root -c pscondaenvs pscondaenvs
```

可是，在国内总是连接有问题，因而只能采用手动安装，把`github`的文件下载下来，将`activate.ps1`，`deactivate.ps1`和`invoke_cmdscript.ps1`三个文件拷贝到`Anaconda\Scripts`下面。

加入后通过`PowerShell`就能够正常使用`activate`和`deactivate`了。

然而，在使用`cmder`时出现另外一个问题，即环境切换成功，但无法在Prompt上显示当前环境名，如下图。

![Fail to set prompt](https://gitee.com/whimian/images/raw/master/fail_prompt.png
)

[cmder的issues](https://github.com/cmderdev/cmder/issues/1207#issuecomment-273478981)里有人提出了一种解决方法，即将`vendor\profile.ps1`中最后一行，将prompt设定为常量的代码注释掉，这样便可以正常使用了。

![Work Fine](https://gitee.com/whimian/images/raw/master/success_prompt.png
)
