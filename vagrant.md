# vagrant 入门

## 第一章 vagrant的安装与初识

#### 一、vagrant是什么？

​	通俗来讲vagrant是一个通过命令行，来管理虚拟机的工具，vagrant本身并没有提供虚拟化的功能，vagrant可以用来管理virtualbox，可以通过vagrant提供的命令来创建，启动和关闭virtualbox里面的虚拟机。

#### 二、为什么使用vagrant

​	vagrant提供一个配置文件Vagrantfile,可以通过该配置文件快速创建或者复制一个虚拟机，同时使用命令行来管理虚拟机非常简单。

#### 三、安装vagrant和virtualbox

vistualbox 官网下载：<https://www.virtualbox.org/wiki/Downloads>

vagrant官网下载：<https://www.vagrantup.com/downloads.html>

#### 四、vagrant box

​	vagrant box 是 vagrant定义的vagrant镜像，vagrant通过box镜像可以在vistualbox里面快速创建一个虚拟机，vagrant box 里面包含相应的操作系统。

​	vagrant官方box地址：<https://app.vagrantup.com/boxes/search>

​	可以在创建虚拟机的时候指定box，该box可以是官方仓库里面的，也可以将box提前下载到本地，在创建的时候指定路径



## 第二章 vagrant 基本操作

#### 一、安装box

方法一：

下载.box文件到本地，然后添加到本地镜像中

进入项目目录

```bash
$ cd /e/vagrant/ubuntu
```

创建box.json文件

```bash
$ vim box.json
```

文件内容如下：

```json
{
    "name": "boxName",  # 添加后的box名称
    "versions": [{
        "version": "2.1.0", # 版本号
        "providers": [{
            "name": "virtualbox",
            "url": "file://{path}/Laravel-Homestead.box"  # 下载到本地的box
        }]
    }]
} 
```

完成本地镜像添加

```bash
$ vagrant box add box.json
```



方法二：

```bash
$ vagrant box add {title} {url}
```

<font color=red>注：方法二 因墙的原因速度过慢，建议使用方法一，下载box文件后添加</font>



#### 二、查看本地镜像

```bash
$ vagrant box list
```



#### 三、创建并运行虚拟机

```bash
$ vagrant init {title}
$ vagrant up
```

当执行完 初始化完环境后当前目录会出现配置文件 Vagrantfile

```bash
$ ll
total 4
-rw-r--r-- 1 Administrator 197121 3094 May 16 10:32 Vagrantfile
```



#### 四、登录vagrant虚拟机

```bash
$ vagrant ssh
```



#### 五、Vagrantfile配置文件详解

设置box名字

```bash
config.vm.box = "ubuntu"
```

设置hostname

```bash
config.vm.hostname = "ubuntu16.4"
```

​	指定vm的hostname，当有多台虚拟服务器时，依靠hostname来做识别。 通过 vagrant up hostname 来启动指定虚拟机。

设置虚拟机网络

Vagrant有三种网络连接方式：
NAT : 缺省创建，用于让vm可以通过host转发访问局域网甚至互联网;
host-only : 只有主机可以访问vm，其他机器无法访问它;
bridge : 此模式下vm就像局域网中的一台独立的机器，可以被其他机器访问。

```bash
#配置当前vm的host-only网络的IP地址为192.168.33.10
config.vm.network "private_network", ip: "192.168.33.10"
```

host-only 模式的IP可以不指定，而是采用dhcp自动生成的方式，如 :

```bash
config.vm.network "private_network", type: "dhcp"
```

创建一个bridge桥接网络，指定IP

```bash
config.vm.network "public_network", ip: "192.168.0.2"
```

设置文件夹同步

```bash
config.vm.synced_folder "../shared", "/app/"
```

​	主机与vm同步文件，vagrant默认会把工作目录映射到vm的/vagrant目录，第一个文件夹为主机的目录，第二个文件夹为vm中的目录。

端口转发设置

```bash
config.vm.network "forwarded_port", guest: 80, host: 8080
```

将主机的8080端口请求，转发到vm的80端口，这样访问[http://host:8080](http://host:8080/) 就相当于访问[http://vm:80了](http://vm:80%E4%BA%86/)
guest和host是必须的，还有几个可选属性：
guest_ip：字符串，vm指定绑定的Ip，缺省为0.0.0.0
host_ip：字符串，host指定绑定的Ip，缺省为0.0.0.0
protocol：字符串，可选TCP或UDP，缺省为TCP



vm provider 通用和个性化设置

通用配置对于不同provider是通用的，常用的通用配置如下：

```bash
config.vm.provider "virtualbox" do |vb|
  #指定vm-name，也就是virtualbox管理控制台中的虚机名称
    vb.name = "ubuntu"
    # vagrant up启动时，是否自动打开virtual box的窗口，缺省为false
    vb.gui = true
    #指定vm内存，单位为MB
    vb.memory = "1024"
    #设置CPU个数
    vb.cpus = 2
end
```





## vagrant 命令详解

| 命令                                     | 作用                                                    |
| ---------------------------------------- | ------------------------------------------------------- |
| vagrant box add                          | 添加box的操作                                           |
| vagrant init                             | 初始化box的操作，会生成vagrant的配置文件Vagrantfile     |
| vagrant up                               | 启动本地环境                                            |
| vagrant ssh                              | 通过 ssh 登录本地环境所在虚拟机                         |
| vagrant halt                             | 关闭本地环境                                            |
| vagrant suspend                          | 暂停本地环境                                            |
| vagrant resume                           | 恢复本地环境                                            |
| vagrant reload                           | 修改了 Vagrantfile 后，使之生效（相当于先 halt，再 up） |
| vagrant destroy                          | 彻底移除本地环境                                        |
| vagrant box list                         | 显示当前已经添加的box列表                               |
| vagrant box remove                       | 删除相应的box                                           |
| vagrant package                          | 打包命令，可以把当前的运行的虚拟机环境进行打包          |
| vagrant plugin                           | 用于安装卸载插件                                        |
| vagrant status                           | 获取当前虚拟机的状态                                    |
| vagrant global-status                    | 显示当前用户Vagrant的所有环境状态                       |
| vagrant snapshot save {snapshot name}    | 新建快照                                                |
| vagrant snapshot list                    | 查看快照                                                |
| vagrant snapshot restore {snapshot name} | 回复快照                                                |
| vagrant snapshot delete {snapshot name}  | 删除快照                                                |
