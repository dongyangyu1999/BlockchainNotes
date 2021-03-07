`configtx.yaml`配置文件模板可在GitHub仓库中找到（v1.4[链接](https://github.com/hyperledger/fabric/blob/release-1.4/sampleconfig/configtx.yaml)），

官方提供的examples/e2e_cli/configtx.yaml这个文件里面配置了由2个Org参与的Orderer共识配置TwoOrgsOrdererGenesis，以及由2个Org参与的Channel配置：TwoOrgsChannel。Orderer可以设置共识的算法是Solo还是Kafka，以及共识时区块大小，超时时间等，我们使用默认值即可，不用更改。而Peer节点的配置包含了MSP的配置，锚节点的配置。如果我们有更多的Org，或者有更多的Channel，那么就可以根据模板进行对应的修改。

# 功能

组织机构配置文件`configtx.yaml`主要用来配置fabric的组织结构，通道及锚节点的配置。它主要完成以下几个功能

- 生成启动 **Orderer 需要的创世区块orderer**.block(genesis.block)
- 创建**应用通道**所需的配置交易文件
- 生成**组织锚节点**更新配置交易文件

# configtxgen 命令语法

官方参考链接：https://hyperledger-fabric.readthedocs.io/zh_CN/release-1.4/commands/configtxgen.html?highlight=configtx.yaml#null

```bash
Usage of configtxgen:
  -asOrg string
      以特定组织（按名称）执行配置生成，仅包括组织（可能）有权设置的写集中的值。
  -channelCreateTxBaseProfile string
      指定要视为排序系统通道当前状态的轮廓（profile），以允许在通道创建交易生成期间修改非应用程序参数。仅在与 “outputCreateChannelTX”  结合时有效。
  -channelID string
      配置交易中使用的通道 ID。
  -configPath string
      包含所用的配置的路径。（如果设置的话），不设置默认就是$FABRIC_CFG_PATH
  -inspectBlock string
      打印指定路径的区块中包含的配置。
  -inspectChannelCreateTx string
      打印指定路径的交易中包含的配置。
  -outputAnchorPeersUpdate string
      创建一个更新锚节点的配置更新（仅在默认通道创建时有效，并仅用于第一次更新）。
  -outputBlock string
      写入创世区块的路径。（如果设置的话）
  -outputCreateChannelTx string
      写入通道创建交易的路径。（如果设置的话）
  -printOrg string
      以 JSON 方式打印组织的定义。（手动向通道中添加组织时很有用）
  -profile string
      configtx.yaml 中用于生成的轮廓。默认（“SampleInsecureSolo”）
  -version
      显示版本信息。
```

### 生成创世区块

配置修改好后，我们就用configtxgen 生成创世区块。并把这个区块保存到本地channel-artifacts文件夹中：

```bash
configtxgen -profile TwoOrgsOrdererGenesis -outputBlock ./channel-artifacts/genesis.block
```

### 2生成Channel配置区块

```bash
configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID mychannel
```

另外关于锚节点的更新，我们也需要使用这个程序来生成文件：

```
../../build/bin/configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx -channelID mychannel -asOrg Org1MSP

../../build/bin/configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors.tx -channelID mychannel -asOrg Org2MSP
```

最终，我们在channel-artifacts文件夹中，应该是能够看到4个文件。

channel-artifacts/
├── channel.tx
├── genesis.block
├── Org1MSPanchors.tx
└── Org2MSPanchors.tx



## 配置注意点

`configtxgen` 工具的输出依赖于 `configtx.yaml`。`configtx.yaml` 可在 `FABRIC_CFG_PATH` 下找到，且在 `configtxgen` 执行时必须存在。

对许多 `configtxgen` 的操作来说，必须提供轮廓名（profile name）。使用轮廓可以在一个文件里描述多条相似的配置。例如，一个轮廓中可以定义含有3个组织的通道，另一个轮廓可能定义了含4个组织的通道。`configtx.yaml` 依赖 YAML 的锚点和引用特性从而避免文件变得繁重。配置中的基础部分使用锚点标记，例如 `&OrdererDefaults`，然后合并到一个轮廓的引用，例如 `<<: *OrdererDefaults`。要注意的是，当使用轮廓来执行 `configtxgen` 时，重写环境变量不必包含轮廓前缀，可以直接从引用轮廓的根元素开始引用。例如，不用指定 `CONFIGTX_PROFILE_SAMPLEINSECURESOLO_ORDERER_ORDERERTYPE`, 而是省略轮廓的细节，使用 `CONFIGTX` 前缀，后面直接使用相对配置名后的元素，例如 `CONFIGTX_ORDERER_ORDERERTYPE`。



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

**为什么设置锚节点**：锚节点是通道中能被所有对等节点探测、并能与之进行通信的一种对等节点。通道中的每个成员都有一个（或多个，以防单点故障）锚节点，允许属于不同成员身份的节点来发现通道中存在的其它节点。

gossip 利用锚节点来保证不同组织间的互相通信。

当提交了一个包含锚节点更新的配置区块时，Peer 节点会连接到锚节点并获取它所知道的所有节点信息。一个组织中至少有一个节点联系到了锚节点，锚节点就可以获取通道中所有节点的信息。因为 gossip 的通信是固定的，而且 Peer 节点总会被告知它们不知道的节点，所以可以建立起一个通道上成员的视图。

参考：[Gossip数据传播协议](https://hyperledger-fabric.readthedocs.io/zh_CN/release-1.4/gossip.html)

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
        Consortium: SampleConsortium		## 通道所关联的联盟名称
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





# 项目代码

文件名：`configtx.yaml`

```YAML
Organizations:
    # Our orgs start from here
    - &OrdererOrg
        Name: OrdererOrg
        ID: OrdererMSP
        MSPDir: crypto-config/ordererOrganizations/dy/msp
    - &Org1
        Name: Org1MSP
        ID: Org1MSP
        MSPDir: crypto-config/peerOrganizations/org1.dy/msp
        AnchorPeers:
            - Host: peer0.org1.dy
              Port: 7061
    - &Org2
        Name: Org2MSP
        ID: Org2MSP
        MSPDir: crypto-config/peerOrganizations/org2.dy/msp
        AnchorPeers:
            - Host: peer0.org2.dy
              Port: 7062
    - &Org3
        Name: Org3MSP
        ID: Org3MSP
        MSPDir: crypto-config/peerOrganizations/org3.dy/msp
        AnchorPeers:
            - Host: peer0.org3.dy
              Port: 7063
    - &Org4
        Name: Org4MSP
        ID: Org4MSP
        MSPDir: crypto-config/peerOrganizations/org4.dy/msp
        AnchorPeers:
            - Host: peer0.org4.dy
              Port: 7064

Capabilities:
    # Channel capabilities apply to both the orderers and the peers and must be
    # supported by both.
    # Set the value of the capability to true to require it.
    Channel: &ChannelCapabilities
        # V1.3 for Channel is a catchall flag for behavior which has been
        # determined to be desired for all orderers and peers running at the v1.3.x
        # level, but which would be incompatible with orderers and peers from
        # prior releases.
        # Prior to enabling V1.3 channel capabilities, ensure that all
        # orderers and peers on a channel are at v1.3.0 or later.
        V1_3: true

    # Orderer capabilities apply only to the orderers, and may be safely
    # used with prior release peers.
    # Set the value of the capability to true to require it.
    Orderer: &OrdererCapabilities
        # V1.1 for Orderer is a catchall flag for behavior which has been
        # determined to be desired for all orderers running at the v1.1.x
        # level, but which would be incompatible with orderers from prior releases.
        # Prior to enabling V1.1 orderer capabilities, ensure that all
        # orderers on a channel are at v1.1.0 or later.
        V1_1: true

    # Application capabilities apply only to the peer network, and may be safely
    # used with prior release orderers.
    # Set the value of the capability to true to require it.
    Application: &ApplicationCapabilities
        # V1.3 for Application enables the new non-backwards compatible
        # features and fixes of fabric v1.3.
        V1_3: true
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
#   APPLICATION
#
#   This section defines the values to encode into a config transaction or
#   genesis block for application-related parameters.
#
################################################################################
Application: &ApplicationDefaults
    ACLs: &ACLsDefault
        # This section provides defaults for policies for various resources
        # in the system. These "resources" could be functions on system chaincodes
        # (e.g., "GetBlockByNumber" on the "qscc" system chaincode) or other resources
        # (e.g.,who can receive Block events). This section does NOT specify the resource's
        # definition or API, but just the ACL policy for it.
        #
        # User's can override these defaults with their own policy mapping by defining the
        # mapping under ACLs in their channel definition

        #---Lifecycle System Chaincode (lscc) function to policy mapping for access control---#

        # ACL policy for lscc's "getid" function
        lscc/ChaincodeExists: /Channel/Application/Readers

        # ACL policy for lscc's "getdepspec" function
        lscc/GetDeploymentSpec: /Channel/Application/Readers

        # ACL policy for lscc's "getccdata" function
        lscc/GetChaincodeData: /Channel/Application/Readers

        # ACL Policy for lscc's "getchaincodes" function
        lscc/GetInstantiatedChaincodes: /Channel/Application/Readers

        #---Query System Chaincode (qscc) function to policy mapping for access control---#

        # ACL policy for qscc's "GetChainInfo" function
        qscc/GetChainInfo: /Channel/Application/Readers

        # ACL policy for qscc's "GetBlockByNumber" function
        qscc/GetBlockByNumber: /Channel/Application/Readers

        # ACL policy for qscc's  "GetBlockByHash" function
        qscc/GetBlockByHash: /Channel/Application/Readers

        # ACL policy for qscc's "GetTransactionByID" function
        qscc/GetTransactionByID: /Channel/Application/Readers

        # ACL policy for qscc's "GetBlockByTxID" function
        qscc/GetBlockByTxID: /Channel/Application/Readers

        #---Configuration System Chaincode (cscc) function to policy mapping for access control---#

        # ACL policy for cscc's "GetConfigBlock" function
        cscc/GetConfigBlock: /Channel/Application/Readers

        # ACL policy for cscc's "GetConfigTree" function
        cscc/GetConfigTree: /Channel/Application/Readers

        # ACL policy for cscc's "SimulateConfigTreeUpdate" function
        cscc/SimulateConfigTreeUpdate: /Channel/Application/Readers

        #---Miscellanesous peer function to policy mapping for access control---#

        # ACL policy for invoking chaincodes on peer
        peer/Propose: /Channel/Application/Writers

        # ACL policy for chaincode to chaincode invocation
        peer/ChaincodeToChaincode: /Channel/Application/Readers

        #---Events resource to policy mapping for access control###---#

        # ACL policy for sending block events
        event/Block: /Channel/Application/Readers

        # ACL policy for sending filtered block events
        event/FilteredBlock: /Channel/Application/Readers

    # Organizations lists the orgs participating on the application side of the
    # network.
    Organizations:

    # Policies defines the set of policies at this level of the config tree
    # For Application policies, their canonical path is
    #   /Channel/Application/<PolicyName>
    Policies: &ApplicationDefaultPolicies
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"

    # Capabilities describes the application level capabilities, see the
    # dedicated Capabilities section elsewhere in this file for a full
    # description
    Capabilities:
        <<: *ApplicationCapabilities

################################################################################
#
#   ORDERER
#
#   This section defines the values to encode into a config transaction or
#   genesis block for orderer related parameters.
#
################################################################################
Orderer: &OrdererDefaults

    # Orderer Type: The orderer implementation to start.
    # Available types are "solo" and "kafka".
    OrdererType: solo

    # Addresses here is a nonexhaustive list of orderers the peers and clients can
    # connect to. Adding/removing nodes from this list has no impact on their
    # participation in ordering.
    # NOTE: In the solo case, this should be a one-item list.
    Addresses:
        - orderer.dy:7050

    # Batch Timeout: The amount of time to wait before creating a batch.
    BatchTimeout: 2s

    # Batch Size: Controls the number of messages batched into a block.
    # The orderer views messages opaquely, but typically, messages may
    # be considered to be Fabric transactions.  The 'batch' is the group
    # of messages in the 'data' field of the block.  Blocks will be a few kb
    # larger than the batch size, when signatures, hashes, and other metadata
    # is applied.
    BatchSize:

        # Max Message Count: The maximum number of messages to permit in a
        # batch.  No block will contain more than this number of messages.
        MaxMessageCount: 500

        # Absolute Max Bytes: The absolute maximum number of bytes allowed for
        # the serialized messages in a batch. The maximum block size is this value
        # plus the size of the associated metadata (usually a few KB depending
        # upon the size of the signing identities). Any transaction larger than
        # this value will be rejected by ordering. If the "kafka" OrdererType is
        # selected, set 'message.max.bytes' and 'replica.fetch.max.bytes' on
        # the Kafka brokers to a value that is larger than this one.
        AbsoluteMaxBytes: 10 MB

        # Preferred Max Bytes: The preferred maximum number of bytes allowed
        # for the serialized messages in a batch. Roughly, this field may be considered
        # the best effort maximum size of a batch. A batch will fill with messages
        # until this size is reached (or the max message count, or batch timeout is
        # exceeded).  If adding a new message to the batch would cause the batch to
        # exceed the preferred max bytes, then the current batch is closed and written
        # to a block, and a new batch containing the new message is created.  If a
        # message larger than the preferred max bytes is received, then its batch
        # will contain only that message.  Because messages may be larger than
        # preferred max bytes (up to AbsoluteMaxBytes), some batches may exceed
        # the preferred max bytes, but will always contain exactly one transaction.
        PreferredMaxBytes: 2 MB

    # Max Channels is the maximum number of channels to allow on the ordering
    # network. When set to 0, this implies no maximum number of channels.
    MaxChannels: 0

    Kafka:
        # Brokers: A list of Kafka brokers to which the orderer connects. Edit
        # this list to identify the brokers of the ordering service.
        # NOTE: Use IP:port notation.
        Brokers:
            - kafka0:9092
            - kafka1:9092
            - kafka2:9092

    # EtcdRaft defines configuration which must be set when the "etcdraft"
    # orderertype is chosen.
    EtcdRaft:
        # The set of Raft replicas for this network. For the etcd/raft-based
        # implementation, we expect every replica to also be an OSN. Therefore,
        # a subset of the host:port items enumerated in this list should be
        # replicated under the Orderer.Addresses key above.
        Consenters:
            - Host: raft0.example.com
              Port: 7050
              ClientTLSCert: path/to/ClientTLSCert0
              ServerTLSCert: path/to/ServerTLSCert0
            - Host: raft1.example.com
              Port: 7050
              ClientTLSCert: path/to/ClientTLSCert1
              ServerTLSCert: path/to/ServerTLSCert1
            - Host: raft2.example.com
              Port: 7050
              ClientTLSCert: path/to/ClientTLSCert2
              ServerTLSCert: path/to/ServerTLSCert2

        # Options to be specified for all the etcd/raft nodes. The values here
        # are the defaults for all new channels and can be modified on a
        # per-channel basis via configuration updates.
        Options:
            # TickInterval is the time interval between two Node.Tick invocations.
            TickInterval: 500ms

            # ElectionTick is the number of Node.Tick invocations that must pass
            # between elections. That is, if a follower does not receive any
            # message from the leader of current term before ElectionTick has
            # elapsed, it will become candidate and start an election.
            # ElectionTick must be greater than HeartbeatTick.
            ElectionTick: 10

            # HeartbeatTick is the number of Node.Tick invocations that must
            # pass between heartbeats. That is, a leader sends heartbeat
            # messages to maintain its leadership every HeartbeatTick ticks.
            HeartbeatTick: 1

            # MaxInflightBlocks limits the max number of in-flight append messages
            # during optimistic replication phase.
            MaxInflightBlocks: 5

            # SnapshotIntervalSize defines number of bytes per which a snapshot is taken
            SnapshotIntervalSize: 20 MB

    # Organizations lists the orgs participating on the orderer side of the
    # network.
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

    # Capabilities describes the orderer level capabilities, see the
    # dedicated Capabilities section elsewhere in this file for a full
    # description
    Capabilities:
        <<: *OrdererCapabilities

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
#   PROFILES
#
#   Different configuration profiles may be encoded here to be specified as
#   parameters to the configtxgen tool. The profiles which specify consortiums
#   are to be used for generating the orderer genesis block. With the correct
#   consortium members defined in the orderer genesis block, channel creation
#   requests may be generated with only the org member names and a consortium
#   name.
#
################################################################################
Profiles:

    # SampleSingleMSPSolo defines a configuration which uses the Solo orderer,
    # and contains a single MSP definition (the MSP sampleconfig).
    # The Consortium SampleConsortium has only a single member, SampleOrg.
    # SampleSingleMSPSolo:
    #     <<: *ChannelDefaults
    #     Orderer:
    #         <<: *OrdererDefaults
    #         Organizations:
    #             - *SampleOrg
    #     Consortiums:
    #         SampleConsortium:
    #             Organizations:
    #                 - *SampleOrg

    # # SampleSingleMSPKafka defines a configuration that differs from the
    # # SampleSingleMSPSolo one only in that it uses the Kafka-based orderer.
    # SampleSingleMSPKafka:
    #     <<: *ChannelDefaults
    #     Orderer:
    #         <<: *OrdererDefaults
    #         OrdererType: kafka
    #         Organizations:
    #             - *SampleOrg
    #     Consortiums:
    #         SampleConsortium:
    #             Organizations:
    #                 - *SampleOrg

    # # SampleInsecureSolo defines a configuration which uses the Solo orderer,
    # # contains no MSP definitions, and allows all transactions and channel
    # # creation requests for the consortium SampleConsortium.
    # SampleInsecureSolo:
    #     <<: *ChannelDefaults
    #     Orderer:
    #         <<: *OrdererDefaults
    #     Consortiums:
    #         SampleConsortium:
    #             Organizations:

    # # SampleInsecureKafka defines a configuration that differs from the
    # # SampleInsecureSolo one only in that it uses the Kafka-based orderer.
    # SampleInsecureKafka:
    #     <<: *ChannelDefaults
    #     Orderer:
    #         OrdererType: kafka
    #         <<: *OrdererDefaults
    #     Consortiums:
    #         SampleConsortium:
    #             Organizations:

    # # SampleDevModeSolo defines a configuration which uses the Solo orderer,
    # # contains the sample MSP as both orderer and consortium member, and
    # # requires only basic membership for admin privileges. It also defines
    # # an Application on the ordering system channel, which should usually
    # # be avoided.
    # SampleDevModeSolo:
    #     <<: *ChannelDefaults
    #     Orderer:
    #         <<: *OrdererDefaults
    #         Organizations:
    #             - <<: *SampleOrg
    #               Policies:
    #                   <<: *SampleOrgPolicies
    #                   Admins:
    #                       Type: Signature
    #                       Rule: "OR('SampleOrg.member')"
    #     Application:
    #         <<: *ApplicationDefaults
    #         Organizations:
    #             - <<: *SampleOrg
    #               Policies:
    #                   <<: *SampleOrgPolicies
    #                   Admins:
    #                       Type: Signature
    #                       Rule: "OR('SampleOrg.member')"
    #     Consortiums:
    #         SampleConsortium:
    #             Organizations:
    #                 - <<: *SampleOrg
    #                   Policies:
    #                       <<: *SampleOrgPolicies
    #                       Admins:
    #                           Type: Signature
    #                           Rule: "OR('SampleOrg.member')"

    # # SampleDevModeKafka defines a configuration that differs from the
    # # SampleDevModeSolo one only in that it uses the Kafka-based orderer.
    # SampleDevModeKafka:
    #     <<: *ChannelDefaults
    #     Orderer:
    #         <<: *OrdererDefaults
    #         OrdererType: kafka
    #         Organizations:
    #             - <<: *SampleOrg
    #               Policies:
    #                   <<: *SampleOrgPolicies
    #                   Admins:
    #                       Type: Signature
    #                       Rule: "OR('SampleOrg.member')"
    #     Application:
    #         <<: *ApplicationDefaults
    #         Organizations:
    #             - <<: *SampleOrg
    #               Policies:
    #                   <<: *SampleOrgPolicies
    #                   Admins:
    #                       Type: Signature
    #                       Rule: "OR('SampleOrg.member')"
    #     Consortiums:
    #         SampleConsortium:
    #             Organizations:
    #                 - <<: *SampleOrg
    #                   Policies:
    #                       <<: *SampleOrgPolicies
    #                       Admins:
    #                           Type: Signature
    #                           Rule: "OR('SampleOrg.member')"

    # # SampleSingleMSPChannel defines a channel with only the sample org as a
    # # member. It is designed to be used in conjunction with SampleSingleMSPSolo
    # # and SampleSingleMSPKafka orderer profiles.   Note, for channel creation
    # # profiles, only the 'Application' section and consortium # name are
    # # considered.
    # SampleSingleMSPChannel:
    #     Consortium: SampleConsortium
    #     Application:
    #         <<: *ApplicationDefaults
    #         Organizations:
    #             - *SampleOrg

    # # SampleDevModeEtcdRaft defines a configuration that differs from the
    # # SampleDevModeSolo one only in that it uses the etcd/raft-based orderer.
    # SampleDevModeEtcdRaft:
    #     <<: *ChannelDefaults
    #     Orderer:
    #         <<: *OrdererDefaults
    #         OrdererType: etcdraft
    #         Organizations:
    #             - <<: *SampleOrg
    #               Policies:
    #                   <<: *SampleOrgPolicies
    #                   Admins:
    #                       Type: Signature
    #                       Rule: "OR('SampleOrg.member')"
    #     Application:
    #         <<: *ApplicationDefaults
    #         Organizations:
    #             - <<: *SampleOrg
    #               Policies:
    #                   <<: *SampleOrgPolicies
    #                   Admins:
    #                       Type: Signature
    #                       Rule: "OR('SampleOrg.member')"
    #     Consortiums:
    #         SampleConsortium:
    #             Organizations:
    #                 - <<: *SampleOrg
    #                   Policies:
    #                       <<: *SampleOrgPolicies
    #                       Admins:
    #                           Type: Signature
    #                           Rule: "OR('SampleOrg.member')"

    # Our channel start from here
    TestOrgsOrdererGenesis:
        Orderer:
            <<: *OrdererDefaults
            Organizations:
                - *OrdererOrg
        Consortiums:
            SampleConsortium:
                Organizations:
                    - *Org1
                    - *Org2
                    - *Org3
                    - *Org4

    TestOrgsChannel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *Org1
                - *Org2
                - *Org3
                - *Org4
    Org1Channel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *Org1
                - *Org4
    Org2Channel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *Org2
                - *Org4
    Org3Channel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *Org3
                - *Org4
```



# 参考

https://blog.csdn.net/lvyibin890/article/details/106217716