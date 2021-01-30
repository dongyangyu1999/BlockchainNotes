# Command

## 初始化新VM

```bash
vagrant init boxName # 如ubuntu/trustry64
```

此命令会在当前目录创建一个名为Vagrantfile的配置文件，内容大致如下：

```
Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
end
```

当在此目录启动Vagrant后，Vagrant会从互联网下载“ubuntu/trusty64”这个box到本地，并使用它作为VM的映像。但是由于官网镜像源不在国内，所以可先下载到本地再安装，[详情](######本地添加box，默认不带版本号)

## 添加Box

```BASH
# 添加box
vagrant box add boxName boxPath/url

# 通过指定的URL添加远程box
# 如：vagrant box add https://atlas.hashicorp.com/ubuntu/boxes/trusty64
# 添加一个本地box
# 如：vagrant box add CentOS7.1 D:/Work/VagrantBoxes/CentOS-7.1.1503-x86_64-netboot.box

# 列出所有的box
vagrant box list
```

## 启动VM

如果我们想启动任意VM，首先进入有`Vagrantfile`配置文件的目录，然后执行下方命令。

```BASH
vagrant up
```

控制台的输出通常如下：

```bash
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'ubuntu/trusty64-juju' could not be found. Attempting to find a
nd install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Loading metadata for box 'ubuntu/trusty64-juju'
    default: URL: https://atlas.hashicorp.com/ubuntu/trusty64-juju
==> default: Adding box 'ubuntu/trusty64-juju' (v20160707.0.1) for provider: vir
tualbox
    default: Downloading: https://atlas.hashicorp.com/ubuntu/boxes/trusty64-juju
/versions/20160707.0.1/providers/virtualbox.box
==> default: Waiting for cleanup before exiting...

```

## 启用SSH登录VM

> 要注意，本机上必须先安装SSH客户端。
>
> 宿主机的本地目录在`/vagrant`路径下，也就是`Vagrantfile`所在的目录

```BASH
vagrant ssh # 以vagrant用户直接登入虚拟机中
```

注意：root用户没有默认密码，也不能直接登录，需要root权限的命令可以通过在命令前添加`sudo`来执行。或者通过执行`sudo -i`直接切换到`root`用户



## 关闭VM

> 需要进入`Vagrantfile`配置文件所在的目录

```BASH
vagrant halt # 关闭虚拟机
```



## 其余命令

```BASH
vagrant status # 查看当前vagrant状态

vagrant global-status #查看全局的虚拟机状态，可才看到VM的id
vagrant destroy [name|id] # 销毁VM
# 如：vagrant destroy ubuntu/trusty64
# 此命令会停止VM的运行，并销毁所有创建的资源。


```



# Command FAQ

###### 本地添加box，默认不带版本号

> 由于从[官网](https://app.vagrantup.com/boxes/search)下载box特别慢，所以通常我们通过国内镜像下载box到本地进行安装。

```BASH
vagrant box add boxName boxPath 
#如vagrant box add fedora/32-cloud-base /usr/local/Dowanloads/fedora/32-cloud-base.box
```

但是这样本地添加的box镜像默认不带版本号，查看已安装box

```BASH
vagrant box list # 查看已安装box列表
# 显示如下
# fedora/32-cloud-base (virtualbox, 0)
```

**解决办法**

1. 新建配置文件`metadata.json`和下载的box放在同级目录

2. 编辑`metadata.json`，添加以下内容

   ```BASH
   {
       "name": "fedora/32-cloud-base", #添加后的box名称
       "versions": 
       [
           {
               "version": "32.20200422.0",#版本号
               "providers": [
                   {
                     "name": "virtualbox",
                     "url": "fedora/32-cloud-base.box"#下载到本地的box路径
                   }
               ]
           }
       ]
   }
   ```

3. ```BASH
   # 执行一下命令
   vagrant box add metadata.json
   ```



###### 运行docker提示“不能连接docker daemo（后台程序）”

**解决方法**

```BASH
# 切换到管理员权限
sudo -i

# 重新加载后台程序
systemctl daemon-reload
systemctl start docker  # 启动docker
```





# Vagrantfile

>  Vagrantfile的主要功能是描述项目所需的机器类型，以及如何配置和供应这些机器。 
>
> `Vagrantfile` 主要包括三个方面的配置，虚拟机的配置、SSH配置、Vagrant 的一些基础配置。Vagrant 是使用 Ruby 开发的，所以它的配置语法也是 Ruby，具体可以查阅官方[文档](https://www.vagrantup.com/docs/vagrantfile)。

可以通过`vagrant init`生成Vagrantfile模板文件，Vagrantfile 将大致采用以下格式：

```
Vagrant.configure("2") do |config|
  # ...
end
```

上面示例中第一行的`“2”`代表配置对象 config 的版本，该配置将用于该块的配置（`do` 和 `end` 之间的部分）。这个对象在不同版本之间可能差异很大。

> 目前只支持两个版本：“1”和“2”。版本 1 代表 Vagrant 1.0.x 的配置。“2”代表 1.1+ 至 2.0.x 的配置。

## 配置详解

下面是一些常用的配置：

`config.vm.hostname`：配置虚拟机主机名
`config.vm.network`：这是配置虚拟机网络，由于比较复杂，我们其后单独讨论
`config.vm.synced_folder`：除了默认的目录绑定外，还可以手动指定绑定
`config.ssh.username`：默认的用户是vagrant，从官方下载的box往往使用的是这个用户名。如果是自定制的box，所使用的用户名可能会有所不同，通过这个配置设定所用的用户名。
`config.vm.provision`：我们可以通过这个配置在虚拟机第一次启动的时候进行一些安装配置



### 给虚拟机命名

* 使用`config.vm.define`给虚拟机指定名称，而不是默认的default。
* `config.vm.hostname`修改虚拟机操作系统在的主机名
* `vb.name`修改的是virtualbox(或hyperv)中显示的虚拟机的名称，对于virtualbox，可使用`name`属性来修改，对于hyperv，可使用`vmname`属性来修改

```bash
Vagrant.configure('2') do |config|
  config.vm.box = ...
  config.vm.define "myVM" # 没有等号
  config.vm.hostname = "dyy_PC"
  config.vm.provider :virtualbox do |vb|
    vb.name = "ubuntu2004"
  end
end
```

**效果如下：**

```BASH
$ vagrant global-status
id       name                    provider   state   directory
----------------------------------------------------------------------------------------
3cc30cd  myVM                    virtualbox running V:/vagrant_test/test

$ vagrant ssh
vagrant@dyy_PC:~$ hostname
dyy_PC
```

![img](Vagrant%E6%8C%87%E4%BB%A4.assets/2020_10_14_1602609713763.png)