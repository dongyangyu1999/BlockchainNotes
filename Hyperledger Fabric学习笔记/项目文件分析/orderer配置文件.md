# 官方模板

地址：https://github.com/hyperledger/fabric/blob/release-1.4/sampleconfig/orderer.yaml

```YAML
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

---
################################################################################
#
#   Orderer Configuration
#
#   - This controls the type and configuration of the orderer.
#
################################################################################
General:

    # Ledger Type: The ledger type to provide to the orderer.
    # Two non-production ledger types are provided for test purposes only:
    #  - ram: An in-memory ledger whose contents are lost on restart.
    #  - json: A simple file ledger that writes blocks to disk in JSON format.
    # Only one production ledger type is provided:
    #  - file: A production file-based ledger.
    LedgerType: file

    # Listen address: The IP on which to bind to listen.
    ListenAddress: 127.0.0.1

    # Listen port: The port on which to bind to listen.
    ListenPort: 7050

    # TLS: TLS settings for the GRPC server.
    TLS:
        Enabled: false
        # PrivateKey governs the file location of the private key of the TLS certificate.
        PrivateKey: tls/server.key
        # Certificate governs the file location of the server TLS certificate.
        Certificate: tls/server.crt
        RootCAs:
          - tls/ca.crt
        ClientAuthRequired: false
        ClientRootCAs:
    # Keepalive settings for the GRPC server.
    Keepalive:
        # ServerMinInterval is the minimum permitted time between client pings.
        # If clients send pings more frequently, the server will
        # disconnect them.
        ServerMinInterval: 60s
        # ServerInterval is the time between pings to clients.
        ServerInterval: 7200s
        # ServerTimeout is the duration the server waits for a response from
        # a client before closing the connection.
        ServerTimeout: 20s
    # Cluster settings for ordering service nodes that communicate with other ordering service nodes
    # such as Raft based ordering service.
    Cluster:
        # SendBufferSize is the maximum number of messages in the egress buffer.
        # Consensus messages are dropped if the buffer is full, and transaction
        # messages are waiting for space to be freed.
        SendBufferSize: 10
        # ClientCertificate governs the file location of the client TLS certificate
        # used to establish mutual TLS connections with other ordering service nodes.
        ClientCertificate:
        # ClientPrivateKey governs the file location of the private key of the client TLS certificate.
        ClientPrivateKey:
        # The below 4 properties should be either set together, or be unset together.
        # If they are set, then the orderer node uses a separate listener for intra-cluster
        # communication. If they are unset, then the general orderer listener is used.
        # This is useful if you want to use a different TLS server certificates on the
        # client-facing and the intra-cluster listeners.

        # ListenPort defines the port on which the cluster listens to connections.
        ListenPort:
        # ListenAddress defines the IP on which to listen to intra-cluster communication.
        ListenAddress:
        # ServerCertificate defines the file location of the server TLS certificate used for intra-cluster
        # communication.
        ServerCertificate:
        # ServerPrivateKey defines the file location of the private key of the TLS certificate.
        ServerPrivateKey:
    # Genesis method: The method by which the genesis block for the orderer
    # system channel is specified. Available options are "provisional", "file":
    #  - provisional: Utilizes a genesis profile, specified by GenesisProfile,
    #                 to dynamically generate a new genesis block.
    #  - file: Uses the file provided by GenesisFile as the genesis block.
    GenesisMethod: provisional

    # Genesis profile: The profile to use to dynamically generate the genesis
    # block to use when initializing the orderer system channel and
    # GenesisMethod is set to "provisional". See the configtx.yaml file for the
    # descriptions of the available profiles. Ignored if GenesisMethod is set to
    # "file".
    GenesisProfile: SampleInsecureSolo

    # Genesis file: The file containing the genesis block to use when
    # initializing the orderer system channel and GenesisMethod is set to
    # "file". Ignored if GenesisMethod is set to "provisional".
    GenesisFile: genesisblock

    # LocalMSPDir is where to find the private crypto material needed by the
    # orderer. It is set relative here as a default for dev environments but
    # should be changed to the real location in production.
    LocalMSPDir: msp

    # LocalMSPID is the identity to register the local MSP material with the MSP
    # manager. IMPORTANT: The local MSP ID of an orderer needs to match the MSP
    # ID of one of the organizations defined in the orderer system channel's
    # /Channel/Orderer configuration. The sample organization defined in the
    # sample configuration provided has an MSP ID of "SampleOrg".
    LocalMSPID: SampleOrg

    # Enable an HTTP service for Go "pprof" profiling as documented at:
    # https://golang.org/pkg/net/http/pprof
    Profile:
        Enabled: false
        Address: 0.0.0.0:6060

    # BCCSP configures the blockchain crypto service providers.
    BCCSP:
        # Default specifies the preferred blockchain crypto service provider
        # to use. If the preferred provider is not available, the software
        # based provider ("SW") will be used.
        # Valid providers are:
        #  - SW: a software based crypto provider
        #  - PKCS11: a CA hardware security module crypto provider.
        Default: SW

        # SW configures the software based blockchain crypto provider.
        SW:
            # TODO: The default Hash and Security level needs refactoring to be
            # fully configurable. Changing these defaults requires coordination
            # SHA2 is hardcoded in several places, not only BCCSP
            Hash: SHA2
            Security: 256
            # Location of key store. If this is unset, a location will be
            # chosen using: 'LocalMSPDir'/keystore
            FileKeyStore:
                KeyStore:

        # Settings for the PKCS#11 crypto provider (i.e. when DEFAULT: PKCS11)
        PKCS11:
            # Location of the PKCS11 module library
            Library:
            # Token Label
            Label:
            # User PIN
            Pin:
            Hash:
            Security:
            FileKeyStore:
                KeyStore:

    # Authentication contains configuration parameters related to authenticating
    # client messages
    Authentication:
        # the acceptable difference between the current server time and the
        # client's time as specified in a client request message
        TimeWindow: 15m


################################################################################
#
#   SECTION: File Ledger
#
#   - This section applies to the configuration of the file or json ledgers.
#
################################################################################
FileLedger:

    # Location: The directory to store the blocks in.
    # NOTE: If this is unset, a new temporary location will be chosen every time
    # the orderer is restarted, using the prefix specified by Prefix.
    Location: /var/hyperledger/production/orderer

    # The prefix to use when generating a ledger directory in temporary space.
    # Otherwise, this value is ignored.
    Prefix: hyperledger-fabric-ordererledger

################################################################################
#
#   SECTION: RAM Ledger
#
#   - This section applies to the configuration of the RAM ledger.
#
################################################################################
RAMLedger:

    # History Size: The number of blocks that the RAM ledger is set to retain.
    # WARNING: Appending a block to the ledger might cause the oldest block in
    # the ledger to be dropped in order to limit the number total number blocks
    # to HistorySize. For example, if history size is 10, when appending block
    # 10, block 0 (the genesis block!) will be dropped to make room for block 10.
    HistorySize: 1000

################################################################################
#
#   SECTION: Kafka
#
#   - This section applies to the configuration of the Kafka-based orderer, and
#     its interaction with the Kafka cluster.
#
################################################################################
Kafka:

    # Retry: What do if a connection to the Kafka cluster cannot be established,
    # or if a metadata request to the Kafka cluster needs to be repeated.
    Retry:
        # When a new channel is created, or when an existing channel is reloaded
        # (in case of a just-restarted orderer), the orderer interacts with the
        # Kafka cluster in the following ways:
        # 1. It creates a Kafka producer (writer) for the Kafka partition that
        # corresponds to the channel.
        # 2. It uses that producer to post a no-op CONNECT message to that
        # partition
        # 3. It creates a Kafka consumer (reader) for that partition.
        # If any of these steps fail, they will be re-attempted every
        # <ShortInterval> for a total of <ShortTotal>, and then every
        # <LongInterval> for a total of <LongTotal> until they succeed.
        # Note that the orderer will be unable to write to or read from a
        # channel until all of the steps above have been completed successfully.
        ShortInterval: 5s
        ShortTotal: 10m
        LongInterval: 5m
        LongTotal: 12h
        # Affects the socket timeouts when waiting for an initial connection, a
        # response, or a transmission. See Config.Net for more info:
        # https://godoc.org/github.com/Shopify/sarama#Config
        NetworkTimeouts:
            DialTimeout: 10s
            ReadTimeout: 10s
            WriteTimeout: 10s
        # Affects the metadata requests when the Kafka cluster is in the middle
        # of a leader election.See Config.Metadata for more info:
        # https://godoc.org/github.com/Shopify/sarama#Config
        Metadata:
            RetryBackoff: 250ms
            RetryMax: 3
        # What to do if posting a message to the Kafka cluster fails. See
        # Config.Producer for more info:
        # https://godoc.org/github.com/Shopify/sarama#Config
        Producer:
            RetryBackoff: 100ms
            RetryMax: 3
        # What to do if reading from the Kafka cluster fails. See
        # Config.Consumer for more info:
        # https://godoc.org/github.com/Shopify/sarama#Config
        Consumer:
            RetryBackoff: 2s
    # Settings to use when creating Kafka topics.  Only applies when
    # Kafka.Version is v0.10.1.0 or higher
    Topic:
        # The number of Kafka brokers across which to replicate the topic
        ReplicationFactor: 3
    # Verbose: Enable logging for interactions with the Kafka cluster.
    Verbose: false

    # TLS: TLS settings for the orderer's connection to the Kafka cluster.
    TLS:

      # Enabled: Use TLS when connecting to the Kafka cluster.
      Enabled: false

      # PrivateKey: PEM-encoded private key the orderer will use for
      # authentication.
      PrivateKey:
        # As an alternative to specifying the PrivateKey here, uncomment the
        # following "File" key and specify the file name from which to load the
        # value of PrivateKey.
        #File: path/to/PrivateKey

      # Certificate: PEM-encoded signed public key certificate the orderer will
      # use for authentication.
      Certificate:
        # As an alternative to specifying the Certificate here, uncomment the
        # following "File" key and specify the file name from which to load the
        # value of Certificate.
        #File: path/to/Certificate

      # RootCAs: PEM-encoded trusted root certificates used to validate
      # certificates from the Kafka cluster.
      RootCAs:
        # As an alternative to specifying the RootCAs here, uncomment the
        # following "File" key and specify the file name from which to load the
        # value of RootCAs.
        #File: path/to/RootCAs

    # SASLPlain: Settings for using SASL/PLAIN authentication with Kafka brokers
    SASLPlain:
      # Enabled: Use SASL/PLAIN to authenticate with Kafka brokers
      Enabled: false
      # User: Required when Enabled is set to true
      User:
      # Password: Required when Enabled is set to true
      Password:

    # Kafka protocol version used to communicate with the Kafka cluster brokers
    # (defaults to 0.10.2.0 if not specified)
    Version:

################################################################################
#
#   Debug Configuration
#
#   - This controls the debugging options for the orderer
#
################################################################################
Debug:

    # BroadcastTraceDir when set will cause each request to the Broadcast service
    # for this orderer to be written to a file in this directory
    BroadcastTraceDir:

    # DeliverTraceDir when set will cause each request to the Deliver service
    # for this orderer to be written to a file in this directory
    DeliverTraceDir:

################################################################################
#
#   Operations Configuration
#
#   - This configures the operations server endpoint for the orderer
#
################################################################################
Operations:
    # host and port for the operations server
    ListenAddress: 127.0.0.1:8443

    # TLS configuration for the operations endpoint
    TLS:
        # TLS enabled
        Enabled: false

        # Certificate is the location of the PEM encoded TLS certificate
        Certificate:

        # PrivateKey points to the location of the PEM-encoded key
        PrivateKey:

        # Most operations service endpoints require client authentication when TLS
        # is enabled. ClientAuthRequired requires client certificate authentication
        # at the TLS layer to access all resources.
        ClientAuthRequired: false

        # Paths to PEM encoded ca certificates to trust for client authentication
        ClientRootCAs: []

################################################################################
#
#   Metrics  Configuration
#
#   - This configures metrics collection for the orderer
#
################################################################################
Metrics:
    # The metrics provider is one of statsd, prometheus, or disabled
    Provider: disabled

    # The statsd configuration
    Statsd:
      # network type: tcp or udp
      Network: udp

      # the statsd server address
      Address: 127.0.0.1:8125

      # The interval at which locally cached counters and gauges are pushed
      # to statsd; timings are pushed immediately
      WriteInterval: 30s

      # The prefix is prepended to all emitted statsd metrics
      Prefix:

################################################################################
#
#   Consensus Configuration
#
#   - This section contains config options for a consensus plugin. It is opaque
#     to orderer, and completely up to consensus implementation to make use of.
#
################################################################################
Consensus:
    # The allowed key-value pairs here depend on consensus plugin. For etcd/raft,
    # we use following options:

    # WALDir specifies the location at which Write Ahead Logs for etcd/raft are
    # stored. Each channel will have its own subdir named after channel ID.
    WALDir: /var/hyperledger/production/orderer/etcdraft/wal

    # SnapDir specifies the location at which snapshots for etcd/raft are
    # stored. Each channel will have its own subdir named after channel ID.
    SnapDir: /var/hyperledger/production/orderer/etcdraft/snapshot
```

