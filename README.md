[中文文档](doc-zh_CN.md)

**Table of Contents**
<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Summary](#summary)
- [Functions](#functions)
- [Quick Start](#quick-start)
	- [Download and Install](#download-and-install)
		- [Raspberry PI](#raspberry-pi)
		- [Linux](#linux)
		- [macOS](#macos)
		- [Windows](#windows)
	- [Move to PATH (optional)](#move-to-path-optional)
- [Usage Manual](#usage-manual)
	- [Show help](#show-help)
	- [Login to IoT server](#login-to-iot-server)
	- [Login to default IoT server](#login-to-default-iot-server)
	- [Login to specific IoT server](#login-to-specific-iot-server)
	- [List projects](#list-projects)
	- [List devices](#list-devices)
	- [Search images](#search-images)
	- [Build RPI Application Image](#build-rpi-application-image)
	- [Push RPI Application Image to IoT Registry](#push-rpi-application-image-to-iot-registry)
	- [Display Version](#display-version)
- [FAQ](#faq)
- [Feedback](#feedback)
- [Contact](#contact)
- [Licence](#licence)

<!-- /TOC -->

# Summary
iotcli is a command line tool to interact with Linker [iot-server][1], which is the
back-end program supporting the IoT platform.

iotcli is written in Go and can run on multiple platforms (Linux, Windows, macOS and ARM).

# Functions
iotcli supports the following functions:

* Login to iot-server
* List projects
* List devices
* Build RPI application image
* Push RPI application image
* Search RPI app images from IoT Docker Registry(both public and logged-in user's images)

Build image and push image is only currently available on RPI (ARM v7) with Docker installed.

> RPI is abbr for Raspberry PI

# Quick Start

## Download and Install
Click the GitHub 'Release' page and download the prebuilt binary for your platform,
unarchive it and execute 'iotcli' directly on your machine.

### Raspberry PI
Download [iotcli_v1.0.2_Linux-ARMv7.tar.gz][dl-arm]
### Linux
Download [iotcli_v1.0.2_Linux-amd64.tar.gz][dl-linux]
### macOS
Download [iotcli_v1.0.2_macOS-amd64.tar.gz][dl-mac]
### Windows
Download [iotcli_v1.0.2_Windows-amd64.zip][dl-win]

## Move to PATH (optional)
You can move 'iotcli' under one of system environment $PATH folder, for example `/usr/local/bin` or `/bin` on Unix-like systems. In this way,
you can access iotcli anywhere in the console. Otherwise, you need to use `./iotcli` to execute it.

# Usage Manual

## Show help
Open a terminal and run `iotcli` or `iotcli --help` to print usage help.

```
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

Subcommands like 'build' are standalone modules of iotcli. You can use 'iotcli <subcommand> --help' to display help for subcommands.

## Login to IoT server
You may need to login to IoT server before you run 'iotcli projects', 'iotcli devices' and
'iotcli push'.

The Email and password you will input are same ones you input on Linker IoT webpage.

The login session is valid in 1 hour. You need to login again when session expired.

The login session contains your email address and secure token, iotcli has encrypted them and saved on your local machine safely.

## Login to default IoT server
The default IoT server address is `app.dcos.iotfactory.io:3000`.
Run the following command to login:

`./iotcli login`

Success output:
```sh
Login to default IoT Server(app.dcos.iotfactory.io:3000) with your IoT account.
Email: someone@email.com
Password:
Login Success
```

Failed output:
```sh
Login to default IoT Server(app.dcos.iotfactory.io:3000) with your IoT account.
Email: invalid@account.com
Password:
Login error: invalid email or password
```

## Login to specific IoT server
If you know another IoT server address and wanna login, set it on the argument.

`./iotcli login 192.168.41.16:3000`

```sh
Login to IoT Server(192.168.41.16:3000) with your IoT account.
Email: someone@email.com
Password:
Login Success
```

## List projects
`./iotcli projects`

Success output:
```sh
PROJECT	ALIAS	ARCHITECT	CREATED		DOWNLOAD
iot	    iot	    rpi3-armv7l	3 weeks ago	<NULL>
```

## List devices
`./iotcli devices`

Success output:
```sh
DEVICE     STATUS    PROJECT   HOSTNAME       IP               ARCH                          CPU      MEM      DISK
M0CcpcCj   active    iot       raspberrypi2   192.168.10.240   ARMv7 Processor rev 4 (v7l)   1.66%    22.50%   92.88%
b1TYLzeM   stopped   iot       raspberrypi    192.168.10.248   ARMv7 Processor rev 4 (v7l)   23.70%   59.98%   92.88%
```

## Search images
`./iotcli search nginx`

Success output:
```sh
NAME	TAG	OFFICIAL	DESCRIPTION	TIMESTAMP
public/nginx	latest	No	None	2 months ago
someone/nginx	latest	No	None	4 weeks ago
```

## Build RPI Application Image
> This command works only on Raspberry PI (RPI, ARMv7) devices.

This command is a wrapper for `docker build`, so you need to firstly enter a
directory containing the RPI application source codes and a Dockerfile.
And then execute this command.

`./iotcli build shortImageName:[TAG]`

For example:

`./iotcli build dht11`

`./iotcli build nginx:1.2`

Success output:
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

A temporary image called 'dht11' is built.

## Push RPI Application Image to IoT Registry
> This command works only on Raspberry PI (RPI, ARMv7) devices.

This command is a wrapper for `docker tag` and `docker push`.
It can help you tag image and push image to Linker IoT Registry automatically, which means you don't need to
run something like `docker tag dht11 <iot-registry-address>/<username>/dht11` manually.

When you run `iotcli push <image>` command, it will do the following:
1. Run 'docker tag' to rename image (from short name to full name registry address and username).
2. Try to login to IoT registry with username and password from the login session;
If you are not logged in, or if the token has expired, iotcli will print some hint and stop.
3. Run 'docker push' to upload the image.
4. Run 'docker rmi' to unplug the temporary image (the short name one).

`./iotcli push shortImageName:[TAG]`

For example:

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

Refresh IoT Portal page and check if 'dht11' exists in your personal application list.

## Display Version
`./iotcli version`

Success output:
```sh
iotcli release v1.0.0
Git commit:	187346f
Build with:	go version go1.9 darwin/amd64
Built on:	2017/09/14 15:25:59
```

# FAQ
## 1. login error when execute 'iotcli push \<image\>'

**Error Message:**
```
# iotcli push dht11
========> Executing [docker tag dht11 app.dcos.iotfactory.io:5000/someone/dht11]
========> Executing [docker login -u someone -p ****** app.dcos.iotfactory.io:5000]
Email: EOF
wait for cmd error: exit status 1
login to registry error: exit status 1
========> Executing [docker push app.dcos.iotfactory.io:5000/someone/dht11]
(skip)
```

**Reason:**
Docker login failed, because it do not support syntax like `docker login -u someone -p ****** app.dcos.iotfactory.io:5000`

**Solution:**

Check `docker version` and if it is < 1.11, upgrade to docker v1.12+ (v1.12.1 recommended).

# Feedback
If you are familiar with GitHub issue tracking flow, try to open an issue!

# Contact


# Licence

[1]: https://bitbucket.org/linkernetworks/iot-server
[dl-linux]: https://github.com/LinkerNetworks/iotcli-release/releases/download/v1.0.2/iotcli_v1.0.2_Linux-amd64.tar.gz
[dl-arm]: https://github.com/LinkerNetworks/iotcli-release/releases/download/v1.0.2/iotcli_v1.0.2_Linux-ARMv7.tar.gz
[dl-mac]: https://github.com/LinkerNetworks/iotcli-release/releases/download/v1.0.2/iotcli_v1.0.2_macOS-amd64.tar.gz
[dl-win]: https://github.com/LinkerNetworks/iotcli-release/releases/download/v1.0.2/iotcli_v1.0.2_Windows-amd64.zip
