`configtx.yaml`配置文件模板可在GitHub仓库中找到（v1.4[链接](https://github.com/hyperledger/fabric/blob/release-1.4/sampleconfig/configtx.yaml)），

# 功能

组织机构配置文件`configtx.yaml`主要用来配置fabric的组织结构，通道及锚节点的配置。它主要完成以下几个功能

- 生成启动 Orderer 需要的创世区块**orderer.block(genesis.block)**
- 创建应用通道所需的**配置交易文件**
- 生成组织**锚节点更新配置交易文件**

# 案例代码

> 代码来源于官方教程项目Build your first network[链接](https://github.com/hyperledger/fabric-samples/blob/release-1.4/first-network/configtx.yaml)

```YAML
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:

    # SampleOrg defines an MSP using the sampleconfig.  It should never be used
    # in production but may be used as a template for other definitions
    - &OrdererOrg
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: OrdererOrg

        # ID to load the MSP definition as
        ID: OrdererMSP

        # MSPDir is the filesystem path which contains the MSP configuration
        MSPDir: crypto-config/ordererOrganizations/example.com/msp

        # Policies defines the set of policies at this level of the config tree
        # For organization policies, their canonical path is usually
        #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('OrdererMSP.member')"
            Writers:
                Type: Signature
                Rule: "OR('OrdererMSP.member')"
            Admins:
                Type: Signature
                Rule: "OR('OrdererMSP.admin')"

    - &Org1
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: Org1MSP

        # ID to load the MSP definition as
        ID: Org1MSP

        MSPDir: crypto-config/peerOrganizations/org1.example.com/msp

        # Policies defines the set of policies at this level of the config tree
        # For organization policies, their canonical path is usually
        #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('Org1MSP.admin', 'Org1MSP.peer', 'Org1MSP.client')"
            Writers:
                Type: Signature
                Rule: "OR('Org1MSP.admin', 'Org1MSP.client')"
            Admins:
                Type: Signature
                Rule: "OR('Org1MSP.admin')"

        # leave this flag set to true.
        AnchorPeers:
            # AnchorPeers defines the location of peers which can be used
            # for cross org gossip communication.  Note, this value is only
            # encoded in the genesis block in the Application section context
            - Host: peer0.org1.example.com
              Port: 7051

    - &Org2
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: Org2MSP

        # ID to load the MSP definition as
        ID: Org2MSP

        MSPDir: crypto-config/peerOrganizations/org2.example.com/msp

        # Policies defines the set of policies at this level of the config tree
        # For organization policies, their canonical path is usually
        #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('Org2MSP.admin', 'Org2MSP.peer', 'Org2MSP.client')"
            Writers:
                Type: Signature
                Rule: "OR('Org2MSP.admin', 'Org2MSP.client')"
            Admins:
                Type: Signature
                Rule: "OR('Org2MSP.admin')"

        AnchorPeers:
            # AnchorPeers defines the location of peers which can be used
            # for cross org gossip communication.  Note, this value is only
            # encoded in the genesis block in the Application section context
            - Host: peer0.org2.example.com
              Port: 9051

################################################################################
#
#   SECTION: Capabilities
#
#   - This section defines the capabilities of fabric network. This is a new
#   concept as of v1.1.0 and should not be utilized in mixed networks with
#   v1.0.x peers and orderers.  Capabilities define features which must be
#   present in a fabric binary for that binary to safely participate in the
#   fabric network.  For instance, if a new MSP type is added, newer binaries
#   might recognize and validate the signatures from this type, while older
#   binaries without this support would be unable to validate those
#   transactions.  This could lead to different versions of the fabric binaries
#   having different world states.  Instead, defining a capability for a channel
#   informs those binaries without this capability that they must cease
#   processing transactions until they have been upgraded.  For v1.0.x if any
#   capabilities are defined (including a map with all capabilities turned off)
#   then the v1.0.x peer will deliberately crash.
#
################################################################################
Capabilities:
    # Channel capabilities apply to both the orderers and the peers and must be
    # supported by both.
    # Set the value of the capability to true to require it.
    Channel: &ChannelCapabilities
        # V1.4.3 for Channel is a catchall flag for behavior which has been
        # determined to be desired for all orderers and peers running at the v1.4.3
        # level, but which would be incompatible with orderers and peers from
        # prior releases.
        # Prior to enabling V1.4.3 channel capabilities, ensure that all
        # orderers and peers on a channel are at v1.4.3 or later.
        V1_4_3: true
        # V1.3 for Channel enables the new non-backwards compatible
        # features and fixes of fabric v1.3
        V1_3: false
        # V1.1 for Channel enables the new non-backwards compatible
        # features and fixes of fabric v1.1
        V1_1: false

    # Orderer capabilities apply only to the orderers, and may be safely
    # used with prior release peers.
    # Set the value of the capability to true to require it.
    Orderer: &OrdererCapabilities
        # V1.4.2 for Orderer is a catchall flag for behavior which has been
        # determined to be desired for all orderers running at the v1.4.2
        # level, but which would be incompatible with orderers from prior releases.
        # Prior to enabling V1.4.2 orderer capabilities, ensure that all
        # orderers on a channel are at v1.4.2 or later.
        V1_4_2: true
        # V1.1 for Orderer enables the new non-backwards compatible
        # features and fixes of fabric v1.1
        V1_1: false

    # Application capabilities apply only to the peer network, and may be safely
    # used with prior release orderers.
    # Set the value of the capability to true to require it.
    Application: &ApplicationCapabilities
        # V1.4.2 for Application enables the new non-backwards compatible
        # features and fixes of fabric v1.4.2.
        V1_4_2: true
        # V1.3 for Application enables the new non-backwards compatible
        # features and fixes of fabric v1.3.
        V1_3: false
        # V1.2 for Application enables the new non-backwards compatible
        # features and fixes of fabric v1.2 (note, this need not be set if
        # later version capabilities are set)
        V1_2: false
        # V1.1 for Application enables the new non-backwards compatible
        # features and fixes of fabric v1.1 (note, this need not be set if
        # later version capabilities are set).
        V1_1: false

################################################################################
#
#   SECTION: Application
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for application related parameters
#
################################################################################
Application: &ApplicationDefaults

    # Organizations is the list of orgs which are defined as participants on
    # the application side of the network
    Organizations:

    # Policies defines the set of policies at this level of the config tree
    # For Application policies, their canonical path is
    #   /Channel/Application/<PolicyName>
    Policies:
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"

    Capabilities:
        <<: *ApplicationCapabilities
################################################################################
#
#   SECTION: Orderer
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for orderer related parameters
#
################################################################################
Orderer: &OrdererDefaults

    # Orderer Type: The orderer implementation to start
    # Available types are "solo","kafka"  and "etcdraft"
    OrdererType: solo

    Addresses:
        - orderer.example.com:7050

    # Batch Timeout: The amount of time to wait before creating a batch
    BatchTimeout: 2s

    # Batch Size: Controls the number of messages batched into a block
    BatchSize:

        # Max Message Count: The maximum number of messages to permit in a batch
        MaxMessageCount: 10

        # Absolute Max Bytes: The absolute maximum number of bytes allowed for
        # the serialized messages in a batch.
        AbsoluteMaxBytes: 99 MB

        # Preferred Max Bytes: The preferred maximum number of bytes allowed for
        # the serialized messages in a batch. A message larger than the preferred
        # max bytes will result in a batch larger than preferred max bytes.
        PreferredMaxBytes: 512 KB

    Kafka:
        # Brokers: A list of Kafka brokers to which the orderer connects
        # NOTE: Use IP:port notation
        Brokers:
            - 127.0.0.1:9092

    # EtcdRaft defines configuration which must be set when the "etcdraft"
    # orderertype is chosen.
    EtcdRaft:
        # The set of Raft replicas for this network. For the etcd/raft-based
        # implementation, we expect every replica to also be an OSN. Therefore,
        # a subset of the host:port items enumerated in this list should be
        # replicated under the Orderer.Addresses key above.
        Consenters:
            - Host: orderer.example.com
              Port: 7050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/server.crt
            - Host: orderer2.example.com
              Port: 7050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/server.crt
            - Host: orderer3.example.com
              Port: 7050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/server.crt
            - Host: orderer4.example.com
              Port: 7050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/server.crt
            - Host: orderer5.example.com
              Port: 7050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer5.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer5.example.com/tls/server.crt

    # Organizations is the list of orgs which are defined as participants on
    # the orderer side of the network
    Organizations:

    # Policies defines the set of policies at this level of the config tree
    # For Orderer policies, their canonical path is
    #   /Channel/Orderer/<PolicyName>
    Policies:
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"
        # BlockValidation specifies what signatures must be included in the block
        # from the orderer for the peer to validate it.
        BlockValidation:
            Type: ImplicitMeta
            Rule: "ANY Writers"

################################################################################
#
#   CHANNEL
#
#   This section defines the values to encode into a config transaction or
#   genesis block for channel related parameters.
#
################################################################################
Channel: &ChannelDefaults
    # Policies defines the set of policies at this level of the config tree
    # For Channel policies, their canonical path is
    #   /Channel/<PolicyName>
    Policies:
        # Who may invoke the 'Deliver' API
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        # Who may invoke the 'Broadcast' API
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        # By default, who may modify elements at this config level
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"

    # Capabilities describes the channel level capabilities, see the
    # dedicated Capabilities section elsewhere in this file for a full
    # description
    Capabilities:
        <<: *ChannelCapabilities

################################################################################
#
#   Profile
#
#   - Different configuration profiles may be encoded here to be specified
#   as parameters to the configtxgen tool
#
################################################################################
Profiles:

    TwoOrgsOrdererGenesis:
        <<: *ChannelDefaults
        Orderer:
            <<: *OrdererDefaults
            Organizations:
                - *OrdererOrg
            Capabilities:
                <<: *OrdererCapabilities
        Consortiums:
            SampleConsortium:
                Organizations:
                    - *Org1
                    - *Org2
    TwoOrgsChannel:
        Consortium: SampleConsortium
        <<: *ChannelDefaults
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *Org1
                - *Org2
            Capabilities:
                <<: *ApplicationCapabilities

    SampleDevModeKafka:
        <<: *ChannelDefaults
        Capabilities:
            <<: *ChannelCapabilities
        Orderer:
            <<: *OrdererDefaults
            OrdererType: kafka
            Kafka:
                Brokers:
                - kafka.example.com:9092

            Organizations:
            - *OrdererOrg
            Capabilities:
                <<: *OrdererCapabilities
        Application:
            <<: *ApplicationDefaults
            Organizations:
            - <<: *OrdererOrg
        Consortiums:
            SampleConsortium:
                Organizations:
                - *Org1
                - *Org2

    SampleMultiNodeEtcdRaft:
        <<: *ChannelDefaults
        Capabilities:
            <<: *ChannelCapabilities
        Orderer:
            <<: *OrdererDefaults
            OrdererType: etcdraft
            EtcdRaft:
                Consenters:
                - Host: orderer.example.com
                  Port: 7050
                  ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/server.crt
                  ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/server.crt
                - Host: orderer2.example.com
                  Port: 7050
                  ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/server.crt
                  ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/server.crt
                - Host: orderer3.example.com
                  Port: 7050
                  ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/server.crt
                  ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/server.crt
                - Host: orderer4.example.com
                  Port: 7050
                  ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/server.crt
                  ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/server.crt
                - Host: orderer5.example.com
                  Port: 7050
                  ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer5.example.com/tls/server.crt
                  ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer5.example.com/tls/server.crt
            Addresses:
                - orderer.example.com:7050
                - orderer2.example.com:7050
                - orderer3.example.com:7050
                - orderer4.example.com:7050
                - orderer5.example.com:7050

            Organizations:
            - *OrdererOrg
            Capabilities:
                <<: *OrdererCapabilities
        Application:
            <<: *ApplicationDefaults
            Organizations:
            - <<: *OrdererOrg
        Consortiums:
            SampleConsortium:
                Organizations:
                - *Org1
                - *Org2
```

# 配置文件内容

* Organizations / 组织机构配置
* Capabilities / 通道能力配置
* Application / 应用通道配置
* Orderer / 排序节点配置
* Channel / 通道配置
* Profiles / 配置入口
  * Profiles配置段用来定义用于configtxgen工具的配置入口。包含联盟（consortium）的配置入口可以用来生成排序节点的创世区块。如果在排序节点的创世区块中正确定义了consortium的成员，那么可以仅使用组织成员名称和联盟的名称来生成通道创建请求。

## Organizations / 组织机构配置

Organizations配置段用来定义组织机构实体，以便在后续配置中引用。

```YAML
Organizations:
    - &OrdererOrg
        Name: OrdererOrg	## 组织名称
        ID: OrdererMSP	## 组织ID，ID是引用组织的关键
        MSPDir: crypto-config/ordererOrganizations/example.com/msp	## 组织的MSP证书路径
        ## 定义本层级的组织策略，其权威路径为 /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('OrdererMSP.member')"
            Writers:
                Type: Signature
                Rule: "OR('OrdererMSP.member')"
            Admins:
                Type: Signature
                Rule: "OR('OrdererMSP.admin')"

    - &Org1
        Name: Org1MSP
        ID: Org1MSP
        MSPDir: crypto-config/peerOrganizations/org1.example.com/msp
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('Org1MSP.admin', 'Org1MSP.peer', 'Org1MSP.client')"
            Writers:
                Type: Signature
                Rule: "OR('Org1MSP.admin', 'Org1MSP.client')"
            Admins:
                Type: Signature
                Rule: "OR('Org1MSP.admin')"
        AnchorPeers:		## 定义组织的锚节点
            - Host: peer0.org1.example.com		## 锚节点的host地址
              Port: 7051	## 锚节点开放的端口号

    - &Org2
        Name: Org2MSP
        ID: Org2MSP
        MSPDir: crypto-config/peerOrganizations/org2.example.com/msp
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('Org2MSP.admin', 'Org2MSP.peer', 'Org2MSP.client')"
            Writers:
                Type: Signature
                Rule: "OR('Org2MSP.admin', 'Org2MSP.client')"
            Admins:
                Type: Signature
                Rule: "OR('Org2MSP.admin')"
        AnchorPeers:	
            - Host: peer0.org2.example.com
              Port: 9051
```



## Capabilities / 通道能力配置

Capabilities 段用来定义fabric网络的能力。这是版本v1.1.0引入的一个新的配置段，当与版本v1.0.x的orderer节点与peer节点混合组网时不可使用。

> Capabilities段定义了fabric程序要加入网络所必须支持的特性。例如，如果添加了一个新的MSP类型，那么更新的程序可能会根据该类型识别并验证签名，但是老版本的程序就没有办法验证这些交易。这可能导致不同版本的fabric程序中维护的世界状态不一致。
>
> 因此，通过定义通道的能力，就明确了不满足该能力要求的fabric程序，将无法处理交易，除非升级到新的版本。对于v1.0.x的程序而言，如果在Capabilities段定义了任何能力，即使声明不需要支持这些能力，都会导致其有意崩溃。

```YAML
Capabilities:
	# Channel配置同时应用于orderer节点与peer节点，并且必须被两种节点同时支持
    # 将该配置项设置为ture表明要求节点具备该能力,false则不要求该节点具备该能力
    Channel: &ChannelCapabilities
        V1_4_3: true    
        V1_3: false 
        V1_1: false
    # Orderer功能仅适用于orderers，可以安全地操作，而无需担心升级peers
    # 将该配置项设置为ture表明要求节点具备该能力,false则不要求该节点具备该能力
    Orderer: &OrdererCapabilities
        V1_4_2: true 
        V1_1: false
    # 应用程序功能仅适用于Peer网络，可以安全地操作，而无需担心升级或更新orderers
    # 将该配置项设置为ture表明要求节点具备该能力,false则不要求该节点具备该能力
    Application: &ApplicationCapabilities
        V1_4_2: true
        V1_3: false
        V1_2: false  
        V1_1: false
```





## Application / 应用通道配置

Application配置段用来定义要写入创世区块或配置交易的应用参数。

```YAML
Application: &ApplicationDefaults  ##  自定义被引用的地址
    Organizations:	## Organizations配置列出参与到网络中的组织机构清单
    Policies:		## 定义本层级的应用控制策略，其权威路径为 /Channel/Application/<PolicyName>
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"
    # Capabilities配置描述应用层级的能力需求，这里直接引用
    # 前面Capabilities配置段中的ApplicationCapabilities配置项
    Capabilities:
        <<: *ApplicationCapabilities

```



## Orderer / 排序节点配置

Orderer配置段用来定义要编码写入创世区块或通道交易的排序节点参数

```YAML
Orderer: &OrdererDefaults
	# 排序节点类型用来指定要启用的排序节点实现，不同的实现对应不同的共识算法。
    # 目前可用的类型为：solo，kafka，EtcdRaft
    OrdererType: solo
    Addresses:		## 服务地址,这个地方很重要，一定要配正确
        - orderer.example.com:7050
    BatchTimeout: 2s	## 区块打包的最大超时时间 (到了该时间就打包区块)
    BatchSize:	## 区块打包的最大包含交易数（orderer端切分区块的参数）
        MaxMessageCount: 10	 			## 一个区块里最大的交易数
        AbsoluteMaxBytes: 99 MB			## 一个区块的最大字节数，任何时候都不能超过
        PreferredMaxBytes: 512 KB		## 一个区块的建议字节数，如果一个交易消息的大小超过了这个值, 就会被放入另外一个更大的区块中
    MaxChannels: 0    ## 【可选项】表示Orderer允许的最大通道数， 默认0表示没有最大通道数
    Kafka:
        Brokers:	## kafka模式的时候kafka节点的地址，通常至少配2个
            - 127.0.0.1:9092
    EtcdRaft:	## 定义了EtcdRaft排序类型被选择时的配置
        Consenters:
            - Host: orderer.example.com
              Port: 7050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/server.crt
            - Host: orderer2.example.com
              Port: 7050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/server.crt
            - Host: orderer3.example.com
              Port: 7050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/server.crt
            - Host: orderer4.example.com
              Port: 7050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/server.crt
            - Host: orderer5.example.com
              Port: 7050
              ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer5.example.com/tls/server.crt
              ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer5.example.com/tls/server.crt
    Organizations:	 ## 参与维护Orderer的组织，默认为空
    # 定义本层级的排序节点策略，其权威路径为 /Channel/Orderer/<PolicyName>
    Policies:
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"
        BlockValidation:
            Type: ImplicitMeta
            Rule: "ANY Writers"
    # Capabilities配置描述排序节点层级的能力需求，这里直接引用
    # 前面Capabilities配置段中的OrdererCapabilities配置项
    Capabilities:
        <<: *OrdererCapabilities

```



## Channel / 通道配置

Channel配置段用来定义要写入创世区块或配置交易的通道参数。

```YAML
Channel: &ChannelDefaults
	# 定义本层级的通道访问策略，其权威路径为 /Channel/<PolicyName>
    Policies:
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        # Writes策略定义了调用Broadcast API提交交易的许可规则
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        # Admin策略定义了修改本层级配置的许可规则
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"
    # Capabilities配置描通道层级的能力需求，这里直接引用
    # 前面Capabilities配置段中的ChannelCapabilities配置项
    Capabilities:
        <<: *ChannelCapabilities
```





## Profiles / 配置入口

Profiles配置段用来定义用于configtxgen工具的配置入口。包含联盟（consortium）的配置入口可以用来生成排序节点的创世区块。

```yaml
Profiles:
	# TwoOrgsOrdererGenesis用来生成orderer启动时所需的block，也就是用于生成创世区块，名字可以任意
		# 需要包含Orderer和Consortiums两部分
    TwoOrgsOrdererGenesis:	
        <<: *ChannelDefaults	## 通道为默认配置，这里直接引用上面channel配置段中的ChannelDefaults
        Orderer:
            <<: *OrdererDefaults	## Orderer为默认配置，这里直接引用上面orderer配置段中的OrdererDefaults
            Organizations:	## 这里直接引用上面Organizations配置段中的OrdererOrg
                - *OrdererOrg	
            Capabilities:	## 这里直接引用上面Capabilities配置段中的OrdererCapabilities
                <<: *OrdererCapabilities
        # 联盟为默认的 SampleConsortium 联盟，添加了两个组织，表示orderer所服务的联盟列表
        Consortiums:	
            SampleConsortium:		##  创建更多应用通道时的联盟引用 TwoOrgsChannel 所示
                Organizations:
                    - *Org1
                    - *Org2
    # TwoOrgsChannel用来生成channel配置信息，名字可以任意
		# 需要包含Consortium和Applicatioon两部分。 
    TwoOrgsChannel:
        Consortium: 
        	SampleConsortium		## 通道所关联的联盟名称
        <<: *ChannelDefaults		## 通道为默认配置，这里直接引用上面channel配置段中的ChannelDefaults
        Application:
            <<: *ApplicationDefaults	## 这里直接引用上面Application配置段中的ApplicationDefaults
            Organizations:
                - *Org1
                - *Org2
            Capabilities:
                <<: *ApplicationCapabilities	## 这里直接引用上面Capabilities配置段中的ApplicationCapabilities
                
    # SampleInsecureKafka定义了一个使用Kfaka排序节点的配置
    SampleDevModeKafka:
        <<: *ChannelDefaults
        Capabilities:
            <<: *ChannelCapabilities
        Orderer:
            <<: *OrdererDefaults
            OrdererType: kafka
            Kafka:
                Brokers:
                - kafka.example.com:9092

            Organizations:
            - *OrdererOrg
            Capabilities:
                <<: *OrdererCapabilities
        Application:
            <<: *ApplicationDefaults
            Organizations:
            - <<: *OrdererOrg
        Consortiums:
            SampleConsortium:
                Organizations:
                - *Org1
                - *Org2
	# SampleInsecureKafka定义了一个使用EtcdRaft排序节点的配置
    SampleMultiNodeEtcdRaft:
        <<: *ChannelDefaults
        Capabilities:
            <<: *ChannelCapabilities
        Orderer:
            <<: *OrdererDefaults
            OrdererType: etcdraft
            EtcdRaft:
                Consenters:
                - Host: orderer.example.com
                  Port: 7050
                  ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/server.crt
                  ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/server.crt
                - Host: orderer2.example.com
                  Port: 7050
                  ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/server.crt
                  ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/server.crt
                - Host: orderer3.example.com
                  Port: 7050
                  ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/server.crt
                  ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/server.crt
                - Host: orderer4.example.com
                  Port: 7050
                  ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/server.crt
                  ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/server.crt
                - Host: orderer5.example.com
                  Port: 7050
                  ClientTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer5.example.com/tls/server.crt
                  ServerTLSCert: crypto-config/ordererOrganizations/example.com/orderers/orderer5.example.com/tls/server.crt
            Addresses:
                - orderer.example.com:7050
                - orderer2.example.com:7050
                - orderer3.example.com:7050
                - orderer4.example.com:7050
                - orderer5.example.com:7050

            Organizations:
            - *OrdererOrg
            Capabilities:
                <<: *OrdererCapabilities
        Application:
            <<: *ApplicationDefaults
            Organizations:
            - <<: *OrdererOrg
        Consortiums:
            SampleConsortium:
                Organizations:
                - *Org1
                - *Org2

```