# orderer模块

## 介绍

Orderer 模块负责对<u>交易进行排序</u>, 并将排序好的交易打包成区块。Orderer节点的配置信息通常放在环境变量或者配置文件中，在具体操作中，如果是通过docker镜像文件的方式启动Orderer，推荐使用环境变量的方式，如果是采用命令的方式直接启动，推荐将所有的信息放到配置文件中。下面将会详细介绍其用到的环境变量。

Hyperledger Fabric v1.4重心是在稳定性和生产运维上，作为首个长期支持版本，v1.4版本可以无缝升级至后续有针对 v1.4 的补丁版本，因此 fabric v1.4 版本是推荐的生产应用版本。

## 命令

```bash
root@localhost fabric $ orderer --help
usage: orderer [<flags>] <command> [<args> ...]

Hyperledger Fabric orderer node

Flags:
  --help  Show context-sensitive help (also try --help-long and --help-man).

Commands:
  help [<command>...]
    Show help.

  start*
    Start the orderer node
  version
    Show version information
  benchmark
    Run orderer in benchmark mode
```



# 详解

该 `orderer.yaml `示例配置文件中共指定了五大部分内容

* General
* FileLedger / 文本账本配置信息
* RAMLedger / 内存账本配置信息
* Kafka / Kafka集群的配置信息
* Debug 调试配置信息

