---
title: 设置Powershell终端
date: 2020-04-14 09:00
category: python, powershell, windows
tags: python, conda, powershell
---

## Intro

提到shell，似乎大家首先想到Linux，其实Windows的Powershell也是非常好的命令行界面。同时由于Powershell默认状态下已经把很多常用的基础bash命令(如`cd`和`ls`)都设置成了别名，因而对于一些常用操作基本上可以当成bash来用。当然还有一些缺失，比如`which`和`touch`等命令，则需要通过自定义函数利用Powershell命令实现。另一方面，Powershell的蓝色背景白色字符实在是缺乏美感，也影响日常使用，因而需要选择一个终端并进行美化工作。因而本文首先介绍几个自定义的常用函数的方法，同时进一步介绍使用oh-my-posh和Windows Terminal构建美观的Powershell终端。

## 常用bash命令实现

由于powershell自带的别名有缺失或默认功能与bash命令有所不同，因而需要自定义和重载一些函数。这些函数需要在`profile.ps1`中定义，这样每次打开powershell时即可自动加载。以下介绍3个常用命令。

### `ls`

`ls`时Powershell自带的别名，然而其输出的结果默认为完整信息形式且没有用不同颜色表示文件夹或文件。

![](https://gitee.com/whimian/images/raw/master/20200414150435.png)

这里使用`Get-ChildItemColor`实现不同文件类型以不同颜色显示，同时增加不同别名来实现仅列出文件名和列出详细文件信息功能。

该Cmdlet可以直接从Powershell Gellery中安装

```powershell

Install-Module -Name Get-ChildItemColor -Scope CurrentUser

```

在`profile.ps1`中加入：

```powershell
# Ensure that Get-ChildItemColor is loaded
Import-Module Get-ChildItemColor
# Set l and ls alias to use the new Get-ChildItemColor cmdlets
Set-Alias l Get-ChildItemColor -Option AllScope
Set-Alias ls Get-ChildItemColorFormatWide -Option AllScope
```

进而实现使用`ls`列出文件名，使用`l`列出详细列表。

![](https://gitee.com/whimian/images/raw/master/20200414150851.png)

### `touch`

Powershell默认没有定义`touch`命令的别名，这里使用以下函数使用`New-Item`实现`touch`。

```powershell

function touch($name)
{
    if ($name) {
        $file_path = Split-Path -Path $name
        $file_name = Split-Path -Path $name -Leaf
        if ($file_path -eq "") {
            $file_path = "."
        }
        if (-Not (Test-Path($file_path))) {
            New-Item -ItemType "directory" -Path $file_path
        }
        New-Item -Path $file_path -Name $file_name -ItemType "file"
    }
    else {
        Write-Host "Command to create new file."
    }
}
```
### `which`

Powershell默认也没有定义`which`，这里用`Get-Command`实现`which`。

```powershell
function which($name)
{
    Get-Command $name | Select-Object -ExpandProperty Definition
}
```

### `~`

Powershell没有使用`~`来代表用户目录，可以通过简单的`Set-Alias`来实现。

```powershell
function cuserprofile { Set-Location ~ }
Set-Alias ~ cuserprofile -Option AllScope
```

## 终端美化

终端美化有两个目标，一是配色及字体，二是Prompt的主题，且支持Git repo的状态指示。

### Windows Terminal

默认的Powershell终端支持有限的颜色，自定义能力极其有限，因而这里选用2019年发布的Windows Terminal来作为终端模拟器。

Windows Terminal可以从Microsoft Store安装。

https://www.microsoft.com/en-us/p/windows-terminal-preview/9n0dx20hk701

Windows Terminal中的Powershell：
![](https://gitee.com/whimian/images/raw/master/20200414152821.png)

点击Windows Terminal的Settings会直接打开`profiles.json`文件。在其中可以进行各种个性化设置。以下是修改后的效果以及其对应的json配置文件。

![](https://gitee.com/whimian/images/raw/master/20200414155343.png)

```json
// To view the default settings, hold "alt" while clicking on the "Settings" button.
// For documentation on these settings, see: https://aka.ms/terminal-documentation

{
    "$schema": "https://aka.ms/terminal-profiles-schema",

    "defaultProfile": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",

    "profiles":
    {
        "defaults":
        {
            // Put settings here that you want to apply to all profiles
        },
        "list":
        [
            {
                // Make changes here to the powershell.exe profile
                "guid": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
                "name": "Windows PowerShell",
                "commandline": "powershell.exe",
                "hidden": false,
                "colorScheme": "Cobalt2",
                "fontFace": "Cascadia Code PL",
            	"background": "#000000",
                "useAcrylic": true,
                "acrylicOpacity": 0.8,
                "fontSize": 10,
                "backgroundImageStretchMode": "none",
                "backgroundImageOpacity": 0.1,
                "backgroundImageAlignment": "bottomRight"

            },
            {
                // Make changes here to the cmd.exe profile
                "guid": "{0caa0dad-35be-5f56-a8ff-afceeeaa6101}",
                "name": "cmd",
                "commandline": "cmd.exe",
                "hidden": false
            },
            {
                "guid": "{b453ae62-4e3d-5e58-b989-0a998ec441b8}",
                "hidden": false,
                "name": "Azure Cloud Shell",
                "source": "Windows.Terminal.Azure"
            }
        ]
    },

    // Add custom color schemes to this array
    "schemes": [
        {
            "name": "Cobalt2",
            "black": "#000000",
            "red": "#ff0000",
            "green": "#38de21",
            "yellow": "#ffe50a",
            "blue": "#1460d2",
            "purple": "#ff005d",
            "cyan": "#00bbbb",
            "white": "#bbbbbb",
            "brightBlack": "#555555",
            "brightRed": "#f40e17",
            "brightGreen": "#3bd01d",
            "brightYellow": "#edc809",
            "brightBlue": "#5555ff",
            "brightPurple": "#ff55ff",
            "brightCyan": "#6ae3fa",
            "brightWhite": "#ffffff",
            "background": "#132738",
            "foreground": "#ffffff"
        }
    ],

    // Add any keybinding overrides to this array.
    // To unbind a default keybinding, set the command to "unbound"
    "keybindings": []
}
```

其中，这里选用的配色主题是`Cobalt2`，它是iTerm Color Schemes中的一个主题，更多适用于Windows Terminal的配色可以从这里下载：
https://github.com/mbadolato/iTerm2-Color-Schemes/tree/master/windowsterminal。
只需要作为字典加入`"schemes"`这里列表里就行了。

这里字体使用了配合Windows Terminal发布的Cascadia字体。https://github.com/microsoft/cascadia-code/releases/download/v1911.21/CascadiaPL.ttf

### oh-my-posh

oh-my-posh (https://github.com/JanDeDobbeleer/oh-my-posh) 收到oh-my-zsh的启发，实现了漂亮的prompt主题。

安装：
```powershell
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser
```
其中，posh-git用于显示git的状态。

安装之后，在powershell的`profiles.ps1`中加入：

```powershell
Import-Module posh-git
Import-Module oh-my-posh
Set-Theme Paradox
```

oh-my-posh支持几个主题（可以在github上找到具体介绍）,这里使用个人认为效果最好的Paradox主题。

安装后的效果如下图：

![](https://gitee.com/whimian/images/raw/master/20200414160521.png)

## 总结

本文通过编辑Powershell的`profiles.ps1`使用powershell函数实现了几个常用的bash命令`ls`、`which`和`touch`，同时介绍了利用oh-my-posh结合Windows Terminal的颜色字体个性化实现prompt主题自定义的方法。