# 什么是vagrant？

vagrant是一个工具，用于创建和部署虚拟化开发环境的。

拿VirtualBox举例，VirtualBox会开放一个创建虚拟机的接口，Vagrant会利用这个接口创建虚拟机，并且通过Vagrant来管理，配置和自动安装虚拟机。



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



## 销毁VM虚拟机

```BASH
vagrant destroy [name|id] # 销毁VM
# 如：vagrant destroy ubuntu/trusty64
# 此命令会停止VM的运行，并销毁所有创建的资源。
```

## 建立快照

使用Vagrant的快照功能可以很方便快速的创建当前虚拟机的一个临时备份状态，在进行重要操作时可以先创建一个快照以便在操作失误后快速恢复。

安装Vagrant快照插件：

```bash
$ vagrant plugin install vagrant-vbox-snapshot

$ vagrant snapshot
Usage: vagrant snapshot <command> [<args>]
 
Available subcommands:
     back
     delete
     go
     list
     take
 
For help on any individual command run `vagrant snapshot <command> -h
```

使用方法

```BASH
# 创建一个快照
vagrant snapshot take "Name"
# 查看快照列表
vagrant snapshot list
# 从指定快照中恢复
vagrant snapshot go "Name"
# 删除一个快照
vagrant snapshot delete "Name"
```





## 查看状态

```BASH
vagrant status # 查看当前vagrant状态

vagrant global-status #查看全局的虚拟机状态，可才看到VM的id
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

`do … end` 为配置的**开始结束符**，所有配置信息都写在这两段代码之间。 `config`是为**当前配置命名**，你可以指定任意名称，如`myvmconfig`，在后面引用的时候，改为自己的名字即可。

## 配置详解

下面是一些常用的配置：

`config.vm.hostname`：配置虚拟机主机名

`config.vm.network`：这是配置虚拟机网络，比较复杂。
`config.vm.synced_folder`：除了默认的目录绑定外，还可以手动指定绑定
`config.ssh.username`：默认的用户是vagrant，从官方下载的box往往使用的是这个用户名。如果是自定制的box，所使用的用户名可能会有所不同，通过这个配置设定所用的用户名。
`config.vm.provision`：我们可以通过这个配置在虚拟机第一次启动的时候进行一些安装配置

## 1.定义vm的configure配置节点(一个节点就是一个虚拟机) 

```ruby
Vagrant.configure("2") do |config|

  config.vm.define "web" do |web_config|
    web_config.vm.box = "apache"
  end
    
  config.vm.define "db" do |db|
    db.vm.box = "mysql"
  end

end
```

`3-6行`表示在config配置中，定义了一个名为web的vm配置，该节点下的配置信息命名为`web_config`；如果该Vagrantfile配置文件只定义了一个vm，这个配置节点层次可忽略。

当定义了多主机之后，在使用vagrant命令的时候，就**需要加上主机名**，例如`vagrant ssh web`；也有一些命令，如果你不指定特定的主机，那么将会对所有的主机起作用，比如`vagrant up`。

## 2. vm提供者配置

```RUBY
config.vm.provider :virtualbox do |vb|
    # ...
end
```

虚机容器提供者配置，对于不同的provider，特有的一些配置，此处配置信息是针对virtualbox定义一个提供者，命名为vb，跟前面一样，这个名字随意取，只要节点内部调用一致即可。

配置信息又分为通用配置和个性化配置，通用配置对于不同provider是通用的，常用的**通用配置**如下：

```RUBY
config.vm.provider :virtualbox do |vb|
  #指定vm-name，也就是virtualbox管理控制台中的虚机名称。如果不指定该选项会生成一个随机的名字，不容易区分。
  vb.name = "centos7"

  # vagrant up启动时，是否自动打开virtual box的窗口，缺省为false
  vb.gui = true

  #指定vm内存，单位为MB
  vb.memory = "1024"

  #设置CPU个数
  vb.cpus = 2

end
```

上面的provider配置是通用的配置，针对不同的虚拟机，还有一些的个性的配置，通过vb.customize配置来定制。