v1.4 新增运维配置

* Operations / 运维服务配置
* Metrics / 服务度量监控配置
* Consensus / 共识配置

## General

```YAML
General:
    LedgerType: file	# 指定账本类型（可选file、RAM、json三种）
    ListenAddress: 127.0.0.1	# 监听地址
    ListenPort: 7050	# 监听端口号

    TLS:	# GRPC服务器的TLS设置
        Enabled: false	# 默认不启用
        PrivateKey: tls/server.key	# 签名的私钥文件
        Certificate: tls/server.crt	# 证书文件
        RootCAs:	# 可信任的根CA证书
          - tls/ca.crt
        ClientAuthRequired: false
        ClientRootCAs:

    Keepalive:	# GRPC服务器的激活设置
        ServerMinInterval: 60s	# 客户机ping之间的最小允许时间
        ServerInterval: 7200s	# 连接到客户机的ping之间的时间
        ServerTimeout: 20s	# 服务器等待响应的超时时间

    LogLevel: info
   
    LogFormat: '%{color}%{time:2006-01-02 15:04:05.000 MST} [%{module}] %{shortfunc} -> %{level:.4s} %{id:03x}%{color:reset} %{message}'

    GenesisMethod: provisional	# 生成初始区块的提供方式（可选provisional、file两种）
    GenesisProfile: SampleInsecureSolo	# 用于动态生成初始区块的概要
    GenesisFile: genesisblock	# 生成初始区块的配置文件 
    
    LocalMSPDir: msp	# 本地MSP目录 
    LocalMSPID: SampleOrg	# MSP ID

    Profile:	# 是否为Go“profiling”配置启用HTTP服务
        Enabled: false
        Address: 0.0.0.0:6060

    BCCSP:	# 区块链加密实现
        Default: SW	# 默认使用SW
        SW:
            Hash: SHA2
            Security: 256
            FileKeyStore:
                KeyStore:

    Authentication:	# 与身份验证相关的配置
        TimeWindow: 15m	# # 当前服务器时间与客户端请求消息中指定的客户端时间差异
```

