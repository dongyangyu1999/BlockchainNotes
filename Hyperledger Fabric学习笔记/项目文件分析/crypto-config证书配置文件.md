# Cryptogen模块

cryptogen模块主要用来生成组织结构和账号相关的文件，任何Fabric系统的开发通常都是从cryptogen模块开始的。在Fabric项目中，当系统设计完成之后第一项工作就是根据系统的设计来编写cryptogen的配置文件，然后通过这些配置文件生成相关的证书文件。
**Cryptogen模块所使用的配置文件是整个Fabric项目的基石。**

## Cryptogen模块命令

cryptogen模块是通过命令行的方式运行的，通过执行命令`cryptogen --help`可以显示cryptogen模块的命令行选项，结果如下所示：

```shell
$ cryptogen --help
usage: cryptogen [<flags>] <command> [<args> ...]
Utility for generating Hyperledger Fabric key material
Flags:
  --help  Show context-sensitive help (also try --help-long and --help-man).
Commands:
   # 显示帮助信息
  `help [<command>...]
   # 根据配置文件生成证书信息。
  `generate [<flags>]	
   # 显示系统默认的cryptogen模块配置文件信息
  `showtemplate
   # 显示当前模块版本号
  `version`
   # 扩展现有网络
  `extend [<flags>]
```

## Cryptogen模块配置文件

Cryptogen模块的配置文件用来描述需要生成的证书文件的特性。
比如：有多少个组织有多少个节点，需要多少个账号等。这里我们通过一个cryptogen模块配置文件的具体例子来初步了解配置文件的结构，该例子是Fabric源代码中自带的示例 - `crypto-config.yaml`，该配置文件名可自行定义。

模版如下(`crypto-config.yaml`)：

```YAML
OrdererOrgs:					# 排序节点的组织定义
  - Name: Orderer				# orderer节点的名称
 	Domain: example.com			# orderer节点的根域名 
 	Specs:
	    - Hostname: orderer		# orderer节点的主机名
PeerOrgs:						# peer节点的组织定义
  - Name: Org1					# 组织1的名称
	Domain: org1.example.com	# 组织1的根域名
 	EnableNodeOUs: true			# 是否支持node.js
 	Template:					
	    Count: 2				# 组织1中的节点(peer)数目
	Users:
 	    Count: 1				# 组织1中的用户数目
  - Name: Org2
    Domain: org2.example.com
    EnableNodeOUs: true
    Template:
        Count: 2
    Users:
        Count: 1
```

**说明**

上述配置文件定义了一个排序组织，两个peer节点组织

* 排序组织名为`Orderer`，域名为`example.com`，主机名为`orderer`

* 共有两个节点组织，分别为Org1和Org2，域名分别为`org[1,2].example.com`
  * 组织 org1 包含了2个节点和1个用户，组织 org2 包含2个点和1个用户

**测试**

接下来我们随便编写一个证书配置文件（命名为`crypto-config.yaml`）如下：

```YAML
OrdererOrgs:					# 排序节点的组织定义
  - Name: Orderer				# orderer节点的名称
    Domain: test.com			# orderer节点的根域名 
    Specs:
    - Hostname: orderer		# orderer节点的主机名
PeerOrgs:						# peer节点的组织定义
  - Name: java					# 组织1的名称设为java
    Domain: java.test.com	# 组织1的根域名
    EnableNodeOUs: true			# 是否支持node.js
    Template:					
      Count: 3				# 组织1中的节点(peer)数目为3
    Users:
      Count: 2				# 组织1中的用户数目为2
  - Name: go
    Domain: go.test.com
    EnableNodeOUs: true
    Template:
      Count: 3
    Users:
      Count: 2
```

### 测试命令

```BASH
# 根据默认模板在对应目录下生成证书
$ cryptogen generate
# 根据指定的模板在指定目录下生成证书
$ cryptogen generate --config=./crypto-config.yaml --output ./crypto-config
#	--config: 指定配置文件
#	--output: 指定证书文件的存储位置, 可以不指定。会在对应路径生成目录，默认名字为：crypto-config
```

### 目录内容

`crypto-config`目录下有oederer和peer的文件夹

```bash
root@localhost test $ tree -L 2
.
├── crypto-config
│   ├── ordererOrganizations # orderer节点相关的证书文件
│   └── peerOrganizations    # 组织相关的证书文件(组织的节点数, 用户数等证书文件)
└── crypto-config.yaml       # 证书配置文件
```

`ordererOrganizations`目录下的内容：

```BASH
root@localhost test $ tree ./crypto-config/ordererOrganizations/ -L 4
./crypto-config/ordererOrganizations/
└── test.com  # 根域名为test.com的orderer节点的相关证书文件
    ├── ca 	  # CA服务器的签名文件
    │   ├── b0aa3d3428d28cf708e7884c2f737c42a8a4ae43040eef5c43643184b110911b_sk
    │   └── ca.test.com-cert.pem
    ├── msp   # 存放代表该组织的身份信息
    │   ├── admincerts  # orderer管理员的证书
    │   │   └── Admin@test.com-cert.pem
    │   ├── cacerts     # orderer根域名服务器的签名证书
    │   │   └── ca.test.com-cert.pem
    │   └── tlscacerts  # tls连接用的身份证书
    │       └── tlsca.test.com-cert.pem
    ├── orderers # orderer节点需要的相关的证书文件
    │   └── orderer.test.com
    │       ├── msp     # orderer节点相关证书
    │       └── tls     # orderer节点和其他节点连接用的身份证书
    ├── tlsca
    │   ├── 34d5992d4c430a5b6beeda13202de1b90c30a46061a2dacce3442d6a25ebeb0a_sk
    │   └── tlsca.test.com-cert.pem
    └── users    # orderer节点 用户 相关的证书
        └── Admin@test.com
            ├── msp
            └── tls

15 directories, 7 files
```

