---
layout: post
title: Windows 10 安装 OpenSSH
date: 2017-11-23 09:00
category: CS
tags: ssh, windows
---

## 安装Win32-OpenSSH

- 下载最新的发布版本 <https://github.com/PowerShell/Win32-OpenSSH/releases>
- 解压到`C:\Program Files\OpenSSH`
  - 可以将该目录加入Path
- 以管理员权限安装`SSHD`和`ssh-agent`服务:

	`powershell.exe -ExecutionPolicy Bypass -File install-sshd.ps1`

- 生成Server Keys并且限制这些文件的访问权限，以管理员权限运行：

	```
	.\ssh-keygen.exe -A
	powershell.exe -ExecutionPolicy Bypass -File .\FixHostFilePermissions.ps1 -Confirm:$false
    ```

- 在Windows防火墙中为SSH服务器开启一个port：
	- 可以执行如下命令：

	```
	New-NetFirewallRule -Protocol TCP -LocalPort 22 -Direction Inbound -Action Allow -DisplayName SSH
	```

	- 或者直接在防火墙图形界面中增加一个port 22的规则。
- 开启服务：

	`net start sshd`

	- 设置默认开启服务

	```
	Set-Service sshd -StartupType Automatic
	Set-Service ssh-agent -StartupType Automatic
	```

- 设置默认ssh Shell：
	在服务器端的windows registry中设置ssh的默认shell，

	```
	Computer\HKEY_LOCAL_MACHINE\SOFTWARE\OpenSSH\DefaultShell
	```

	![registry](https://gitee.com/whimian/images/raw/master/registry_for_ssh.png
)

## 设置使用SSH秘钥登陆

安装后，可以直接使用用户名和密码方式从client登陆到host上：

`ssh user@host`

要使用密匙登陆需要采用以下步骤来设置：

### 在client端：

1. 生成key pair （即private client key 和 public client key）

- 在`$HOME`中建立`.ssh`目录

	```
	mkdir $HOME\.ssh
	cd $HOME\.ssh
	```

- 生成key pair

	`ssh-keygen -t rsa -f id_rsa`

id_rsa 为key文件名，生成时会提示输入passphrase来保护private key，也可以为空。

2. 在ssh-agent上注册private key

- 运行源码中的`FixUserFilePermissions.ps1`来对进行权限设置

	`FixUserFilePermissions.ps1`

- 开启ssh-agent:

	`net start ssh-agent`

- 将key注册到`ssh-agent`

	`ssh-add id_rsa`

3. 使用private key来登陆host

	`ssh -i .\id_rsa user@host`

### 在服务器端

1. 同样在服务器端的用户目录`$HOME`下建立`.ssh\`，并在其中建立`authorized_keys`文件：

	```
	touch .ssh\authorized_keys
	mkdir .ssh\other_keys
	```

	将之前客户端上生成的public client key 即 `id_rsa.pub`文件，发送到host上的`$HOME\.ssh\other_keys\`文件夹下，在服务器上，将这个`id_ras.pub`内容写入`authorized_keys`文件中。

	`cat .\other_keys\id_rsa.pub > authorized_keys`

	*`authorized_keys`文件的编码要为UTF-8，而非windows默认编码*

2. 设置`authorized_keys`的访问权限

	`FixUserFilePermissions.ps1`