## FileLedger / 文本账本配置信息

```YAML
FileLedger:
    Location: /var/hyperledger/production/orderer	# 区块存储路径
    Prefix: hyperledger-fabric-ordererledger	# 在临时空间中生成分类目录时使用的前缀
```

## RAMLedger / 内存账本配置信息

```YAML
RAMLedger:
    HistorySize: 1000	# 如果设置为保存在内存中，分类帐最大保留的块的数量
```

## Kafka / Kafka集群配置信息

```YAML
Kafka:
    Retry:	# 无法建立到Kafka集群的连接时的重试请求    
        ShortInterval: 5s	# 重试时间间隔
        ShortTotal: 10m		# 重试的总时间
        LongInterval: 5m	# 重试失败后再次发送重试的时间间隔
        LongTotal: 12h		# 重试的最长总时间
        NetworkTimeouts:    # 网络超时设置
            DialTimeout: 10s
            ReadTimeout: 10s
            WriteTimeout: 10s
        Metadata:	# 请求领导人选举时影响元数据的设置
            RetryBackoff: 250ms	# 指定重试的最大时间
            RetryMax: 3	# 重试的最大次数
        Producer:	# 向Kafka集群发送消息失败的设置
            RetryBackoff: 100ms	# 指定重试的最大时间
            RetryMax: 3	# 重试的最大次数
        Consumer:	# 向Kafka集群读取消息失败的设置
            RetryBackoff: 2s	# 指定重试的最大时间

    Verbose: false	# 是否为与Kafka集群的交互启用日志记录

    TLS:	# Orderer连接到Kafka集群的TLS设置
      Enabled: false	# 连接到Kafka集群时是否使用TLS
      PrivateKey:      
      Certificate:       
      RootCAs:

    Version:	# Kafka版本(未指定默认值为0.10.2.0)
```

