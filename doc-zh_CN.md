[Document in English](README.md)

**目录**
<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [概述](#概述)
- [功能](#功能)
- [快速开始](#快速开始)
	- [下载和安装](#下载和安装)
		- [Raspberry PI](#raspberry-pi)
		- [Linux](#linux)
		- [macOS](#macos)
		- [Windows](#windows)
	- [添加到 PATH 中（可选）](#添加到-path-中可选)
- [使用手册](#使用手册)
	- [显示帮助](#显示帮助)
	- [登录到 IoT 服务端](#登录到-iot-服务端)
	- [登录到默认 IoT 服务端](#登录到默认-iot-服务端)
	- [登录到指定 IoT 服务端](#登录到指定-iot-服务端)
	- [列出项目](#列出项目)
	- [列出设备](#列出设备)
	- [搜寻镜像](#搜寻镜像)
	- [构建 RPI 应用镜像](#构建-rpi-应用镜像)
	- [上传 RPI 应用镜像到 IoT 仓库](#上传-rpi-应用镜像到-iot-仓库)
	- [显示版本](#显示版本)
- [常见问题](#常见问题)
- [反馈](#反馈)
- [联系方式](#联系方式)
- [Licence](#licence)

<!-- /TOC -->

# 概述
iotcli 是个与 Linker IoT 平台后端程序 [iot-server][1] 交互的命令行工具。

iotcli 用 Go 编写，可以在多个平台上运行（Linux，Windows，macOS 和 ARM）。

# 功能
iotcli 支持下列功能：

* 登录到 iot-server
* 列出项目
* 列出设备
* 构建 RPI 应用镜像
* 上传 RPI 应用镜像
* 从 IoT Docker 镜像仓库中搜索镜像（公有的和登录用户的）

构建镜像和上传镜像两个功能现在只在 ARM 平台上可用。

> RPI 是树莓派（Raspberry PI）的缩写。

# 快速开始
## 下载和安装
点击 GitHub 的 “Release” 页面，选择您的平台，并下载预先构建的二进制程序。
下载完成后，解压缩它，并直接在你的机器上运行 “iotcli” 工具。

### Raspberry PI
下载 [iotcli_v1.0.0_Linux-ARMv7.tar.gz][dl-arm]
### Linux
下载 [iotcli_v1.0.0_Linux-amd64.tar.gz][dl-linux]
### macOS
下载 [iotcli_v1.0.0_macOS-amd64.tar.gz][dl-mac]
### Windows
下载 [iotcli_v1.0.0_Windows-amd64.zip][dl-win]

## 添加到 PATH 中（可选）
您可将 “iotcli” 移动到系统环境变量 $PATH 的某个目录下，比如类 Unix 系统的 `/usr/local/bin` 或 `/bin` 目录。这样的话，就可以在命令行
中任意位置运行 iotcli。否则需要使用 `./iotcli` 去执行它。

# 使用手册
## 显示帮助
打开控制台（命令行模拟器），运行 “iotcli” 或者 “iotcli --help” 打印帮助信息。

```sh
Linker IoT Command Line Tool.

Usage:
  iotcli [flags]
  iotcli [command]

Available Commands:
  build       (Unavailable) Build RPI application Docker image
  devices     List devices of the logged in user
  help        Help about any command
  login       Login to IoT server
  projects    List projects of the logged in user
  push        (Unavailable) Push image to IoT Registry
  search      Search images
  version     Print version of iotcli

Flags:
  -h, --help   help for iotcli

Use "iotcli [command] --help" for more information about a command.
```

像 “build” 这样的子命令是 iotcli 独立的模块。你可以使用 “iotcli <子命令> --help” 来显示子命令使用说明。

## 登录到 IoT 服务端
在运行 “iotcli projects”，“iotcli devices” 和 “iotcli push” 之前，你
可能需要先登录到 IoT 服务端。

下面将要输入的 Email 和 Password 就是你在 IoT 平台网页上输入的那个账户。

登录会话有效期是 1 小时。在会话失效后，你需要再次登录。

登录会话中包含你的邮箱地址和安全令牌，iotcli 已经将它加密并安全地存储在您的机器上。

## 登录到默认 IoT 服务端
默认的 IoT 服务端地址为 `app.dcos.iotfactory.io:3000`。执行下面命令登录它：

`./iotcli login`

成功后输入：
```sh
Login to default IoT Server(app.dcos.iotfactory.io:3000) with your IoT account.
Email: someone@email.com
Password:
Login Success
```

错误输出：
```sh
Login to default IoT Server(app.dcos.iotfactory.io:3000) with your IoT account.
Email: invalid@account.com
Password:
Login error: invalid email or password
```

## 登录到指定 IoT 服务端
如果你知道另一个 IoT 服务端地址，并想登入，将它设置到参数上。

`./iotcli login 192.168.41.16:3000`

```sh
Login to IoT Server(192.168.41.16:3000) with your IoT account.
Email: someone@email.com
Password:
Login Success
```

## 列出项目
`./iotcli projects`

成功后输出：
```sh
PROJECT	ALIAS	ARCHITECT	CREATED		DOWNLOAD
iot	    iot	    rpi3-armv7l	3 weeks ago	<NULL>
```

## 列出设备

`./iotcli devices`

成功后输入：
```sh
DEVICE     STATUS    PROJECT   HOSTNAME       IP               ARCH                          CPU      MEM      DISK
M0CcpcCj   active    iot       raspberrypi2   192.168.10.240   ARMv7 Processor rev 4 (v7l)   1.66%    22.50%   92.88%
b1TYLzeM   stopped   iot       raspberrypi    192.168.10.248   ARMv7 Processor rev 4 (v7l)   23.70%   59.98%   92.88%
```

## 搜寻镜像
`./iotcli search nginx`

成功后输出:
```sh
NAME	TAG	OFFICIAL	DESCRIPTION	TIMESTAMP
public/nginx	latest	No	None	2 months ago
someone/nginx	latest	No	None	4 weeks ago
```

## 构建 RPI 应用镜像
> 这个命令只在树莓派（RPI, ARMv7）上可用。

这个命令是 `docker build` 的一个替代，你需要先进入到一个包含 Dockerfile 和树莓派应用源码的目录，然后执行这个命令。

`./iotcli build shortImageName:[TAG]`

例如：

`./iotcli build dht11`

`./iotcli build nginx:1.2`

成功后输出：
```sh
========> Executing [docker build -t dht11 .]
Sending build context to Docker daemon 7.68 kB
Step 1 : FROM armbuild/alpine
---> 4c5200d71b67
Step 2 : ENV PATH $PATH:/root
---> Using cache
---> 0daff08c4b39
Step 3 : WORKDIR /root
...
Step 9 : ENTRYPOINT dht11.sh
---> Using cache
---> 1d1b7792499e
Successfully built 1d1b7792499e
Use 'iotcli push dht11' to push image to IoT Registry
```

一个叫做 “dht11” 的镜像会被构建出来。

## 上传 RPI 应用镜像到 IoT 仓库
> 这个命令只在树莓派（RPI, ARMv7）上可用。

这个命令包装了 “docker tag” 和 “docker push”。可以帮助你自动重命名镜像，
并将镜像上传到 Linker IoT 镜像仓库中。你无需手动执行 `docker tag dht11 <iot-registry-address>/<username>/dht11` 这样的操作。

当你运行 `iotcli push <image>` 命令时，会发生以下事情：
1. 调用 “docker tag” 重命名镜像（从短名称到包含仓库地址和用户名的全名称）。
2. 尝试使用登录会话中的用户名和密码登入 IoT 镜像仓库；如果登录失败，会打印出重新登录的提示。
3. 调用 “docker push” 上传镜像。
4. 调用 “docker rmi” 删除临时的镜像（短名称的那个）。

`./iotcli push shortImageName:[TAG]`

例如:

`./iotcli push dht11`

`./iotcli push nginx:1.2`

Success output:
```sh
========> Executing [docker tag dht11 app.dcos.iotfactory.io:5000/yourname/dht11]
========> Executing [docker login -u yourname -p ****** app.dcos.iotfactory.io:5000]
WARNING: login credentials saved in /root/.docker/config.json
Login Succeeded
========> Executing [docker push app.dcos.iotfactory.io:5000/yourname/dht11]
The push refers to a repository [app.dcos.iotfactory.io:5000/yourname/dht11]
9f3cc2957c8f: Preparing
fb576ba69e79: Preparing
...
8d49fe7d1ea3: Pushed
a1e32923f46e: Pushed
a1e32923f46e: Pushed
latest: digest: sha256:b54498cbc6af755f55c827767a271a07d1dff1b31fa8fa017d46c8ffde81e9b0 size: 1759
========> Executing [docker rmi dht11]
Untagged: dht11:latest
```

刷新 IoT 网页，并查看 “dht11” 是否存在于你个人应用的列表中。

## 显示版本
`./iotcli version`

```sh
iotcli release v1.0.0
Git commit:	187346f
Build with:	go version go1.9 darwin/amd64
Built on:	2017/09/14 15:25:59
```

# 常见问题

# 反馈
如果对 GitHub Issue tracking 流程比较熟悉，可以直接创建一个 Issue ！

# 联系方式

# Licence

[1]: https://bitbucket.org/linkernetworks/iot-server
[dl-linux]: https://github.com/LinkerNetworks/iotcli-release/releases/download/v1.0.0/iotcli_v1.0.0_Linux-amd64.tar.gz
[dl-arm]: https://github.com/LinkerNetworks/iotcli-release/releases/download/v1.0.0/iotcli_v1.0.0_Linux-ARMv7.tar.gz
[dl-mac]: https://github.com/LinkerNetworks/iotcli-release/releases/download/v1.0.0/iotcli_v1.0.0_macOS-amd64.tar.gz
[dl-win]: https://github.com/LinkerNetworks/iotcli-release/releases/download/v1.0.0/iotcli_v1.0.0_Windows-amd64.zip