> 在实际开发中orderer节点这些证书其实不需要直接使用, 只是在orderer节点启动时指明项目的位置即可。

`peerOrganizations`目录下的内容：

> 由于每个组织的目录结构都是一样的， 所以我们只对其中一个组织(go)的目录进行详细介绍。

```BASH
root@localhost peerOrganizations # tree
.
├── go.test.com
│   ├── ca    # 根节点签名证书
│   │   ├── c38685c0919ed148d17a6613c8573dce114320d7330bf6ac49d2de842065d6bb_sk
│   │   └── ca.go.test.com-cert.pem
│   ├── msp
│   │   ├── admincerts  # 组织管理员的证书
│   │   ├── cacerts     # 组织的根证书
│   │   │   └── ca.go.test.com-cert.pem
│   │   ├── config.yaml
│   │   └── tlscacerts  # TLS连接身份证书
│   │       └── tlsca.go.test.com-cert.pem
│   ├── peers  # 存放该组织下所有peer节点的证书
│   │   ├── peer0.go.test.com
│   │   │   ├── msp
│   │   │   │   ├── admincerts # 组织的管理证书, 创建通道必须要有该证书
│   │   │   │   ├── cacerts    # 组织根证书
│   │   │   │   │   └── ca.go.test.com-cert.pem
│   │   │   │   ├── config.yaml 
│   │   │   │   ├── keystore   # 本节点的身份私钥，用来签名
│   │   │   │   │   └── 42f19ead596381652fd9d0dfb7fd6b78660c4b9a75d5c329d5fe0b658c25622d_sk
│   │   │   │   ├── signcerts  # 验证本节点签名的证书，被组织根证书签名
│   │   │   │   │   └── peer0.go.test.com-cert.pem
│   │   │   │   └── tlscacerts # tls连接的身份证书
│   │   │   │       └── tlsca.go.test.com-cert.pem
│   │   │   └── tls   # 存放tls相关的证书和私钥
│   │   │       ├── ca.crt     # 组织的根证书
│   │   │       ├── server.crt # 验证本节点签名的证书
│   │   │       └── server.key # 当前节点的私钥
│   │   ├── peer1.go.test.com
│   │   │       ...
│   │   └── peer2.go.test.com
│   │       │   ...
│   ├── tlsca
│   │   ├── a2234b85a5bf1f3d3814041fe4bbe28e755694a4683690872510a35f31171001_sk
│   │   └── tlsca.go.test.com-cert.pem
│   └── users  # 存放属于该组织的用户实体
│       ├── Admin@go.test.com # 管理员用户的信息，包括其msp证书和tls证书
│       │   ├── msp
│       │   │   ├── admincerts   # 组织的根证书, 作为管理身份的验证
│       │   │   ├── cacerts      # 用户所属组织的根证书
│       │   │   │   └── ca.go.test.com-cert.pem
│       │   │   ├── config.yaml
│       │   │   ├── keystore     # 用户私钥
│       │   │   │   └── 3311fb401ff0c9b5af67b84e5d66798112e8c4dccd63eeadaa26c7b612b5a68d_sk
│       │   │   ├── signcerts    # 用户的签名证书
│       │   │   │   └── Admin@go.test.com-cert.pem
│       │   │   └── tlscacerts   # tls连接通信证书, sdk客户端使用
│       │   │       └── tlsca.go.test.com-cert.pem
│       │   └── tls
│       │       ├── ca.crt       # 组织的根证书
│       │       ├── client.crt   # 客户端身份的证书
│       │       └── client.key   # 客户端的私钥
│       ├── User1@go.test.com # 第一个用户的信息，结构和admin相同，包括msp证书和tls证书
│       │   ├── msp
│       │   │   ├── admincerts
│       │   │   ├── cacerts
│       │   │   ├── config.yaml
│       │   │   ├── keystore
│       │   │   ├── signcerts
│       │   │   └── tlscacerts
│       │   └── tls
│       │       ├── ca.crt
│       │       ├── client.crt
│       │       └── client.key
│       └── User2@go.test.com
│           ...
└── java.test.com
    ├── ca ...
```



# 测试文件代码

```YAML
OrdererOrgs:
  - Name: Orderer
    Domain: dy
    Specs:
      - Hostname: orderer
PeerOrgs:
  - Name: Org1
    Domain: org1.dy
    # EnableNodeOUs: false
    Template:
      Count: 1
    Users:
      Count: 1
  - Name: Org2
    Domain: org2.dy
    # EnableNodeOUs: false
    Template:
      Count: 1
    Users:
      Count: 1
  - Name: Org3
    Domain: org3.dy
    # EnableNodeOUs: false
    Template:
      Count: 1
    Users:
      Count: 1
  - Name: Org4
    Domain: org4.dy
    # EnableNodeOUs: false
    Template:
      Count: 1
    Users:
      Count: 1
```

# 参考

[Fabric核心模块之Cryptogen解析](https://sunlidong.blog.csdn.net/article/details/84203675)