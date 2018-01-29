---
title: go-ethereum 搭建本地开发环境
date: 2018-01-29 19:48:57
categories:
- 区块链
tags:
- go-ethereum
- 区块链
---
# 1. 使用 Vagrant
> 目的：使用 Vagrant 可以方便跨平台开发，可定制一致的开发环境，共享给团队其他成员使用。

## 1.1 安装 Vagrant
软件版本：Vagrant 2.0.0。
去 Vagrant（[https://www.vagrantup.com/](https://www.vagrantup.com/)）官网下载相应的软件，当时最新版本为 Vagrant 2.0.0。
安装完成后，可以使用下面命令进行验证：
```
vagrant version
```

## 1.2 安装 VirtualBox
软件版本：VirtualBox 5.0（5.0 以上版本不兼容 Vagrant 2.0.0）。
去 VirtualBox（[https://www.virtualbox.org/](https://www.virtualbox.org/)）官网下载相应的软件，注意要兼容 Vagant 对应的版本。

## 1.3 使用 CentOS 6.5
去 [http://www.vagrantbox.es/](http://www.vagrantbox.es/) 下载 CentOS 6.5 x86_64，创建 vagrant 目录，将下载的 box 放到这个目录下，然后使用命令，进入 vagrant 目录下，执行下面命令进行添加名称为 base 的 box，引用 vagrant-centos-6.5.box：
```
vagrant box add base vagrant-centos-6.5.box
```
执行下面命令进行初始化 box，默认为名称为 base 的 box，如果 box 不是 base，需要指定 box 的名称：
```
vagrant init
```
执行下面命令进行开机：
```
vagrant up
```
执行下面命令进行登陆系统：
```
vagrant ssh
```
执行下面命令进行关机：
```
vagrant halt
```

## 1.4 修改 Vagrantfile 配置
添加下面配置，分配虚拟机 IP 和名称：
```
config.vm.network :private_network, ip: "11.11.11.11"
config.vm.hostname = "tron01"
```
添加完成后，需要执行下面命令进行加载:
```
vagrant reload
```

## 1.5 共享工作目录
Vagrant 这里会把本地的 vagrant 目录映射到虚拟机的 /vagrant 目录，所以在本地只需要把文件放到 vagrant 目录就可以在虚拟机的 /vagrant 目录下看到，进行操作。

# 2. 搭建 go-ethereum 开发环境
## 2.1 安装 Git
安装 Git，生成 SSH Key 备用。

## 2.2 安装 Golang
Golang 版本为 1.9。去 [https://dl.gocn.io/](https://dl.gocn.io/) 下载相应的 Go，本地安装一个，用于本地开发提示用，对应的虚拟机安装一个，用于编译运行。配置好 GOPATH 和 GOROOT。这里虚拟机的 GOPATH 的绝对路径为：/vagrant/workspace/，本地的 GOPATH 相对路径为：vagrant/workspace，这样就可以本地开发，虚拟机编译运行了。

## 2.3. 下载 go-ethereum 源码
在本地相对路径：vagrant/workspace/src/ 下执行下面命令进行下载 go-ethereum 源码：
```
git clone https://github.com/ethereum/go-ethereum
```

## 2.4. 编译 geth
在虚拟机绝对路径 /vagrant/workspace/src/github.com/ethereum/go-ethereum 目录下执行下面命令进行编译 geth：
```
go install -v ./cmd/geth
```
稍后会在 /vagrant/worspace/bin 目录下生成 geth 可执行程序。执行下面命令进行验证：
```
geth version
```