## Debug / 调试配置信息

```YAML
Debug:
    BroadcastTraceDir:	# 对广播服务的每个请求写入此目录中的文件
    DeliverTraceDir:	# 对交付服务的每个请求写入此目录中的文件
```

## Operations / 运维服务

Fabric 1.4 引入operation severice，即运维服务接口。

orderer,peer节点可提供http服务, 方便外部获取节点的运行指标，管理日志级别，健康检查。

```YAML
Operations:
    # host and port for the operations server
    ListenAddress: 0.0.0.0:8443

    # TLS configuration for the operations endpoint
    TLS:
        # TLS enabled
        Enabled: false

        # Certificate is the location of the PEM encoded TLS certificate
        Certificate:

        # PrivateKey points to the location of the PEM-encoded key
        PrivateKey:

        # Most operations service endpoints require client authentication when TLS
        # is enabled. ClientAuthRequired requires client certificate authentication
        # at the TLS layer to access all resources.
        ClientAuthRequired: false

        # Paths to PEM encoded ca certificates to trust for client authentication
        ClientRootCAs: []
```



## Metrics / 标准配置

这将为orderer配置度量标准集合。

```yaml
Metrics:
    # The metrics provider is one of statsd, prometheus, or disabled
    Provider: disabled

    # The statsd configuration
    Statsd:
      # network type: tcp or udp
      Network: udp

      # the statsd server address
      Address: 127.0.0.1:8125

      # The interval at which locally cached counters and gauges are pushed
      # to statsd; timings are pushed immediately
      WriteInterval: 30s

      # The prefix is prepended to all emitted statsd metrics
      Prefix:
```





## Consensus / 共识配置

本节包含共识插件的配置选项。它对orderer是不透明的，完全取决于共识实现的使用。

```YAML
Consensus:
    # The allowed key-value pairs here depend on consensus plugin. For etcd/raft,
    # we use following options:

    # WALDir specifies the location at which Write Ahead Logs for etcd/raft are
    # stored. Each channel will have its own subdir named after channel ID.
    # WALDir: /var/hyperledger/production/orderer/etcdraft/wal

    # SnapDir specifies the location at which snapshots for etcd/raft are
    # stored. Each channel will have its own subdir named after channel ID.
    # SnapDir: /var/hyperledger/production/orderer/etcdraft/snapshot
```











# 项目代码