对virtual box的个性化配置，可以参考：VBoxManage modifyvm 命令的使用方法。详细的功能接口和使用说明，可以参考[virtualbox官方文档](http://www.virtualbox.org/manual/)。

## 3. 同步目录设置

首先要安装一个插件：

```ruby
vagrant plugin install vagrant-vbguest
```

然后修改配置文件：

```ruby
config.vm.synced_folder "D:/xxx/code", "/home/www/" 
```

前面的路径(D:/xxx/code)是本机代码的地址，后面的地址就是虚拟机的目录。虚拟机的/vagrant目录默认挂载宿主机的开发目录(可以在进入虚拟机机后，使用`df -h` 查看)，这是在虚拟机启动时自动挂载的。我们还可以设置额外的共享目录，上面这个设定，第一个参数是宿主机的目录，第二个参数是虚拟机挂载的目录。

> **选项**
>
> - `SharedFoldersEnableSymlinksCreate (boolean)`：默认是 true。如果为 false，将禁用与指定的 virtualbox 共享目录创建符号链接的功能。

注意事项：

1. **使用VirtualBox启用双向文件夹同步** [参考](http://www.voidcn.com/article/p-fbopifvv-bug.html)

根据文档,当未指定Vagrantfile中的类型选项config.vm.synced_folder参数时,Vagrant会尝试选择最佳可用选项：

> `type` (string) – The type of synced folder. If this is not specified, Vagrant will automatically choose the best synced folder option for your environment. Otherwise, you can specify a specific type such as “nfs”.

为了使文件夹以双向同步,我在Vagrantfile中添加了显式定义：

```ruby
config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
```

当然,这只适用于VirtualBox。双向同步对于工作流很有用,其中客户机上的应用程序创建文件

2. Vagrant 如果在共享目录的时候无法双向同步，在启动的时日志提示信息，提示系统缺失`Virtualbox Guest Additions`插件

```
No guest additions were detected on the base box for this VM!
```

​	解决方法

​	安装`vagrant-vbguest`插件，重新启动虚拟机后会自动在虚拟机里面编译安装`Virtualbox Guest Additions`插件

```bash
$ vagrant plugin install vagrant-vbguest
# 可选
$ vagrant vbguest
```

## 4. 端口转发设置

```RUBY
config.vm.network :forwarded_port, guest: 80, host: 8080
```

上面的配置把**宿主机**上的8080端口映射到**客户虚拟机**的80端口，例如你在虚拟机上使用nginx跑了一个Go应用，那么你在host上的浏览器中打开`http://localhost:8080`时，Vagrant就会把这个请求转发到虚拟机里跑在80端口的nginx服务上。不建议使用该方法，因为涉及端口占用问题，常常导致应用之间不能正常通信，建议使用Host-only和Bridge方式进行设置。

`guest`和`host`是必须的，还有几个可选属性：

* guest_ip：字符串，vm指定绑定的Ip，缺省为0.0.0.0
* host_ip：字符串，host指定绑定的Ip，缺省为0.0.0.0
* protocol：字符串，可选TCP或UDP，缺省为TCP







## 5. provision任务

你可以编写一些命令，让vagrant在启动虚拟机的时候自动执行，这样你就可以省去手动配置环境的时间了。

provision任务是预先设置的一些操作指令，格式：

```RUBY
config.vm.provision 命令字 json格式参数

config.vm.provion 命令字 do |s|
    s.参数名 = 参数值
end
```

每一个 `config.vm.provision 命令字 ` 代码段，我们称之为一个`provisioner`。

根据任务操作的对象，provisioner可以分为：

* Shell
* File
* Ansible
* CFEngine
* Chef
* Docker

* Puppet

* Salt

根据vagrantfile的层次，分为：

* configure级：它定义在 `Vagrant.configure("2") `的下一层次，形如： `config.vm.provision ...`

* vm级：它定义在 `config.vm.define "web" do |web|` 的下一层次，`web.vm.provision ...`

执行的顺序是先执行configure级任务，再执行vm级任务，即便configure级任务在vm定义的下面定义。例如：

```RUBY
Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: "echo foo"

  config.vm.define "web" do |web|
    web.vm.provision "shell", inline: "echo bar"
  end

  config.vm.provision "shell", inline: "echo baz"
end
```

输出结果：

```bash
==> default: "foo"
==> default: "baz"  # 先输出config级
==> default: "bar"
```

### shell如何执行

- 启动时自动执行，缺省地，任务只执行一次，第二次启动就不会自动运行了。如果需要每次都自动运行，需要为provision指定`run:"always"`属性
- 启动时运行，在启动命令加 `--provision` 参数,适用于 `vagrant up` 和 `vagrant reload`
- vm启动状态时，执行 `vagrant provision` 命令。

在编写provision任务时，可能同时存在几种类型的任务，但执行时可能只执行一种，如，我只执行shell类型的任务。可以如下操作：

```bash
vagrant provision --provision-with shell
```

#### 使用规则

1. 单行脚本

对于inline模式，命令只能在写在一行中。一个以上的命令，可以写在同一行，用分号分隔，这属于shell编程的范畴，在这里不多解释。

单行脚本使用的基本格式：

```ruby
config.vm.provision "shell", inline: "echo foo"
```

shell命令的参数还可以写入`do ... end`代码块中，如下：

```ruby
config.vm.provision "shell" do |s|
  s.inline = "echo hello provision."
end
```

2. 内联脚本（不过多赘述）
3. 外部脚本

即把代码写入代码文件，并保存在一个shell脚本里，进行调用：

```RUBY
config.vm.provision "shell", path: "script.sh"
```

> Tips：path可以使用http/https来访问远程脚本，这个在部署时访问一个脚本仓库来做一些通用的操作，比较方便。如：
>
> ```ruby
> config.vm.provision "shell", path: "https://example.com/provisioner.sh"
> ```

### docker如何执行

vagrant提供了使用Docker作为provider（其他的provider有virtualBox、VMware\hyper-V等）的开箱即用支持。这允许你的开发环境由Docker容器而不是虚拟机支持。此外，它为开发dockerfile提供了一个良好的工作流。

#### 方法一 provisioner

Vagrant的docker provisioner能够自动安装Docker、下载Docker容器、随着`vagrant up`命令自动运行容器。

```RUBY
Vagrant.configure("2") do |config|
  config.vm.provision "docker" do |d|
    d.pull_images "consul"
    d.run "consul"
    d.pull_images "rabbitmq"
    d.run "rabbitmq"
  end
end
```



#### 方法二  provisioner+脚本

仅使用Vagrant的docker provisioner安装Docker+使用脚本下载并运行Docker容器

```RUBY
# Install Docker
config.vm.provision "docker"

# Download Docker images, create and start containers
config.vm.provision :shell, :path => "runMyDockers.sh"
```

**runMyDockers.sh:**

```shell
#!/bin/bash

docker rm -f consul 2>/dev/null
docker create --hostname consul --name consul -v /data/consul1:/data --dns 127.0.0.1 --restart always -p 8500:8500 --env CONSUL_OPTIONS=-bootstrap consul:dev
docker start consul
docker rm -f rabbitmq 2>/dev/null
docker create --name rabbitmq --hostname rabbitmq -p 5672:5672 -v /data/rabbitmq:/data --dns 127.0.0.1 --restart always --link consul:consul rabbitmq:dev
docker start rabbitmq
```



### 方法三 provisioner+docker-compose

仅使用Vagrant的docker provisioner安装Docker，使用[Docker Compose](https://docs.docker.com/compose/)下载并运行Docker容器。
[Docker Compose](https://docs.docker.com/compose/)是一个定义和运行Docker复杂程序的工具。使用[Compose](https://docs.docker.com/compose/)，可以在一个文件内定义多容器应用程序，并有控制所有/单个容器启动停止的相应命令。
Compose有一整套命令来对应用的整个生命周期进行管理：

- 启动、停止和重构建服务
- 查看运行服务的状态
- 将运行服务的日志输出整理成数据流。
- 对一个服务运行一次性指令

```ruby
# Install Docker
config.vm.provision "docker"

# Install Docker compose
config.vm.provision "shell", inline: <<-END
  set -x
  sudo curl -L https://github.com/docker/compose/releases/download/1.3.0rc1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
  sudo curl -L https://raw.githubusercontent.com/docker/compose/1.3.0rc1/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
  sudo chmod +x /usr/local/bin/docker-compose
END

# Launch docker-compose to pull Docker images, create and start containers
config.vm.provision "shell", inline: <<-END
  set -x
  cd /vagrant
  docker-compose up -d --allow-insecure-ssl
END
```

**docker-compose.yml:**

```yml
consul:
  image: consul:dev
  hostname: consul
  dns: 127.0.0.1
  restart: always
  ports: 
   - "8500:8500"    
  volumes:
   - /data/consul:/data
  environment:
   - CONSUL_OPTIONS=-bootstrap

rabbitmq:
  image: rabbitmq:dev
  hostname: rabbitmq
  dns: 127.0.0.1
  restart: always
  ports:
   - "5672:5672"
  volumes:
   - /data/rabbitmq:/data
  links:
   - consul:consul
```



参考链接

https://blog.csdn.net/u011781521/article/details/80291765

Vagrant运行Docker的几种方法 http://blog.sina.com.cn/s/blog_72ef7bea0102vucz.html

# 给虚拟机命名

* 使用`config.vm.define`给**虚拟机指定名称**，而不是默认的default。
* `config.vm.hostname`修改虚拟机操作系统在的**主机名**
* `vb.name`修改的是virtualbox(或hyperv)中显示的虚拟机名称，对于virtualbox，可使用`name`属性来修改，对于hyperv，可使用`vmname`属性来修改

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