```yaml
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

---
################################################################################
#
#   Orderer Configuration
#
#   - This controls the type and configuration of the orderer.
#
################################################################################
General:

    # Ledger Type: The ledger type to provide to the orderer.
    # Two non-production ledger types are provided for test purposes only:
    #  - ram: An in-memory ledger whose contents are lost on restart.
    #  - json: A simple file ledger that writes blocks to disk in JSON format.
    # Only one production ledger type is provided:
    #  - file: A production file-based ledger.
    LedgerType: file

    # Listen address: The IP on which to bind to listen.
    ListenAddress: 0.0.0.0

    # Listen port: The port on which to bind to listen.
    ListenPort: 7050

    # TLS: TLS settings for the GRPC server.
    TLS:
        Enabled: false
        # PrivateKey governs the file location of the private key of the TLS certificate.
        PrivateKey: crypto-config/ordererOrganizations/dy/orderers/orderer.dy/tls/server.key
        # Certificate governs the file location of the server TLS certificate.
        Certificate: crypto-config/ordererOrganizations/dy/orderers/orderer.dy/tls/server.crt
        RootCAs:
          - crypto-config/ordererOrganizations/dy/orderers/orderer.dy/tls/ca.crt
        ClientAuthRequired: false
        ClientRootCAs:
    # Keepalive settings for the GRPC server.
    Keepalive:
        # ServerMinInterval is the minimum permitted time between client pings.
        # If clients send pings more frequently, the server will
        # disconnect them.
        ServerMinInterval: 60s
        # ServerInterval is the time between pings to clients.
        ServerInterval: 7200s
        # ServerTimeout is the duration the server waits for a response from
        # a client before closing the connection.
        ServerTimeout: 20s
    # Cluster settings for ordering service nodes that communicate with other ordering service nodes
    # such as Raft based ordering service.
    Cluster:
        # SendBufferSize is the maximum number of messages in the egress buffer.
        # Consensus messages are dropped if the buffer is full, and transaction
        # messages are waiting for space to be freed.
        SendBufferSize: 10
        # ClientCertificate governs the file location of the client TLS certificate
        # used to establish mutual TLS connections with other ordering service nodes.
        ClientCertificate:
        # ClientPrivateKey governs the file location of the private key of the client TLS certificate.
        ClientPrivateKey:
        # The below 4 properties should be either set together, or be unset together.
        # If they are set, then the orderer node uses a separate listener for intra-cluster
        # communication. If they are unset, then the general orderer listener is used.
        # This is useful if you want to use a different TLS server certificates on the
        # client-facing and the intra-cluster listeners.

        # ListenPort defines the port on which the cluster listens to connections.
        ListenPort:
        # ListenAddress defines the IP on which to listen to intra-cluster communication.
        ListenAddress:
        # ServerCertificate defines the file location of the server TLS certificate used for intra-cluster
        # communication.
        ServerCertificate:
        # ServerPrivateKey defines the file location of the private key of the TLS certificate.
        ServerPrivateKey:
    # Genesis method: The method by which the genesis block for the orderer
    # system channel is specified. Available options are "provisional", "file":
    #  - provisional: Utilizes a genesis profile, specified by GenesisProfile,
    #                 to dynamically generate a new genesis block.
    #  - file: Uses the file provided by GenesisFile as the genesis block.
    GenesisMethod: file

    # Genesis profile: The profile to use to dynamically generate the genesis
    # block to use when initializing the orderer system channel and
    # GenesisMethod is set to "provisional". See the configtx.yaml file for the
    # descriptions of the available profiles. Ignored if GenesisMethod is set to
    # "file".
    GenesisProfile: SampleInsecureSolo

    # Genesis file: The file containing the genesis block to use when
    # initializing the orderer system channel and GenesisMethod is set to
    # "file". Ignored if GenesisMethod is set to "provisional".
    GenesisFile: ../orderer/orderer-genesis.block

    # LocalMSPDir is where to find the private crypto material needed by the
    # orderer. It is set relative here as a default for dev environments but
    # should be changed to the real location in production.
    LocalMSPDir: crypto-config/ordererOrganizations/dy/orderers/orderer.dy/msp

    # LocalMSPID is the identity to register the local MSP material with the MSP
    # manager. IMPORTANT: The local MSP ID of an orderer needs to match the MSP
    # ID of one of the organizations defined in the orderer system channel's
    # /Channel/Orderer configuration. The sample organization defined in the
    # sample configuration provided has an MSP ID of "SampleOrg".
    LocalMSPID: OrdererMSP

    # Enable an HTTP service for Go "pprof" profiling as documented at:
    # https://golang.org/pkg/net/http/pprof
    Profile:
        Enabled: false
        Address: 0.0.0.0:6060

    # BCCSP configures the blockchain crypto service providers.
    BCCSP:
        # Default specifies the preferred blockchain crypto service provider
        # to use. If the preferred provider is not available, the software
        # based provider ("SW") will be used.
        # Valid providers are:
        #  - SW: a software based crypto provider
        #  - PKCS11: a CA hardware security module crypto provider.
        Default: SW

        # SW configures the software based blockchain crypto provider.
        SW:
            # TODO: The default Hash and Security level needs refactoring to be
            # fully configurable. Changing these defaults requires coordination
            # SHA2 is hardcoded in several places, not only BCCSP
            Hash: SHA2
            Security: 256
            # Location of key store. If this is unset, a location will be
            # chosen using: 'LocalMSPDir'/keystore
            FileKeyStore:
                KeyStore:

    # Authentication contains configuration parameters related to authenticating
    # client messages
    Authentication:
        # the acceptable difference between the current server time and the
        # client's time as specified in a client request message
        TimeWindow: 15m

################################################################################
#
#   SECTION: File Ledger
#
#   - This section applies to the configuration of the file or json ledgers.
#
################################################################################
FileLedger:

    # Location: The directory to store the blocks in.
    # NOTE: If this is unset, a new temporary location will be chosen every time
    # the orderer is restarted, using the prefix specified by Prefix.
    Location: ../orderer/production

    # The prefix to use when generating a ledger directory in temporary space.
    # Otherwise, this value is ignored.
    Prefix: hyperledger-fabric-ordererledger

################################################################################
#
#   SECTION: RAM Ledger
#
#   - This section applies to the configuration of the RAM ledger.
#
################################################################################
RAMLedger:

    # History Size: The number of blocks that the RAM ledger is set to retain.
    # WARNING: Appending a block to the ledger might cause the oldest block in
    # the ledger to be dropped in order to limit the number total number blocks
    # to HistorySize. For example, if history size is 10, when appending block
    # 10, block 0 (the genesis block!) will be dropped to make room for block 10.
    HistorySize: 1000

################################################################################
#
#   SECTION: Kafka
#
#   - This section applies to the configuration of the Kafka-based orderer, and
#     its interaction with the Kafka cluster.
#
################################################################################
Kafka:

    # Retry: What do if a connection to the Kafka cluster cannot be established,
    # or if a metadata request to the Kafka cluster needs to be repeated.
    Retry:
        # When a new channel is created, or when an existing channel is reloaded
        # (in case of a just-restarted orderer), the orderer interacts with the
        # Kafka cluster in the following ways:
        # 1. It creates a Kafka producer (writer) for the Kafka partition that
        # corresponds to the channel.
        # 2. It uses that producer to post a no-op CONNECT message to that
        # partition
        # 3. It creates a Kafka consumer (reader) for that partition.
        # If any of these steps fail, they will be re-attempted every
        # <ShortInterval> for a total of <ShortTotal>, and then every
        # <LongInterval> for a total of <LongTotal> until they succeed.
        # Note that the orderer will be unable to write to or read from a
        # channel until all of the steps above have been completed successfully.
        ShortInterval: 5s
        ShortTotal: 10m
        LongInterval: 5m
        LongTotal: 12h
        # Affects the socket timeouts when waiting for an initial connection, a
        # response, or a transmission. See Config.Net for more info:
        # https://godoc.org/github.com/Shopify/sarama#Config
        NetworkTimeouts:
            DialTimeout: 10s
            ReadTimeout: 10s
            WriteTimeout: 10s
        # Affects the metadata requests when the Kafka cluster is in the middle
        # of a leader election.See Config.Metadata for more info:
        # https://godoc.org/github.com/Shopify/sarama#Config
        Metadata:
            RetryBackoff: 250ms
            RetryMax: 3
        # What to do if posting a message to the Kafka cluster fails. See
        # Config.Producer for more info:
        # https://godoc.org/github.com/Shopify/sarama#Config
        Producer:
            RetryBackoff: 100ms
            RetryMax: 3
        # What to do if reading from the Kafka cluster fails. See
        # Config.Consumer for more info:
        # https://godoc.org/github.com/Shopify/sarama#Config
        Consumer:
            RetryBackoff: 2s
    # Settings to use when creating Kafka topics.  Only applies when
    # Kafka.Version is v0.10.1.0 or higher
    Topic:
        # The number of Kafka brokers across which to replicate the topic
        ReplicationFactor: 3
    # Verbose: Enable logging for interactions with the Kafka cluster.
    Verbose: false

    # TLS: TLS settings for the orderer's connection to the Kafka cluster.
    TLS:

      # Enabled: Use TLS when connecting to the Kafka cluster.
      Enabled: false

      # PrivateKey: PEM-encoded private key the orderer will use for
      # authentication.
      PrivateKey:
        # As an alternative to specifying the PrivateKey here, uncomment the
        # following "File" key and specify the file name from which to load the
        # value of PrivateKey.
        #File: path/to/PrivateKey

      # Certificate: PEM-encoded signed public key certificate the orderer will
      # use for authentication.
      Certificate:
        # As an alternative to specifying the Certificate here, uncomment the
        # following "File" key and specify the file name from which to load the
        # value of Certificate.
        #File: path/to/Certificate

      # RootCAs: PEM-encoded trusted root certificates used to validate
      # certificates from the Kafka cluster.
      RootCAs:
        # As an alternative to specifying the RootCAs here, uncomment the
        # following "File" key and specify the file name from which to load the
        # value of RootCAs.
        #File: path/to/RootCAs

    # SASLPlain: Settings for using SASL/PLAIN authentication with Kafka brokers
    SASLPlain:
      # Enabled: Use SASL/PLAIN to authenticate with Kafka brokers
      Enabled: false
      # User: Required when Enabled is set to true
      User:
      # Password: Required when Enabled is set to true
      Password:

    # Kafka protocol version used to communicate with the Kafka cluster brokers
    # (defaults to 0.10.2.0 if not specified)
    Version:

################################################################################
#
#   Debug Configuration
#
#   - This controls the debugging options for the orderer
#
################################################################################
Debug:

    # BroadcastTraceDir when set will cause each request to the Broadcast service
    # for this orderer to be written to a file in this directory
    BroadcastTraceDir:

    # DeliverTraceDir when set will cause each request to the Deliver service
    # for this orderer to be written to a file in this directory
    DeliverTraceDir:

################################################################################
#
#   Operations Configuration
#
#   - This configures the operations server endpoint for the orderer
#
################################################################################
Operations:
    # host and port for the operations server
    ListenAddress: 0.0.0.0:8443

    # TLS configuration for the operations endpoint
    TLS:
        # TLS enabled
        Enabled: false

        # Certificate is the location of the PEM encoded TLS certificate
        Certificate:

        # PrivateKey points to the location of the PEM-encoded key
        PrivateKey:

        # Most operations service endpoints require client authentication when TLS
        # is enabled. ClientAuthRequired requires client certificate authentication
        # at the TLS layer to access all resources.
        ClientAuthRequired: false

        # Paths to PEM encoded ca certificates to trust for client authentication
        ClientRootCAs: []

################################################################################
#
#   Metrics  Configuration
#
#   - This configures metrics collection for the orderer
#
################################################################################
Metrics:
    # The metrics provider is one of statsd, prometheus, or disabled
    Provider: disabled

    # The statsd configuration
    Statsd:
      # network type: tcp or udp
      Network: udp

      # the statsd server address
      Address: 127.0.0.1:8125

      # The interval at which locally cached counters and gauges are pushed
      # to statsd; timings are pushed immediately
      WriteInterval: 30s

      # The prefix is prepended to all emitted statsd metrics
      Prefix:

################################################################################
#
#   Consensus Configuration
#
#   - This section contains config options for a consensus plugin. It is opaque
#     to orderer, and completely up to consensus implementation to make use of.
#
################################################################################
Consensus:
    # The allowed key-value pairs here depend on consensus plugin. For etcd/raft,
    # we use following options:

    # WALDir specifies the location at which Write Ahead Logs for etcd/raft are
    # stored. Each channel will have its own subdir named after channel ID.
    # WALDir: /var/hyperledger/production/orderer/etcdraft/wal

    # SnapDir specifies the location at which snapshots for etcd/raft are
    # stored. Each channel will have its own subdir named after channel ID.
    # SnapDir: /var/hyperledger/production/orderer/etcdraft/snapshot
```

