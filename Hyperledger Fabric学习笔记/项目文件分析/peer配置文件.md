# 官方模板

> 链接：https://github.com/hyperledger/fabric/blob/release-1.4/sampleconfig/core.yaml

```bash
###############################################################################
#
#    Peer section  peer部分
#
###############################################################################
peer:
    id: jdoe            ##  Peer节点ID
    networkId: dev      ##  网络ID
    listenAddress: 0.0.0.0:7051     ##  节点监听的本地网络接口地址
    chaincodeAddress: 0.0.0.0:7052  ##  链码容器连接时的监听地址 如未指定, 则使用listenAddress的IP地址和7052端口
    address: 0.0.0.0:7051           ##  节点对外的服务地址 （对外的地址）【还有人说是 与同组织内其他peer通信的地址; 配置在cli节点上表示peer与其通信的地址 ？？】
    addressAutoDetect: false        ##  是否自动探测服务地址 (默认 关闭， 如果启用TLS时，最好关闭)
    gomaxprocs: -1                  ##  Go的进程限制数 runtime.GOMAXPROCS(n) 默认 -1
    keepalive:                      ##  客户端和peer间的网络心跳连接配置
       
        minInterval: 60s            ##  最小的心跳间隔时间
        client: ##  该节点和客户端 的交互配置
            interval: 60s   ##  和客户端 的心跳间隔 必须 interval >= minInterval
            timeout: 20s    ##  和客户端 间的网络连接超时时间
        deliveryClient: ## 交付客户端用于与订购节点通信的心跳
            interval: 60s
            timeout: 20s
    gossip:     ##  节点间通信的gossip 协议的P2P通信 【主要包含了 启动 及 连接】
        bootstrap: 127.0.0.1:7051   ##  启动节点后 向哪些节点发起gossip连接，以加入网络，且节点相互间都是同一个组织的
        useLeaderElection: true     ##  是否启动动态选举 组织的Leader 节点 与 orgLeader 互斥
        orgLeader: false            ##  是否指定本节点为 组织Leader 节点 与 useLeaderElection 互斥
        endpoint:                   ##  本节点在组织内的gossip id
        maxBlockCountToStore: 100   ##  保存到内存的区块个数上限
        maxPropagationBurstLatency: 10ms    ##  保存消息的最大时间，超过则触发转发给其他节点
        maxPropagationBurstSize: 10         ##  保存的最大消息个数，超过则触发转发给其他节点
        propagateIterations: 1              ##  消息转发的次数
        propagatePeerNum: 3         ##  推送消息给指定个数的节点
        pullInterval: 4s            ##  拉取消息的时间间隔  (unit: second) 必须大于 digestWaitTime + responseWaitTime
        pullPeerNum: 3              ##  从指定个数的节点拉取消息
        requestStateInfoInterval: 4s        ##  从节点拉取状态信息(StateInfo) 消息间隔 (unit: second)
        publishStateInfoInterval: 4s        ##  向其他节点推动状态信息消息的间隔 (unit: second)
        stateInfoRetentionInterval:         ##  状态信息消息的超时时间 (unit: second)
        publishCertPeriod: 10s      ##  启动后在心跳消息中包括证书的等待时间
        skipBlockVerification: false        ##  是否不对区块消息进行校验，默认为false
        dialTimeout: 3s             ##  gRPC 连接拨号的超时 (unit: second)
        connTimeout: 2s             ##  建立连接的超时 (unit: second)
        recvBuffSize: 20            ##  收取消息的缓冲大小
        sendBuffSize: 200           ##  发送消息的缓冲大小
        digestWaitTime: 1s          ##  处理摘要数据的等待时间 (unit: second)  可以大于 requestWaitTime
        requestWaitTime: 1500ms     ##  处理nonce 数据的等待时间 (unit: milliseconds) 可以大于 digestWaitTime
        responseWaitTime: 2s        ##  终止拉取数据处理的等待时间 (unit: second)
        aliveTimeInterval: 5s       ##  定期发送Alive 心跳消息的时间间隔 (unit: second)
        aliveExpirationTimeout: 25s ##  Alive 心跳消息的超时时间 (unit: second)
        reconnectInterval: 25s      ##  断线后重连的时间间隔 (unit: second)
        externalEndpoint:           ##  节点被组织外节点感知时的地址，公布给其他组织的地址和端口, 如果不指定, 其他组织将无法知道本peer的存在
        election:   ## Leader 节点的选举配置
            startupGracePeriod: 15s         ##  leader节点选举等待的时间 (unit: second)
            membershipSampleInterval: 1s    ##  测试peer稳定性的时间间隔 (unit: second)
            leaderAliveThreshold: 10s       ##  pear 尝试进行选举的等待超时 (unit: second)
            leaderElectionDuration: 5s      ##  pear 宣布自己为Leader节点的等待时间 (unit: second)
 
        pvtData:        ##  这个我还真不知道干嘛的？谁知道告诉我
            pullRetryThreshold: 60s
            transientstoreMaxBlockRetention: 1000
            pushAckTimeout: 3s
            btlPullMargin: 10
 
    
    events:     ##  事件配置
        address: 0.0.0.0:7053       ##  本地服务监听地址 (默认在所有网络接口上进心监听，端口 7053)
        buffersize: 100             ##  最大缓冲消息数，超过则向缓冲中发送事件消息会被阻塞
        timeout: 10ms               ##  事件发送超时时间, 如果事件缓存已满, timeout < 0, 事件被丢弃; timeout > 0, 阻塞直到超时丢弃, timeout = 0, 阻塞直到发送出去
        timewindow: 15m             ##  允许peer和 客户端 时间不一致的最大时间差
 
        keepalive:      ##  客户端到peer间的事件心跳
            minInterval: 60s
        sendTimeout: 60s            ##  在GRPC流上向客户端发送事件的超时时间
 
 
    tls:        ##  tls配置
        
        enabled:  false             ##  是否开启 TLS，默认不开启TLS
        clientAuthRequired: false   ##  客户端连接到peer是否需要使用加密
        
        cert:   ##  证书密钥的位置, 各peer应该填写各自相应的路径
            file: tls/server.crt    ##  本服务的身份验证证书，公开可见，访问者通过该证书进行验证
        key:    
            file: tls/server.key    ##  本服务的签名私钥
        rootcert:   
            file: tls/ca.crt        ##  信任的根CA整数位置
        
        clientRootCAs:              ##  用于验证客户端证书的根证书颁发机构的集合
            files:
              - tls/ca.crt
        clientKey:      ##  当TLS密钥用于制作客户端连接。如果不设置，将使用而不是peer.tls.key.file
            file:
        clientCert:     ##  在进行客户端连接时用于TLS的X.509证书。 如果未设置，将使用peer.tls.cert.file
            file:
 
        serverhostoverride:         ##  是否制定进行TLS握手时的主机名称
    
    authentication:     ##  身份验证包含与验证客户端消息相关的配置参数
        
        timewindow: 15m         ##  客户端请求消息中指定的当前服务器时间与客户端时间之间的可接受差异
 
    fileSystemPath: /var/hyperledger/production     ##  peer数据存储位置(包括账本,状态数据库等)
 
   
    BCCSP:      ##  加密库配置 与Orderer 配置一样
        Default: SW             ##  使用软件加密方式 (默认 SW)
        SW:     
            Hash: SHA2          ##  Hash 算法类型，目前仅支持SHA2
            Security: 256       
          
            FileKeyStore:       ##  本地私钥文件路径，默认指向 <mapConfigPath>/keystore
                KeyStore:
        # Settings for the PKCS#11 crypto provider (i.e. when DEFAULT: PKCS11)
        PKCS11:     ##  设置 PKCS#11 加密算法 (默认PKCS11)
            Library:            ##  本地PKCS11依赖库  
 
            Label:              ##  token的标识
            Pin:                ##  使用Pin
            Hash:
            Security:
            FileKeyStore:
                KeyStore:
 
    mspConfigPath: msp          ##  msp 的本地路径
 
    localMspId: SampleOrg       ##  Peer 所关联的MSP 的ID
 
    client:        ##   cli 公共客户端配置选项
        connTimeout: 3s     ##  连接超时时间
 
    
    deliveryclient:     ## 交付服务配置
        reconnectTotalTimeThreshold: 3600s  ##  交付服务交付失败后尝试重连的时间
        connTimeout: 3s     ##  交付服务和 orderer节点的连接超时时间
        reConnectBackoffThreshold: 3600s    ##  设置连续重试之间的最大延迟
 
    localMspType: bccsp     ##  本地MSP类型 （默认为 BCCSP）
 
    profile:            ##  是否启用Go自带的profiling 支持进行调试
        enabled:     false
        listenAddress: 0.0.0.0:6060
 
    adminService:       ##  admin服务用于管理操作，例如控制日志模块严重性等。只有对等管理员才能使用该服务
       
    handlers:
        authFilters:
          -
            name: DefaultAuth
          -
            name: ExpirationCheck       ##  此筛选器检查身份x509证书过期 
        decorators:
          -
            name: DefaultDecorator
        endorsers:
          escc:
            name: DefaultEndorsement
            library:
        validators:
          vscc:
            name: DefaultValidation
            library:
    validatorPoolSize:                  ##  处理交易验证的并发数, 默认是CPU的核数
 
    discovery:      ##  客户端使用发现服务来查询有关peers的信息，例如 - 哪些peer已加入某个channel，最新的channel配置是什么，最重要的是 - 给定chaincode和channel，哪些可能的peer满足认可 policy
        enabled: true
        authCacheEnabled: true
        authCacheMaxSize: 1000
        authCachePurgeRetentionRatio: 0.75
        orgMembersAllowedAccess: false
###############################################################################
#
#    VM section 链码运行环境配置    目前主要支持 Docker容器
#
###############################################################################
vm:
 
    endpoint: unix:///var/run/docker.sock   ##  Docker Daemon 地址，默认是本地 套接字
 
    docker:
        tls:    ##  Docker Daemon 启用TLS时的相关证书配置, 包括信任的根CA证书、服务身份证书、签名私钥等等
            enabled: false
            ca:
                file: docker/ca.crt
            cert:
                file: docker/tls.crt
            key:
                file: docker/tls.key
 
        attachStdout: false     ##  是否启用绑定到标准输出，启用后 链码容器 的输出消息会绑定到标准输出，方便进行调试
 
        hostConfig:             ##  Docker 相关的主机配置，包括网络配置、日志、内存等等，这些配置在启动链码容器时进行使用
            NetworkMode: host
            Dns:
               # - 192.168.0.1
            LogConfig:
                Type: json-file
                Config:
                    max-size: "50m"
                    max-file: "5"
            Memory: 2147483648
 
###############################################################################
#
#    Chaincode section      链码相关配置
#
###############################################################################
chaincode:
 
    id:             ##  记录链码相关信息，包括路径、名称、版本等等，该信息会以标签形式写到链码容器
        path:
        name:
 
    builder: $(DOCKER_NS)/fabric-ccenv:latest       ##  通用的本地编译环境，是一个Docker 镜像
    pull: false     ##      
    golang:         ##  Go语言的链码部署生成镜像的基础Docker镜像
        runtime: $(BASE_DOCKER_NS)/fabric-baseos:$(ARCH)-$(BASE_VERSION)
        dynamicLink: false
    car:            ##  car格式的链码部署生成镜像的基础Docker 镜像
        runtime: $(BASE_DOCKER_NS)/fabric-baseos:$(ARCH)-$(BASE_VERSION)
    java:           ##  java语言的基础镜像
        Dockerfile:  |
            from $(DOCKER_NS)/fabric-javaenv:$(ARCH)-1.1.0
    node:           ##  node.js的基础镜像
        runtime: $(BASE_DOCKER_NS)/fabric-baseimage:$(ARCH)-$(BASE_VERSION)
 
    startuptimeout: 300s    ##  启动链码容器超时，等待超时时间后还没收到链码段的注册消息，则认为启动失败
 
    executetimeout: 30s     ##  invoke 和 initialize 命令执行超时时间
 
    deploytimeout:          ##  部署链码的命令执行超时时间
    mode: net               ##  执行链码的模式，dev: 允许本地直接运行链码，方便调试； net: 意味着在容器中运行链码
    keepalive: 0            ##  Peer 和链码之间的心跳超市时间， <= 0 意味着关闭
    system:                 ##  系统链码的相关配置 (系统链码白名单 ??)
        cscc: enable
        lscc: enable
        escc: enable
        vscc: enable
        qscc: enable
    systemPlugins:          ##  系统链码插件
    logging:                ##  链码容器日志相关配置
      level:  info
      shim:   warning
      format: '%{color}%{time:2006-01-02 15:04:05.000 MST} [%{module}] %{shortfunc} -> %{level:.4s} %{id:03x}%{color:reset} %{message}'
 
###############################################################################
#
#    Ledger section - ledger configuration encompases both the blockchain
#    and the state      账本相关配置
#
###############################################################################
ledger:
  blockchain:       ##  设置系统区块链的整体配置，【后面会被丢弃】
  state:            ##  状态DB的相关配置(包括 golevelDB、couchDB)、DN连接、查询最大返回记录数等
    stateDatabase: goleveldb    ##  stateDB的底层DB配置  (默认golevelDB)
    couchDBConfig:              ##  如果启用couchdb，配置连接信息 (goleveldb 不需要配置这些)
       couchDBAddress: 127.0.0.1:5984
       username:
      o prevent unintended users from discovering the password.
       password:
       maxRetries: 3    ##  运行时出错重试数
       maxRetriesOnStartup: 10  ##  启动时出错的重试数
       requestTimeout: 35s      ##  请求超时时间
       queryLimit: 10000        ##  每个查询最大返回数
       maxBatchUpdateSize: 1000 ##  批量更新最大记录数
       warmIndexesAfterNBlocks: 1
  history:      
    enableHistoryDatabase: true    ##  是否启用历史数据库，默认开启
 
###############################################################################
#
#    Metrics section    服务度量监控配置
#
#
###############################################################################
metrics:
        enabled: false      ##  是否开启监控服务
        reporter: statsd
        interval: 1s
        statsdReporter:
              address: 0.0.0.0:8125
              flushInterval: 2s
              flushBytes: 1432
        promReporter:       ##  prometheus 普罗米修斯服务监听地址
              listenAddress: 0.0.0.0:8080
```



# 解释

该文件就是 peer 节点的各项配置，其中主要包含了：

*  peer / 节点
*  vm / 链码运行环境
* chaincode / 链码相关
* ledger / 账本配置
* operations / 运维配置
* metrics / 服务度量监控配置

##  peer / 节点

peer部分是 Peer 服务的核心配置内容，包括 Peer 的基础服务部分、gossip 部分、event、tls、BCCSP 等相关配置信息

```YAML
peer:
    id: jdoe		# 指定节点ID
    networkId: dev	 # 指定网络ID
    listenAddress: 0.0.0.0:7051		#侦听本地网络接口上的地址。默认监听所有网络接口
    
    #侦听入站链码连接的端点。如果被注释，则选择侦听地址端口7052的对等点地址
    # chaincodeListenAddress: 0.0.0.0:7052    
    # 此peer的链码端点用于连接到peer。如果没有指定，则选择chaincodeListenAddress地址。
    # 如果没有指定chaincodeListenAddress，则从其中选择address 
    # chaincodeAddress: 0.0.0.0:705
    
    address: 0.0.0.0:7051	# 节点对外的服务地址
    addressAutoDetect: false	# 是否自动探测对外服务地址	
    gomaxprocs: -1	# 进程数限制，－1代表无限制
    
    # Peer服务与Client的设置
    keepalive:
    	# 指定客户机ping的最小间隔，如果客户端频繁发送ping，Peer服务器会自动断开
        minInterval: 60s	
        
        client: 	# 客户端与Peer的通信设置 
        	# 指定ping Peer节点的间隔时间，必须大于或等于 minInterval 的值        
            interval: 60s           
            timeout: 20s	# 在断开peer节点连接之前等待的响应时间
      
        deliveryClient:    # 客户端与Orderer节点的通信设置
        	# 指定ping orderer节点的间隔时间，必须大于或等于 minInterval 的值
            interval: 60s             
            timeout: 20s	# 在断开Orderer节点连接之前等待的响应时间

    gossip:   # gossip相关配置    
        bootstrap: 127.0.0.1:7051	# 启动后的初始节点
        useLeaderElection: true     # 是否指定使用选举方式产式Leader
        orgLeader: false	# 是否指定当前节点为Leader
        endpoint:      
        
        maxBlockCountToStore: 100  # 保存在内存中的最大区块     
        maxPropagationBurstLatency: 10ms	#消息连续推送之间的最大时间(超过则触发，转发给其它节点)
        maxPropagationBurstSize: 10	# 消息的最大存储数量，直到推送被触发 
        propagateIterations: 1    # 将消息推送到远程Peer节点的次数   
        propagatePeerNum: 3  # 选择推送消息到Peer节点的数量     
        pullInterval: 4s    # 拉取消息的时间间隔  
        pullPeerNum: 3      # 从指定数量的Peer节点拉取 
        requestStateInfoInterval: 4s # 确定从Peer节点提取状态信息消息的频率(单位:秒)             
        publishStateInfoInterval: 4s # 确定将状态信息消息推送到Peer节点的频率     
        stateInfoRetentionInterval:  # 状态信息的最长保存时间     
        publishCertPeriod: 10s      #  启动后包括证书的等待时间
        skipBlockVerification: false     # 是否应该跳过区块消息的验证   
        dialTimeout: 3s     # 拨号的超时时间     
        connTimeout: 2s     # 连接超时时间    
        recvBuffSize: 20    # 接收到消息的缓存区大小    
        sendBuffSize: 200	# 发送消息的缓冲区大小
        digestWaitTime: 1s  # 处理摘要数据的等待时间     
        requestWaitTime: 1500ms  	# 处理nonce之前等待的时间   
        responseWaitTime: 2s   # 终止拉取数据处理的等待时间    
        aliveTimeInterval: 5s      # 心跳检查间隔时间  
        aliveExpirationTimeout: 25s    # 心跳消息的超时时间    
        reconnectInterval: 25s       # 重新连接的间隔时间 
        externalEndpoint:	# 组织外的端点
        
        election:   # 选举Leader配置     
            startupGracePeriod: 15s       # 最长等待时间 
            membershipSampleInterval: 1s  # 检查稳定性的间隔时间     
            leaderAliveThreshold: 10s     # 进行选举的间隔时间
            leaderElectionDuration: 5s	# 声明自己为Leader的等待时间
            
        pvtData:	# 私有数据配置
        	# 尝试从peer节点中提取给定块对应的私有数据的最大持续时间
            pullRetryThreshold: 60s    
            # 当前分类帐在提交时的高度之间的最大差异
            transientstoreMaxBlockRetention: 1000            
            pushAckTimeout: 3s   # 等待每个对等方确认的最大时间         
            # 用作缓冲器；防止peer试图获取私有数据来自即将在接下来的N个块中被清除的对等节点
            btlPullMargin: 10	
    
    events:    
        address: 0.0.0.0:7053	# 指定事件服务的地址
        buffersize: 100	# 可以在不阻塞发送的情况下缓冲的事件总数
        # 将事件添加到一个完整的缓冲区时要阻塞多长时间
        # 如果小于0，直接丢弃
        # 如果等于0，事件被添加至缓冲区并发出
        # 如果大于0，超时还未发出则丢弃
        timeout: 10ms	
        # 在注册事件中指定的时间和客户端时间之间的差异
        timewindow: 15m	
        
        keepalive: # peer服务器与客户端的实时设置           
            minInterval: 60s	# 允许客户端向peer服务器发送ping的最小间隔时间

        sendTimeout: 60s	# GRPC向客户端发送事件的超时时间

    tls:	# TLS设置 
        enabled:  false   # 是否开启服务器端TLS    
        # 是否需要客户端证书（没有配置使用证书的客户端不能连接到对等点）
        clientAuthRequired: false	
        
        cert:	# TLS服务器的X.509证书
            file: tls/server.crt
       
        key:	# TLS服务器(需启用clientAuthEnabled的客户端)的签名私钥
            file: tls/server.key
       
        rootcert:	# 可信任的根CA证书
            file: tls/ca.crt
       
        clientRootCAs:	# 用于验证客户端证书的根证书
            files:
              - tls/ca.crt
       
        clientKey:	# 建立客户端连接时用于TLS的私钥。如果没有设置将使用peer.tls.key
            file:
       
        clientCert:	# 建立客户端连接时用于TLS的证书。如果没有设置将使用peer.tls.cert
            file:

    authentication:	# 与身份验证相关的配置
        timewindow: 15m	# 当前服务器时间与客户端请求消息中指定的客户端时间差异

    fileSystemPath: /var/hyperledger/production	# 文件存储路径

    BCCSP:	# 区块链加密实现
        Default: SW		# 设置SW为默认加密程序  
        SW:	  # SW加密配置（如果默认为SW）       
            Hash: SHA2		# 默认的哈希算法和安全级别
            Security: 256	# 
           
            FileKeyStore:	# 密钥存储位置
            	# 如果为空，默认为'mspConfigPath/keystore'
                KeyStore:
        
        PKCS11:  # PKCS11加密配置（如果默认为PKCS11）
            Library:	# PKCS11模块库位置           
            Label:	# 令牌Label
   
            Pin:
            Hash:
            Security:
            FileKeyStore:
                KeyStore:

	# MSP配置路径，peer根据此路径找到MSP本地配置
    mspConfigPath: msp
    localMspId: SampleOrg	#本地MSP的标识符

    client:	# CLI客户端配置选项
        connTimeout: 3s	# 连接超时

    deliveryclient:	# 订购服务相关的配置        
        reconnectTotalTimeThreshold: 3600s	# 尝试重新连接的总时间
        connTimeout: 3s	# 订购服务节点连接超时
        reConnectBackoffThreshold: 3600s	# 最大延迟时间

    localMspType: bccsp	# 本地MSP类型（默认情况下，是bccsp类型）

	# 仅在非生产环境中与Go分析工具一起使用。在生产中，它应该被禁用
    profile:
        enabled:     false
        listenAddress: 0.0.0.0:6060

	# 用于管理操作，如控制日志模块的严重程度等。只有对等管理员才能使用该服务
    adminService:

	# 定义处理程序可以过滤和自定义处理程序在对等点内传递的对象
    handlers:
        authFilters:
          -
            name: DefaultAuth
          -
            name: ExpirationCheck   
        decorators:
          -
            name: DefaultDecorator
        endorsers:
          escc:
            name: DefaultEndorsement
            library:
        validators:
          vscc:
            name: DefaultValidation
            library:

	# 并行执行事务验证的goroutines的数量（注意重写此值可能会对性能产生负面影响）
    validatorPoolSize:

	# 客户端使用发现服务查询关于对等点的信息
	# 例如——哪些同行加入了某个频道，最新消息是什么通道配置
	# 最重要的是——给定一个链码和通道，什么可能的对等点集满足背书政策
    discovery:
        enabled: true        
        authCacheEnabled: true        
        authCacheMaxSize: 1000       
        authCachePurgeRetentionRatio: 0.75       
        orgMembersAllowedAccess: false
```





##  vm / 链码运行环境

对链码运行环境的配置，目前主要支持 `Docker` 容器

```yaml
vm:
    endpoint: unix:///var/run/docker.sock	# vm管理系统的端点

    docker:	# 设置docker
        tls:
            enabled: false
            ca:
                file: docker/ca.crt
            cert:
                file: docker/tls.crt
            key:
                file: docker/tls.key

        attachStdout: false	# 启用/禁用链码容器中的标准out/err

		# 创建docker容器的参数
		# 使用用于集群的ipam和dns-server可以有效地创建容器设置容器的网络模式
		# 支持标准值是：“host”(默认)、“bridge”、“ipvlan”、“none”
		# Dns -供容器使用的Dns服务器列表
		#注:'Privileged'、'Binds'、'Links'和'PortBindings'属性不支持Docker主机配置，设置后将不使用
        hostConfig:
            NetworkMode: host
            Dns:
               # - 192.168.0.1
            LogConfig:
                Type: json-file
                Config:
                    max-size: "50m"
                    max-file: "5"
            Memory: 2147483648
```



## chaincode / 链码相关

与链码相关的配置

```yaml
chaincode:
    id:	
        path:
        name:
    # 通用构建器环境，适用于大多数链代码类型
    builder: $(DOCKER_NS)/fabric-ccenv:latest

	# 在用户链码实例化过程中启用/禁用基本docker镜像的拉取
    pull: false

    golang:	# golang的baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseos:$(ARCH)-$(BASE_VERSION)
        dynamicLink: false	# 是否动态链接golang链码

    car:
    	#　平台可能需要更多的扩展工具(JVM等)。目前，只能使用baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseos:$(ARCH)-$(BASE_VERSION)

    java:
    	# 用于Java链代码运行时，基于java:openjdk-8和加法编译器的镜像
        Dockerfile:  |
            from $(DOCKER_NS)/fabric-javaenv:$(ARCH)-1.1.0

    node:  
    	# js引擎在运行时，指定的baseimage（不是baseos）
        runtime: $(BASE_DOCKER_NS)/fabric-baseimage:$(ARCH)-$(BASE_VERSION)

    startuptimeout: 300s	# 启动超时时间
    executetimeout: 30s		# 调用和Init调用的超时持续时间
    mode: net	# 指定模式（dev、net两种）
    keepalive: 0	# Peer和链码之间的心跳超时，值小于或等于0会关闭

    system:	# 系统链码白名单
        cscc: enable
        lscc: enable
        escc: enable
        vscc: enable
        qscc: enable

    systemPlugins:	# 系统链码插件:
     
    logging:   # 链码容器的日志部分  
      level:  info
      shim:   warning
      format: '%{color}%{time:2006-01-02 15:04:05.000 MST} [%{module}] %{shortfunc} -> %{level:.4s} %{id:03x}%{color:reset} %{message}'
```

## ledger / 账本配置

分类帐本的配置信息。

```yaml
ledger:
  blockchain:

  state: 
    stateDatabase: goleveldb	# 指定默认的状态数据库
    couchDBConfig:	# 配置couchDB信息
       couchDBAddress: 127.0.0.1:5984	# 监听地址
       username:
       password:
       maxRetries: 3	# 重新尝试CouchDB错误的次数
       maxRetriesOnStartup: 10      #　对等启动期间对CouchDB错误的重试次数 
       requestTimeout: 35s      #　对等启动期间对CouchDB错误的重试次数 
       queryLimit: 10000     #　限制每个查询返回的记录数量  
       maxBatchUpdateSize: 1000      #　限制每个CouchDB批量更新的记录数量
       
       #　值为１时将在每次提交块后对进行索引
       # 增加值可以提高peer和CouchDB的写效率，但是可能会降低查询响应时间
       warmIndexesAfterNBlocks: 1	

  history:	
    enableHistoryDatabase: true		# 是否开启历史数据库
```

## operations / 运维配置

```YAML
operations:
    listenAddress: 127.0.0.1:9443  # 运维监控服务器的主机和端口

    tls:  # 运维服务端点的TLS配置
        enabled: false  # 是否允许tls

        # path to PEM encoded server certificate for the operations server
        cert:
            file:
        # path to PEM encoded server key for the operations server
        key:
            file:
        # most operations service endpoints require client authentication when TLS
        # is enabled. clientAuthRequired requires client certificate authentication
        # at the TLS layer to access all resources.
        clientAuthRequired: false

        # paths to PEM encoded ca certificates to trust for client authentication
        clientRootCAs:
            files: []
```



## metrics / 服务度量监控配置

```YAML
metrics:
    enabled: false	# 启用或禁用metrics服务器
    # 当启用metrics服务器时
    # 必须使用特定的metrics报告程序类型当前支持的类型:“statsd”、“prom”
    reporter: statsd
    interval: 1s	# 确定报告度量的频率

    statsdReporter:
          address: 0.0.0.0:8125	# 要连接的statsd服务器地址
          flushInterval: 2s	# 确定向statsd服务器推送指标的频率
          # 每个push metrics请求的最大字节数 #内部网推荐1432，互联网推荐512
          flushBytes: 1432	

    promReporter:
          listenAddress: 0.0.0.0:8080	# http服务器监听地址
```





# 项目代码

相较于模板文件`core.yaml`，修改部分如下：

* `peer.listenAddress `这的端口设置成了7061，并且会根据peer1\~4对应设成7061\~7064。这是本机部署多peer节点（单机部署多节点）的无奈之举，当多机部署时，建议一律改成7051。
  * `peer.address`, `peer.gossip.bootstrap`, `peer.gossip.externalEndpoint`等类似的端口都需要设置成7061

* `peer.chaincodeListenAddress` 这的端口设置成了7041，并且会根据peer1\~4对应设成7041\~7044，因为是单机部署多节点。但当多机部署时，建议一律改成7052。

* `peer.fileSystemPath` 是该节点ledger的存储位置。我这里的值是'../production/peer1'。在多机部署时，请改成'../production/peer1'
* `peer.tls` 中的cert, key, rootcert使用了相对路径，所以请不要移动该文件的位置，或使用变量替换这些路径。
* `peer.mspConfigPath` 使用了相对路径，所以请不要移动该文件的位置，或使用变量替换该路径。
* `peer.localMspId` 这个值在后面经常会用到，代表了这个节点的MSP ID。一切操作都需要这个ID无误。
* `operations.listenAddress` 虽然我们可能用不到，但是这个服务会启动。在单机部署时，会有端口冲突。我把Peer1\~4的这一项的端口分配给了9443\~9446。多机部署时，可以全部修改回9443
* 其他没有目前需要注意的地方。vm section配docker时要看，此外的几个大项我们用不着，其中的地址和端口通通忽视。



## core1.yaml

```YAML
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

###############################################################################
#
#    Peer section
#
###############################################################################
peer:

    # The Peer id is used for identifying this Peer instance.
    id: peer0.org1.dy

    # The networkId allows for logical seperation of networks
    networkId: dev

    # The Address at local network interface this Peer will listen on.
    # By default, it will listen on all network interfaces
    listenAddress: 0.0.0.0:7061

    # The endpoint this peer uses to listen for inbound chaincode connections.
    # If this is commented-out, the listen address is selected to be
    # the peer's address (see below) with port 7052

    # I change it to 7041 for local test.
    chaincodeListenAddress: 0.0.0.0:7041

    # The endpoint the chaincode for this peer uses to connect to the peer.
    # If this is not specified, the chaincodeListenAddress address is selected.
    # And if chaincodeListenAddress is not specified, address is selected from
    # peer listenAddress.
    # chaincodeAddress: 0.0.0.0:7041

    # When used as peer config, this represents the endpoint to other peers
    # in the same organization. For peers in other organization, see
    # gossip.externalEndpoint for more info.
    # When used as CLI config, this means the peer's endpoint to interact with
    address: peer0.org1.dy:7061

    # Whether the Peer should programmatically determine its address
    # This case is useful for docker containers.
    addressAutoDetect: false

    # Setting for runtime.GOMAXPROCS(n). If n < 1, it does not change the
    # current setting
    gomaxprocs: -1

    # Keepalive settings for peer server and clients
    keepalive:
        # MinInterval is the minimum permitted time between client pings.
        # If clients send pings more frequently, the peer server will
        # disconnect them
        minInterval: 60s
        # Client keepalive settings for communicating with other peer nodes
        client:
            # Interval is the time between pings to peer nodes.  This must
            # greater than or equal to the minInterval specified by peer
            # nodes
            interval: 60s
            # Timeout is the duration the client waits for a response from
            # peer nodes before closing the connection
            timeout: 20s
        # DeliveryClient keepalive settings for communication with ordering
        # nodes.
        deliveryClient:
            # Interval is the time between pings to ordering nodes.  This must
            # greater than or equal to the minInterval specified by ordering
            # nodes.
            interval: 60s
            # Timeout is the duration the client waits for a response from
            # ordering nodes before closing the connection
            timeout: 20s


    # Gossip related configuration
    gossip:
        # Bootstrap set to initialize gossip with.
        # This is a list of other peers that this peer reaches out to at startup.
        # Important: The endpoints here have to be endpoints of peers in the same
        # organization, because the peer would refuse connecting to these endpoints
        # unless they are in the same organization as the peer.
        bootstrap: peer0.org1.dy:7061

        # NOTE: orgLeader and useLeaderElection parameters are mutual exclusive.
        # Setting both to true would result in the termination of the peer
        # since this is undefined state. If the peers are configured with
        # useLeaderElection=false, make sure there is at least 1 peer in the
        # organization that its orgLeader is set to true.

        # Defines whenever peer will initialize dynamic algorithm for
        # "leader" selection, where leader is the peer to establish
        # connection with ordering service and use delivery protocol
        # to pull ledger blocks from ordering service. It is recommended to
        # use leader election for large networks of peers.
        useLeaderElection: true
        # Statically defines peer to be an organization "leader",
        # where this means that current peer will maintain connection
        # with ordering service and disseminate block across peers in
        # its own organization
        orgLeader: false

        # Interval for membershipTracker polling
        membershipTrackerInterval: 5s

        # Overrides the endpoint that the peer publishes to peers
        # in its organization. For peers in foreign organizations
        # see 'externalEndpoint'
        endpoint:
        # Maximum count of blocks stored in memory
        maxBlockCountToStore: 100
        # Max time between consecutive message pushes(unit: millisecond)
        maxPropagationBurstLatency: 10ms
        # Max number of messages stored until a push is triggered to remote peers
        maxPropagationBurstSize: 10
        # Number of times a message is pushed to remote peers
        propagateIterations: 1
        # Number of peers selected to push messages to
        propagatePeerNum: 3
        # Determines frequency of pull phases(unit: second)
        # Must be greater than digestWaitTime + responseWaitTime
        pullInterval: 4s
        # Number of peers to pull from
        pullPeerNum: 3
        # Determines frequency of pulling state info messages from peers(unit: second)
        requestStateInfoInterval: 4s
        # Determines frequency of pushing state info messages to peers(unit: second)
        publishStateInfoInterval: 4s
        # Maximum time a stateInfo message is kept until expired
        stateInfoRetentionInterval:
        # Time from startup certificates are included in Alive messages(unit: second)
        publishCertPeriod: 10s
        # Should we skip verifying block messages or not (currently not in use)
        skipBlockVerification: false
        # Dial timeout(unit: second)
        dialTimeout: 3s
        # Connection timeout(unit: second)
        connTimeout: 2s
        # Buffer size of received messages
        recvBuffSize: 20
        # Buffer size of sending messages
        sendBuffSize: 200
        # Time to wait before pull engine processes incoming digests (unit: second)
        # Should be slightly smaller than requestWaitTime
        digestWaitTime: 1s
        # Time to wait before pull engine removes incoming nonce (unit: milliseconds)
        # Should be slightly bigger than digestWaitTime
        requestWaitTime: 1500ms
        # Time to wait before pull engine ends pull (unit: second)
        responseWaitTime: 2s
        # Alive check interval(unit: second)
        aliveTimeInterval: 5s
        # Alive expiration timeout(unit: second)
        aliveExpirationTimeout: 25s
        # Reconnect interval(unit: second)
        reconnectInterval: 25s
        # This is an endpoint that is published to peers outside of the organization.
        # If this isn't set, the peer will not be known to other organizations.
        externalEndpoint: peer0.org1.dy:7061
        # Leader election service configuration
        election:
            # Longest time peer waits for stable membership during leader election startup (unit: second)
            startupGracePeriod: 15s
            # Interval gossip membership samples to check its stability (unit: second)
            membershipSampleInterval: 1s
            # Time passes since last declaration message before peer decides to perform leader election (unit: second)
            leaderAliveThreshold: 10s
            # Time between peer sends propose message and declares itself as a leader (sends declaration message) (unit: second)
            leaderElectionDuration: 5s

        pvtData:
            # pullRetryThreshold determines the maximum duration of time private data corresponding for a given block
            # would be attempted to be pulled from peers until the block would be committed without the private data
            pullRetryThreshold: 60s
            # As private data enters the transient store, it is associated with the peer's ledger's height at that time.
            # transientstoreMaxBlockRetention defines the maximum difference between the current ledger's height upon commit,
            # and the private data residing inside the transient store that is guaranteed not to be purged.
            # Private data is purged from the transient store when blocks with sequences that are multiples
            # of transientstoreMaxBlockRetention are committed.
            transientstoreMaxBlockRetention: 1000
            # pushAckTimeout is the maximum time to wait for an acknowledgement from each peer
            # at private data push at endorsement time.
            pushAckTimeout: 3s
            # Block to live pulling margin, used as a buffer
            # to prevent peer from trying to pull private data
            # from peers that is soon to be purged in next N blocks.
            # This helps a newly joined peer catch up to current
            # blockchain height quicker.
            btlPullMargin: 10
            # the process of reconciliation is done in an endless loop, while in each iteration reconciler tries to
            # pull from the other peers the most recent missing blocks with a maximum batch size limitation.
            # reconcileBatchSize determines the maximum batch size of missing private data that will be reconciled in a
            # single iteration.
            reconcileBatchSize: 10
            # reconcileSleepInterval determines the time reconciler sleeps from end of an iteration until the beginning
            # of the next reconciliation iteration.
            reconcileSleepInterval: 1m
            # reconciliationEnabled is a flag that indicates whether private data reconciliation is enable or not.
            reconciliationEnabled: true

        # Gossip state transfer related configuration
        state:
            # indicates whenever state transfer is enabled or not
            # default value is true, i.e. state transfer is active
            # and takes care to sync up missing blocks allowing
            # lagging peer to catch up to speed with rest network
            enabled: true
            # checkInterval interval to check whether peer is lagging behind enough to
            # request blocks via state transfer from another peer.
            checkInterval: 10s
            # responseTimeout amount of time to wait for state transfer response from
            # other peers
            responseTimeout: 3s
            # batchSize the number of blocks to request via state transfer from another peer
            batchSize: 10
            # blockBufferSize reflect the maximum distance between lowest and
            # highest block sequence number state buffer to avoid holes.
            # In order to ensure absence of the holes actual buffer size
            # is twice of this distance
            blockBufferSize: 100
            # maxRetries maximum number of re-tries to ask
            # for single state transfer request
            maxRetries: 3

    # TLS Settings
    # Note that peer-chaincode connections through chaincodeListenAddress is
    # not mutual TLS auth. See comments on chaincodeListenAddress for more info
    tls:
        # Require server-side TLS
        enabled:  false
        # Require client certificates / mutual TLS.
        # Note that clients that are not configured to use a certificate will
        # fail to connect to the peer.
        clientAuthRequired: false
        # X.509 certificate used for TLS server
        cert:
            file: crypto-config/peerOrganizations/org1.dy/peers/peer0.org1.dy/tls/server.crt
        # Private key used for TLS server (and client if clientAuthEnabled
        # is set to true
        key:
            file: crypto-config/peerOrganizations/org1.dy/peers/peer0.org1.dy/tls/server.key
        # Trusted root certificate chain for tls.cert
        rootcert:
            file: crypto-config/peerOrganizations/org1.dy/peers/peer0.org1.dy/tls/ca.crt
        # Set of root certificate authorities used to verify client certificates
        clientRootCAs:
            files:
              - tls/ca.crt
        # Private key used for TLS when making client connections.  If
        # not set, peer.tls.key.file will be used instead
        clientKey:
            file:
        # X.509 certificate used for TLS when making client connections.
        # If not set, peer.tls.cert.file will be used instead
        clientCert:
            file:

    # Authentication contains configuration parameters related to authenticating
    # client messages
    authentication:
        # the acceptable difference between the current server time and the
        # client's time as specified in a client request message
        timewindow: 15m

    # Path on the file system where peer will store data (eg ledger). This
    # location must be access control protected to prevent unintended
    # modification that might corrupt the peer operations.
    fileSystemPath: ../peer1/production

    # BCCSP (Blockchain crypto provider): Select which crypto implementation or
    # library to use
    BCCSP:
        Default: SW
        # Settings for the SW crypto provider (i.e. when DEFAULT: SW)
        SW:
            # TODO: The default Hash and Security level needs refactoring to be
            # fully configurable. Changing these defaults requires coordination
            # SHA2 is hardcoded in several places, not only BCCSP
            Hash: SHA2
            Security: 256
            # Location of Key Store
            FileKeyStore:
                # If "", defaults to 'mspConfigPath'/keystore
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

    # Path on the file system where peer will find MSP local configurations
    mspConfigPath: crypto-config/peerOrganizations/org1.dy/peers/peer0.org1.dy/msp

    # Identifier of the local MSP
    # ----!!!!IMPORTANT!!!-!!!IMPORTANT!!!-!!!IMPORTANT!!!!----
    # Deployers need to change the value of the localMspId string.
    # In particular, the name of the local MSP ID of a peer needs
    # to match the name of one of the MSPs in each of the channel
    # that this peer is a member of. Otherwise this peer's messages
    # will not be identified as valid by other nodes.
    localMspId: Org1MSP

    # CLI common client config options
    client:
        # connection timeout
        connTimeout: 3s

    # Delivery service related config
    deliveryclient:
        # It sets the total time the delivery service may spend in reconnection
        # attempts until its retry logic gives up and returns an error
        reconnectTotalTimeThreshold: 3600s

        # It sets the delivery service <-> ordering service node connection timeout
        connTimeout: 3s

        # It sets the delivery service maximal delay between consecutive retries
        reConnectBackoffThreshold: 3600s

    # Type for the local MSP - by default it's of type bccsp
    localMspType: bccsp

    # Used with Go profiling tools only in none production environment. In
    # production, it should be disabled (eg enabled: false)
    profile:
        enabled:     false
        listenAddress: 0.0.0.0:6060

    # The admin service is used for administrative operations such as
    # control over logger levels, etc.
    # Only peer administrators can use the service.
    adminService:
        # The interface and port on which the admin server will listen on.
        # If this is commented out, or the port number is equal to the port
        # of the peer listen address - the admin service is attached to the
        # peer's service (defaults to 7051).
        #listenAddress: 0.0.0.0:7055

    # Handlers defines custom handlers that can filter and mutate
    # objects passing within the peer, such as:
    #   Auth filter - reject or forward proposals from clients
    #   Decorators  - append or mutate the chaincode input passed to the chaincode
    #   Endorsers   - Custom signing over proposal response payload and its mutation
    # Valid handler definition contains:
    #   - A name which is a factory method name defined in
    #     core/handlers/library/library.go for statically compiled handlers
    #   - library path to shared object binary for pluggable filters
    # Auth filters and decorators are chained and executed in the order that
    # they are defined. For example:
    # authFilters:
    #   -
    #     name: FilterOne
    #     library: /opt/lib/filter.so
    #   -
    #     name: FilterTwo
    # decorators:
    #   -
    #     name: DecoratorOne
    #   -
    #     name: DecoratorTwo
    #     library: /opt/lib/decorator.so
    # Endorsers are configured as a map that its keys are the endorsement system chaincodes that are being overridden.
    # Below is an example that overrides the default ESCC and uses an endorsement plugin that has the same functionality
    # as the default ESCC.
    # If the 'library' property is missing, the name is used as the constructor method in the builtin library similar
    # to auth filters and decorators.
    # endorsers:
    #   escc:
    #     name: DefaultESCC
    #     library: /etc/hyperledger/fabric/plugin/escc.so
    handlers:
        authFilters:
          -
            name: DefaultAuth
          -
            name: ExpirationCheck    # This filter checks identity x509 certificate expiration
        decorators:
          -
            name: DefaultDecorator
        endorsers:
          escc:
            name: DefaultEndorsement
            library:
        validators:
          vscc:
            name: DefaultValidation
            library:

    #    library: /etc/hyperledger/fabric/plugin/escc.so
    # Number of goroutines that will execute transaction validation in parallel.
    # By default, the peer chooses the number of CPUs on the machine. Set this
    # variable to override that choice.
    # NOTE: overriding this value might negatively influence the performance of
    # the peer so please change this value only if you know what you're doing
    validatorPoolSize:

    # The discovery service is used by clients to query information about peers,
    # such as - which peers have joined a certain channel, what is the latest
    # channel config, and most importantly - given a chaincode and a channel,
    # what possible sets of peers satisfy the endorsement policy.
    discovery:
        enabled: true
        # Whether the authentication cache is enabled or not.
        authCacheEnabled: true
        # The maximum size of the cache, after which a purge takes place
        authCacheMaxSize: 1000
        # The proportion (0 to 1) of entries that remain in the cache after the cache is purged due to overpopulation
        authCachePurgeRetentionRatio: 0.75
        # Whether to allow non-admins to perform non channel scoped queries.
        # When this is false, it means that only peer admins can perform non channel scoped queries.
        orgMembersAllowedAccess: false
###############################################################################
#
#    VM section
#
###############################################################################
vm:

    # Endpoint of the vm management system.  For docker can be one of the following in general
    # unix:///var/run/docker.sock
    # http://localhost:2375
    # https://localhost:2376
    endpoint: unix:///var/run/docker.sock

    # settings for docker vms
    docker:
        tls:
            enabled: false
            ca:
                file: docker/ca.crt
            cert:
                file: docker/tls.crt
            key:
                file: docker/tls.key

        # Enables/disables the standard out/err from chaincode containers for
        # debugging purposes
        attachStdout: true

        # Parameters on creating docker container.
        # Container may be efficiently created using ipam & dns-server for cluster
        # NetworkMode - sets the networking mode for the container. Supported
        # standard values are: `host`(default),`bridge`,`ipvlan`,`none`.
        # Dns - a list of DNS servers for the container to use.
        # Note:  `Privileged` `Binds` `Links` and `PortBindings` properties of
        # Docker Host Config are not supported and will not be used if set.
        # LogConfig - sets the logging driver (Type) and related options
        # (Config) for Docker. For more info,
        # https://docs.docker.com/engine/admin/logging/overview/
        # Note: Set LogConfig using Environment Variables is not supported.
        hostConfig:
            NetworkMode: mynet
            Dns:
               # - 192.168.0.1
            LogConfig:
                Type: json-file
                Config:
                    max-size: "50m"
                    max-file: "5"
            Memory: 2147483648

###############################################################################
#
#    Chaincode section
#
###############################################################################
chaincode:

    # The id is used by the Chaincode stub to register the executing Chaincode
    # ID with the Peer and is generally supplied through ENV variables
    # the `path` form of ID is provided when installing the chaincode.
    # The `name` is used for all other requests and can be any string.
    id:
        path:
        name:

    # Generic builder environment, suitable for most chaincode types
    builder: $(DOCKER_NS)/fabric-ccenv:latest

    # Enables/disables force pulling of the base docker images (listed below)
    # during user chaincode instantiation.
    # Useful when using moving image tags (such as :latest)
    pull: false

    golang:
        # golang will never need more than baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseos:$(ARCH)-$(BASE_VERSION)

        # whether or not golang chaincode should be linked dynamically
        dynamicLink: false

    car:
        # car may need more facilities (JVM, etc) in the future as the catalog
        # of platforms are expanded.  For now, we can just use baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseos:$(ARCH)-$(BASE_VERSION)

    java:
        # This is an image based on java:openjdk-8 with addition compiler
        # tools added for java shim layer packaging.
        # This image is packed with shim layer libraries that are necessary
        # for Java chaincode runtime.
        runtime: $(DOCKER_NS)/fabric-javaenv:$(ARCH)-$(PROJECT_VERSION)

    node:
        # need node.js engine at runtime, currently available in baseimage
        # but not in baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseimage:$(ARCH)-$(BASE_VERSION)

    # Timeout duration for starting up a container and waiting for Register
    # to come through. 1sec should be plenty for chaincode unit tests
    startuptimeout: 300s

    # Timeout duration for Invoke and Init calls to prevent runaway.
    # This timeout is used by all chaincodes in all the channels, including
    # system chaincodes.
    # Note that during Invoke, if the image is not available (e.g. being
    # cleaned up when in development environment), the peer will automatically
    # build the image, which might take more time. In production environment,
    # the chaincode image is unlikely to be deleted, so the timeout could be
    # reduced accordingly.
    executetimeout: 30s

    # There are 2 modes: "dev" and "net".
    # In dev mode, user runs the chaincode after starting peer from
    # command line on local machine.
    # In net mode, peer will run chaincode in a docker container.
    mode: net

    # keepalive in seconds. In situations where the communiction goes through a
    # proxy that does not support keep-alive, this parameter will maintain connection
    # between peer and chaincode.
    # A value <= 0 turns keepalive off
    keepalive: 0

    # system chaincodes whitelist. To add system chaincode "myscc" to the
    # whitelist, add "myscc: enable" to the list below, and register in
    # chaincode/importsysccs.go
    system:
        cscc: enable
        lscc: enable
        escc: enable
        vscc: enable
        qscc: enable

    # System chaincode plugins:
    # System chaincodes can be loaded as shared objects compiled as Go plugins.
    # See examples/plugins/scc for an example.
    # Plugins must be white listed in the chaincode.system section above.
    systemPlugins:
      # example configuration:
      # - enabled: true
      #   name: myscc
      #   path: /opt/lib/myscc.so
      #   invokableExternal: true
      #   invokableCC2CC: true

    # Logging section for the chaincode container
    logging:
      # Default level for all loggers within the chaincode container
      level:  info
      # Override default level for the 'shim' logger
      shim:   warning
      # Format for the chaincode container logs
      format: '%{color}%{time:2006-01-02 15:04:05.000 MST} [%{module}] %{shortfunc} -> %{level:.4s} %{id:03x}%{color:reset} %{message}'

###############################################################################
#
#    Ledger section - ledger configuration encompases both the blockchain
#    and the state
#
###############################################################################
ledger:

  blockchain:

  state:
    # stateDatabase - options are "goleveldb", "CouchDB"
    # goleveldb - default state database stored in goleveldb.
    # CouchDB - store state database in CouchDB
    stateDatabase: goleveldb
    # Limit on the number of records to return per query
    totalQueryLimit: 100000
    couchDBConfig:
       # It is recommended to run CouchDB on the same server as the peer, and
       # not map the CouchDB container port to a server port in docker-compose.
       # Otherwise proper security must be provided on the connection between
       # CouchDB client (on the peer) and server.
       couchDBAddress: 127.0.0.1:5984
       # This username must have read and write authority on CouchDB
       username:
       # The password is recommended to pass as an environment variable
       # during start up (eg CORE_LEDGER_STATE_COUCHDBCONFIG_PASSWORD).
       # If it is stored here, the file must be access control protected
       # to prevent unintended users from discovering the password.
       password:
       # Number of retries for CouchDB errors
       maxRetries: 3
       # Number of retries for CouchDB errors during peer startup
       maxRetriesOnStartup: 12
       # CouchDB request timeout (unit: duration, e.g. 20s)
       requestTimeout: 35s
       # Limit on the number of records per each CouchDB query
       # Note that chaincode queries are only bound by totalQueryLimit.
       # Internally the chaincode may execute multiple CouchDB queries,
       # each of size internalQueryLimit.
       internalQueryLimit: 1000
       # Limit on the number of records per CouchDB bulk update batch
       maxBatchUpdateSize: 1000
       # Warm indexes after every N blocks.
       # This option warms any indexes that have been
       # deployed to CouchDB after every N blocks.
       # A value of 1 will warm indexes after every block commit,
       # to ensure fast selector queries.
       # Increasing the value may improve write efficiency of peer and CouchDB,
       # but may degrade query response time.
       warmIndexesAfterNBlocks: 1
       # Create the _global_changes system database
       # This is optional.  Creating the global changes database will require
       # additional system resources to track changes and maintain the database
       createGlobalChangesDB: false

  history:
    # enableHistoryDatabase - options are true or false
    # Indicates if the history of key updates should be stored.
    # All history 'index' will be stored in goleveldb, regardless if using
    # CouchDB or alternate database for the state.
    enableHistoryDatabase: true

###############################################################################
#
#    Operations section
#
###############################################################################
operations:
    # host and port for the operations server
    listenAddress: 0.0.0.0:9443

    # TLS configuration for the operations endpoint
    tls:
        # TLS enabled
        enabled: false

        # path to PEM encoded server certificate for the operations server
        cert:
            file:

        # path to PEM encoded server key for the operations server
        key:
            file:

        # most operations service endpoints require client authentication when TLS
        # is enabled. clientAuthRequired requires client certificate authentication
        # at the TLS layer to access all resources.
        clientAuthRequired: false

        # paths to PEM encoded ca certificates to trust for client authentication
        clientRootCAs:
            files: []

###############################################################################
#
#    Metrics section
#
###############################################################################
metrics:
    # metrics provider is one of statsd, prometheus, or disabled
    provider: disabled

    # statsd configuration
    statsd:
        # network type: tcp or udp
        network: udp

        # statsd server address
        address: 127.0.0.1:8125

        # the interval at which locally cached counters and gauges are pushed
        # to statsd; timings are pushed immediately
        writeInterval: 10s

        # prefix is prepended to all emitted statsd metrics
        prefix:

```

## core2.yaml

```YAML
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

###############################################################################
#
#    Peer section
#
###############################################################################
peer:

    # The Peer id is used for identifying this Peer instance.
    id: peer0.org2.dy

    # The networkId allows for logical seperation of networks
    networkId: dev

    # The Address at local network interface this Peer will listen on.
    # By default, it will listen on all network interfaces
    listenAddress: 0.0.0.0:7062

    # The endpoint this peer uses to listen for inbound chaincode connections.
    # If this is commented-out, the listen address is selected to be
    # the peer's address (see below) with port 7052

    # I change it to 7041 for local test.
    chaincodeListenAddress: 0.0.0.0:7042

    # The endpoint the chaincode for this peer uses to connect to the peer.
    # If this is not specified, the chaincodeListenAddress address is selected.
    # And if chaincodeListenAddress is not specified, address is selected from
    # peer listenAddress.
    # chaincodeAddress: 0.0.0.0:7041

    # When used as peer config, this represents the endpoint to other peers
    # in the same organization. For peers in other organization, see
    # gossip.externalEndpoint for more info.
    # When used as CLI config, this means the peer's endpoint to interact with
    address: peer0.org2.dy:7062

    # Whether the Peer should programmatically determine its address
    # This case is useful for docker containers.
    addressAutoDetect: false

    # Setting for runtime.GOMAXPROCS(n). If n < 1, it does not change the
    # current setting
    gomaxprocs: -1

    # Keepalive settings for peer server and clients
    keepalive:
        # MinInterval is the minimum permitted time between client pings.
        # If clients send pings more frequently, the peer server will
        # disconnect them
        minInterval: 60s
        # Client keepalive settings for communicating with other peer nodes
        client:
            # Interval is the time between pings to peer nodes.  This must
            # greater than or equal to the minInterval specified by peer
            # nodes
            interval: 60s
            # Timeout is the duration the client waits for a response from
            # peer nodes before closing the connection
            timeout: 20s
        # DeliveryClient keepalive settings for communication with ordering
        # nodes.
        deliveryClient:
            # Interval is the time between pings to ordering nodes.  This must
            # greater than or equal to the minInterval specified by ordering
            # nodes.
            interval: 60s
            # Timeout is the duration the client waits for a response from
            # ordering nodes before closing the connection
            timeout: 20s


    # Gossip related configuration
    gossip:
        # Bootstrap set to initialize gossip with.
        # This is a list of other peers that this peer reaches out to at startup.
        # Important: The endpoints here have to be endpoints of peers in the same
        # organization, because the peer would refuse connecting to these endpoints
        # unless they are in the same organization as the peer.
        bootstrap: peer0.org2.dy:7062

        # NOTE: orgLeader and useLeaderElection parameters are mutual exclusive.
        # Setting both to true would result in the termination of the peer
        # since this is undefined state. If the peers are configured with
        # useLeaderElection=false, make sure there is at least 1 peer in the
        # organization that its orgLeader is set to true.

        # Defines whenever peer will initialize dynamic algorithm for
        # "leader" selection, where leader is the peer to establish
        # connection with ordering service and use delivery protocol
        # to pull ledger blocks from ordering service. It is recommended to
        # use leader election for large networks of peers.
        useLeaderElection: true
        # Statically defines peer to be an organization "leader",
        # where this means that current peer will maintain connection
        # with ordering service and disseminate block across peers in
        # its own organization
        orgLeader: false

        # Interval for membershipTracker polling
        membershipTrackerInterval: 5s

        # Overrides the endpoint that the peer publishes to peers
        # in its organization. For peers in foreign organizations
        # see 'externalEndpoint'
        endpoint:
        # Maximum count of blocks stored in memory
        maxBlockCountToStore: 100
        # Max time between consecutive message pushes(unit: millisecond)
        maxPropagationBurstLatency: 10ms
        # Max number of messages stored until a push is triggered to remote peers
        maxPropagationBurstSize: 10
        # Number of times a message is pushed to remote peers
        propagateIterations: 1
        # Number of peers selected to push messages to
        propagatePeerNum: 3
        # Determines frequency of pull phases(unit: second)
        # Must be greater than digestWaitTime + responseWaitTime
        pullInterval: 4s
        # Number of peers to pull from
        pullPeerNum: 3
        # Determines frequency of pulling state info messages from peers(unit: second)
        requestStateInfoInterval: 4s
        # Determines frequency of pushing state info messages to peers(unit: second)
        publishStateInfoInterval: 4s
        # Maximum time a stateInfo message is kept until expired
        stateInfoRetentionInterval:
        # Time from startup certificates are included in Alive messages(unit: second)
        publishCertPeriod: 10s
        # Should we skip verifying block messages or not (currently not in use)
        skipBlockVerification: false
        # Dial timeout(unit: second)
        dialTimeout: 3s
        # Connection timeout(unit: second)
        connTimeout: 2s
        # Buffer size of received messages
        recvBuffSize: 20
        # Buffer size of sending messages
        sendBuffSize: 200
        # Time to wait before pull engine processes incoming digests (unit: second)
        # Should be slightly smaller than requestWaitTime
        digestWaitTime: 1s
        # Time to wait before pull engine removes incoming nonce (unit: milliseconds)
        # Should be slightly bigger than digestWaitTime
        requestWaitTime: 1500ms
        # Time to wait before pull engine ends pull (unit: second)
        responseWaitTime: 2s
        # Alive check interval(unit: second)
        aliveTimeInterval: 5s
        # Alive expiration timeout(unit: second)
        aliveExpirationTimeout: 25s
        # Reconnect interval(unit: second)
        reconnectInterval: 25s
        # This is an endpoint that is published to peers outside of the organization.
        # If this isn't set, the peer will not be known to other organizations.
        externalEndpoint: peer0.org2.dy:7062
        # Leader election service configuration
        election:
            # Longest time peer waits for stable membership during leader election startup (unit: second)
            startupGracePeriod: 15s
            # Interval gossip membership samples to check its stability (unit: second)
            membershipSampleInterval: 1s
            # Time passes since last declaration message before peer decides to perform leader election (unit: second)
            leaderAliveThreshold: 10s
            # Time between peer sends propose message and declares itself as a leader (sends declaration message) (unit: second)
            leaderElectionDuration: 5s

        pvtData:
            # pullRetryThreshold determines the maximum duration of time private data corresponding for a given block
            # would be attempted to be pulled from peers until the block would be committed without the private data
            pullRetryThreshold: 60s
            # As private data enters the transient store, it is associated with the peer's ledger's height at that time.
            # transientstoreMaxBlockRetention defines the maximum difference between the current ledger's height upon commit,
            # and the private data residing inside the transient store that is guaranteed not to be purged.
            # Private data is purged from the transient store when blocks with sequences that are multiples
            # of transientstoreMaxBlockRetention are committed.
            transientstoreMaxBlockRetention: 1000
            # pushAckTimeout is the maximum time to wait for an acknowledgement from each peer
            # at private data push at endorsement time.
            pushAckTimeout: 3s
            # Block to live pulling margin, used as a buffer
            # to prevent peer from trying to pull private data
            # from peers that is soon to be purged in next N blocks.
            # This helps a newly joined peer catch up to current
            # blockchain height quicker.
            btlPullMargin: 10
            # the process of reconciliation is done in an endless loop, while in each iteration reconciler tries to
            # pull from the other peers the most recent missing blocks with a maximum batch size limitation.
            # reconcileBatchSize determines the maximum batch size of missing private data that will be reconciled in a
            # single iteration.
            reconcileBatchSize: 10
            # reconcileSleepInterval determines the time reconciler sleeps from end of an iteration until the beginning
            # of the next reconciliation iteration.
            reconcileSleepInterval: 1m
            # reconciliationEnabled is a flag that indicates whether private data reconciliation is enable or not.
            reconciliationEnabled: true

        # Gossip state transfer related configuration
        state:
            # indicates whenever state transfer is enabled or not
            # default value is true, i.e. state transfer is active
            # and takes care to sync up missing blocks allowing
            # lagging peer to catch up to speed with rest network
            enabled: true
            # checkInterval interval to check whether peer is lagging behind enough to
            # request blocks via state transfer from another peer.
            checkInterval: 10s
            # responseTimeout amount of time to wait for state transfer response from
            # other peers
            responseTimeout: 3s
            # batchSize the number of blocks to request via state transfer from another peer
            batchSize: 10
            # blockBufferSize reflect the maximum distance between lowest and
            # highest block sequence number state buffer to avoid holes.
            # In order to ensure absence of the holes actual buffer size
            # is twice of this distance
            blockBufferSize: 100
            # maxRetries maximum number of re-tries to ask
            # for single state transfer request
            maxRetries: 3

    # TLS Settings
    # Note that peer-chaincode connections through chaincodeListenAddress is
    # not mutual TLS auth. See comments on chaincodeListenAddress for more info
    tls:
        # Require server-side TLS
        enabled:  false
        # Require client certificates / mutual TLS.
        # Note that clients that are not configured to use a certificate will
        # fail to connect to the peer.
        clientAuthRequired: false
        # X.509 certificate used for TLS server
        cert:
            file: crypto-config/peerOrganizations/org2.dy/peers/peer0.org2.dy/tls/server.crt
        # Private key used for TLS server (and client if clientAuthEnabled
        # is set to true
        key:
            file: crypto-config/peerOrganizations/org2.dy/peers/peer0.org2.dy/tls/server.key
        # Trusted root certificate chain for tls.cert
        rootcert:
            file: crypto-config/peerOrganizations/org2.dy/peers/peer0.org2.dy/tls/ca.crt
        # Set of root certificate authorities used to verify client certificates
        clientRootCAs:
            files:
              - tls/ca.crt
        # Private key used for TLS when making client connections.  If
        # not set, peer.tls.key.file will be used instead
        clientKey:
            file:
        # X.509 certificate used for TLS when making client connections.
        # If not set, peer.tls.cert.file will be used instead
        clientCert:
            file:

    # Authentication contains configuration parameters related to authenticating
    # client messages
    authentication:
        # the acceptable difference between the current server time and the
        # client's time as specified in a client request message
        timewindow: 15m

    # Path on the file system where peer will store data (eg ledger). This
    # location must be access control protected to prevent unintended
    # modification that might corrupt the peer operations.
    fileSystemPath: ../peer2/production

    # BCCSP (Blockchain crypto provider): Select which crypto implementation or
    # library to use
    BCCSP:
        Default: SW
        # Settings for the SW crypto provider (i.e. when DEFAULT: SW)
        SW:
            # TODO: The default Hash and Security level needs refactoring to be
            # fully configurable. Changing these defaults requires coordination
            # SHA2 is hardcoded in several places, not only BCCSP
            Hash: SHA2
            Security: 256
            # Location of Key Store
            FileKeyStore:
                # If "", defaults to 'mspConfigPath'/keystore
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

    # Path on the file system where peer will find MSP local configurations
    mspConfigPath: crypto-config/peerOrganizations/org2.dy/peers/peer0.org2.dy/msp

    # Identifier of the local MSP
    # ----!!!!IMPORTANT!!!-!!!IMPORTANT!!!-!!!IMPORTANT!!!!----
    # Deployers need to change the value of the localMspId string.
    # In particular, the name of the local MSP ID of a peer needs
    # to match the name of one of the MSPs in each of the channel
    # that this peer is a member of. Otherwise this peer's messages
    # will not be identified as valid by other nodes.
    localMspId: Org2MSP

    # CLI common client config options
    client:
        # connection timeout
        connTimeout: 3s

    # Delivery service related config
    deliveryclient:
        # It sets the total time the delivery service may spend in reconnection
        # attempts until its retry logic gives up and returns an error
        reconnectTotalTimeThreshold: 3600s

        # It sets the delivery service <-> ordering service node connection timeout
        connTimeout: 3s

        # It sets the delivery service maximal delay between consecutive retries
        reConnectBackoffThreshold: 3600s

    # Type for the local MSP - by default it's of type bccsp
    localMspType: bccsp

    # Used with Go profiling tools only in none production environment. In
    # production, it should be disabled (eg enabled: false)
    profile:
        enabled:     false
        listenAddress: 0.0.0.0:6060

    # The admin service is used for administrative operations such as
    # control over logger levels, etc.
    # Only peer administrators can use the service.
    adminService:
        # The interface and port on which the admin server will listen on.
        # If this is commented out, or the port number is equal to the port
        # of the peer listen address - the admin service is attached to the
        # peer's service (defaults to 7051).
        #listenAddress: 0.0.0.0:7055

    # Handlers defines custom handlers that can filter and mutate
    # objects passing within the peer, such as:
    #   Auth filter - reject or forward proposals from clients
    #   Decorators  - append or mutate the chaincode input passed to the chaincode
    #   Endorsers   - Custom signing over proposal response payload and its mutation
    # Valid handler definition contains:
    #   - A name which is a factory method name defined in
    #     core/handlers/library/library.go for statically compiled handlers
    #   - library path to shared object binary for pluggable filters
    # Auth filters and decorators are chained and executed in the order that
    # they are defined. For example:
    # authFilters:
    #   -
    #     name: FilterOne
    #     library: /opt/lib/filter.so
    #   -
    #     name: FilterTwo
    # decorators:
    #   -
    #     name: DecoratorOne
    #   -
    #     name: DecoratorTwo
    #     library: /opt/lib/decorator.so
    # Endorsers are configured as a map that its keys are the endorsement system chaincodes that are being overridden.
    # Below is an example that overrides the default ESCC and uses an endorsement plugin that has the same functionality
    # as the default ESCC.
    # If the 'library' property is missing, the name is used as the constructor method in the builtin library similar
    # to auth filters and decorators.
    # endorsers:
    #   escc:
    #     name: DefaultESCC
    #     library: /etc/hyperledger/fabric/plugin/escc.so
    handlers:
        authFilters:
          -
            name: DefaultAuth
          -
            name: ExpirationCheck    # This filter checks identity x509 certificate expiration
        decorators:
          -
            name: DefaultDecorator
        endorsers:
          escc:
            name: DefaultEndorsement
            library:
        validators:
          vscc:
            name: DefaultValidation
            library:

    #    library: /etc/hyperledger/fabric/plugin/escc.so
    # Number of goroutines that will execute transaction validation in parallel.
    # By default, the peer chooses the number of CPUs on the machine. Set this
    # variable to override that choice.
    # NOTE: overriding this value might negatively influence the performance of
    # the peer so please change this value only if you know what you're doing
    validatorPoolSize:

    # The discovery service is used by clients to query information about peers,
    # such as - which peers have joined a certain channel, what is the latest
    # channel config, and most importantly - given a chaincode and a channel,
    # what possible sets of peers satisfy the endorsement policy.
    discovery:
        enabled: true
        # Whether the authentication cache is enabled or not.
        authCacheEnabled: true
        # The maximum size of the cache, after which a purge takes place
        authCacheMaxSize: 1000
        # The proportion (0 to 1) of entries that remain in the cache after the cache is purged due to overpopulation
        authCachePurgeRetentionRatio: 0.75
        # Whether to allow non-admins to perform non channel scoped queries.
        # When this is false, it means that only peer admins can perform non channel scoped queries.
        orgMembersAllowedAccess: false
###############################################################################
#
#    VM section
#
###############################################################################
vm:

    # Endpoint of the vm management system.  For docker can be one of the following in general
    # unix:///var/run/docker.sock
    # http://localhost:2375
    # https://localhost:2376
    endpoint: unix:///var/run/docker.sock

    # settings for docker vms
    docker:
        tls:
            enabled: false
            ca:
                file: docker/ca.crt
            cert:
                file: docker/tls.crt
            key:
                file: docker/tls.key

        # Enables/disables the standard out/err from chaincode containers for
        # debugging purposes
        attachStdout: true

        # Parameters on creating docker container.
        # Container may be efficiently created using ipam & dns-server for cluster
        # NetworkMode - sets the networking mode for the container. Supported
        # standard values are: `host`(default),`bridge`,`ipvlan`,`none`.
        # Dns - a list of DNS servers for the container to use.
        # Note:  `Privileged` `Binds` `Links` and `PortBindings` properties of
        # Docker Host Config are not supported and will not be used if set.
        # LogConfig - sets the logging driver (Type) and related options
        # (Config) for Docker. For more info,
        # https://docs.docker.com/engine/admin/logging/overview/
        # Note: Set LogConfig using Environment Variables is not supported.
        hostConfig:
            NetworkMode: mynet
            Dns:
               # - 192.168.0.1
            LogConfig:
                Type: json-file
                Config:
                    max-size: "50m"
                    max-file: "5"
            Memory: 2147483648

###############################################################################
#
#    Chaincode section
#
###############################################################################
chaincode:

    # The id is used by the Chaincode stub to register the executing Chaincode
    # ID with the Peer and is generally supplied through ENV variables
    # the `path` form of ID is provided when installing the chaincode.
    # The `name` is used for all other requests and can be any string.
    id:
        path:
        name:

    # Generic builder environment, suitable for most chaincode types
    builder: $(DOCKER_NS)/fabric-ccenv:latest

    # Enables/disables force pulling of the base docker images (listed below)
    # during user chaincode instantiation.
    # Useful when using moving image tags (such as :latest)
    pull: false

    golang:
        # golang will never need more than baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseos:$(ARCH)-$(BASE_VERSION)

        # whether or not golang chaincode should be linked dynamically
        dynamicLink: false

    car:
        # car may need more facilities (JVM, etc) in the future as the catalog
        # of platforms are expanded.  For now, we can just use baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseos:$(ARCH)-$(BASE_VERSION)

    java:
        # This is an image based on java:openjdk-8 with addition compiler
        # tools added for java shim layer packaging.
        # This image is packed with shim layer libraries that are necessary
        # for Java chaincode runtime.
        runtime: $(DOCKER_NS)/fabric-javaenv:$(ARCH)-$(PROJECT_VERSION)

    node:
        # need node.js engine at runtime, currently available in baseimage
        # but not in baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseimage:$(ARCH)-$(BASE_VERSION)

    # Timeout duration for starting up a container and waiting for Register
    # to come through. 1sec should be plenty for chaincode unit tests
    startuptimeout: 300s

    # Timeout duration for Invoke and Init calls to prevent runaway.
    # This timeout is used by all chaincodes in all the channels, including
    # system chaincodes.
    # Note that during Invoke, if the image is not available (e.g. being
    # cleaned up when in development environment), the peer will automatically
    # build the image, which might take more time. In production environment,
    # the chaincode image is unlikely to be deleted, so the timeout could be
    # reduced accordingly.
    executetimeout: 30s

    # There are 2 modes: "dev" and "net".
    # In dev mode, user runs the chaincode after starting peer from
    # command line on local machine.
    # In net mode, peer will run chaincode in a docker container.
    mode: net

    # keepalive in seconds. In situations where the communiction goes through a
    # proxy that does not support keep-alive, this parameter will maintain connection
    # between peer and chaincode.
    # A value <= 0 turns keepalive off
    keepalive: 0

    # system chaincodes whitelist. To add system chaincode "myscc" to the
    # whitelist, add "myscc: enable" to the list below, and register in
    # chaincode/importsysccs.go
    system:
        cscc: enable
        lscc: enable
        escc: enable
        vscc: enable
        qscc: enable

    # System chaincode plugins:
    # System chaincodes can be loaded as shared objects compiled as Go plugins.
    # See examples/plugins/scc for an example.
    # Plugins must be white listed in the chaincode.system section above.
    systemPlugins:
      # example configuration:
      # - enabled: true
      #   name: myscc
      #   path: /opt/lib/myscc.so
      #   invokableExternal: true
      #   invokableCC2CC: true

    # Logging section for the chaincode container
    logging:
      # Default level for all loggers within the chaincode container
      level:  info
      # Override default level for the 'shim' logger
      shim:   warning
      # Format for the chaincode container logs
      format: '%{color}%{time:2006-01-02 15:04:05.000 MST} [%{module}] %{shortfunc} -> %{level:.4s} %{id:03x}%{color:reset} %{message}'

###############################################################################
#
#    Ledger section - ledger configuration encompases both the blockchain
#    and the state
#
###############################################################################
ledger:

  blockchain:

  state:
    # stateDatabase - options are "goleveldb", "CouchDB"
    # goleveldb - default state database stored in goleveldb.
    # CouchDB - store state database in CouchDB
    stateDatabase: goleveldb
    # Limit on the number of records to return per query
    totalQueryLimit: 100000
    couchDBConfig:
       # It is recommended to run CouchDB on the same server as the peer, and
       # not map the CouchDB container port to a server port in docker-compose.
       # Otherwise proper security must be provided on the connection between
       # CouchDB client (on the peer) and server.
       couchDBAddress: 127.0.0.1:5984
       # This username must have read and write authority on CouchDB
       username:
       # The password is recommended to pass as an environment variable
       # during start up (eg CORE_LEDGER_STATE_COUCHDBCONFIG_PASSWORD).
       # If it is stored here, the file must be access control protected
       # to prevent unintended users from discovering the password.
       password:
       # Number of retries for CouchDB errors
       maxRetries: 3
       # Number of retries for CouchDB errors during peer startup
       maxRetriesOnStartup: 12
       # CouchDB request timeout (unit: duration, e.g. 20s)
       requestTimeout: 35s
       # Limit on the number of records per each CouchDB query
       # Note that chaincode queries are only bound by totalQueryLimit.
       # Internally the chaincode may execute multiple CouchDB queries,
       # each of size internalQueryLimit.
       internalQueryLimit: 1000
       # Limit on the number of records per CouchDB bulk update batch
       maxBatchUpdateSize: 1000
       # Warm indexes after every N blocks.
       # This option warms any indexes that have been
       # deployed to CouchDB after every N blocks.
       # A value of 1 will warm indexes after every block commit,
       # to ensure fast selector queries.
       # Increasing the value may improve write efficiency of peer and CouchDB,
       # but may degrade query response time.
       warmIndexesAfterNBlocks: 1
       # Create the _global_changes system database
       # This is optional.  Creating the global changes database will require
       # additional system resources to track changes and maintain the database
       createGlobalChangesDB: false

  history:
    # enableHistoryDatabase - options are true or false
    # Indicates if the history of key updates should be stored.
    # All history 'index' will be stored in goleveldb, regardless if using
    # CouchDB or alternate database for the state.
    enableHistoryDatabase: true

###############################################################################
#
#    Operations section
#
###############################################################################
operations:
    # host and port for the operations server
    listenAddress: 0.0.0.0:9444

    # TLS configuration for the operations endpoint
    tls:
        # TLS enabled
        enabled: false

        # path to PEM encoded server certificate for the operations server
        cert:
            file:

        # path to PEM encoded server key for the operations server
        key:
            file:

        # most operations service endpoints require client authentication when TLS
        # is enabled. clientAuthRequired requires client certificate authentication
        # at the TLS layer to access all resources.
        clientAuthRequired: false

        # paths to PEM encoded ca certificates to trust for client authentication
        clientRootCAs:
            files: []

###############################################################################
#
#    Metrics section
#
###############################################################################
metrics:
    # metrics provider is one of statsd, prometheus, or disabled
    provider: disabled

    # statsd configuration
    statsd:
        # network type: tcp or udp
        network: udp

        # statsd server address
        address: 127.0.0.1:8125

        # the interval at which locally cached counters and gauges are pushed
        # to statsd; timings are pushed immediately
        writeInterval: 10s

        # prefix is prepended to all emitted statsd metrics
        prefix:
```





## core3.yaml

```yaml
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

###############################################################################
#
#    Peer section
#
###############################################################################
peer:

    # The Peer id is used for identifying this Peer instance.
    id: peer0.org3.dy

    # The networkId allows for logical seperation of networks
    networkId: dev

    # The Address at local network interface this Peer will listen on.
    # By default, it will listen on all network interfaces
    listenAddress: 0.0.0.0:7063

    # The endpoint this peer uses to listen for inbound chaincode connections.
    # If this is commented-out, the listen address is selected to be
    # the peer's address (see below) with port 7052

    # I change it to 7041 for local test.
    chaincodeListenAddress: 0.0.0.0:7043

    # The endpoint the chaincode for this peer uses to connect to the peer.
    # If this is not specified, the chaincodeListenAddress address is selected.
    # And if chaincodeListenAddress is not specified, address is selected from
    # peer listenAddress.
    # chaincodeAddress: 0.0.0.0:7041

    # When used as peer config, this represents the endpoint to other peers
    # in the same organization. For peers in other organization, see
    # gossip.externalEndpoint for more info.
    # When used as CLI config, this means the peer's endpoint to interact with
    address: peer0.org3.dy:7063

    # Whether the Peer should programmatically determine its address
    # This case is useful for docker containers.
    addressAutoDetect: false

    # Setting for runtime.GOMAXPROCS(n). If n < 1, it does not change the
    # current setting
    gomaxprocs: -1

    # Keepalive settings for peer server and clients
    keepalive:
        # MinInterval is the minimum permitted time between client pings.
        # If clients send pings more frequently, the peer server will
        # disconnect them
        minInterval: 60s
        # Client keepalive settings for communicating with other peer nodes
        client:
            # Interval is the time between pings to peer nodes.  This must
            # greater than or equal to the minInterval specified by peer
            # nodes
            interval: 60s
            # Timeout is the duration the client waits for a response from
            # peer nodes before closing the connection
            timeout: 20s
        # DeliveryClient keepalive settings for communication with ordering
        # nodes.
        deliveryClient:
            # Interval is the time between pings to ordering nodes.  This must
            # greater than or equal to the minInterval specified by ordering
            # nodes.
            interval: 60s
            # Timeout is the duration the client waits for a response from
            # ordering nodes before closing the connection
            timeout: 20s


    # Gossip related configuration
    gossip:
        # Bootstrap set to initialize gossip with.
        # This is a list of other peers that this peer reaches out to at startup.
        # Important: The endpoints here have to be endpoints of peers in the same
        # organization, because the peer would refuse connecting to these endpoints
        # unless they are in the same organization as the peer.
        bootstrap: peer0.org3.dy:7063

        # NOTE: orgLeader and useLeaderElection parameters are mutual exclusive.
        # Setting both to true would result in the termination of the peer
        # since this is undefined state. If the peers are configured with
        # useLeaderElection=false, make sure there is at least 1 peer in the
        # organization that its orgLeader is set to true.

        # Defines whenever peer will initialize dynamic algorithm for
        # "leader" selection, where leader is the peer to establish
        # connection with ordering service and use delivery protocol
        # to pull ledger blocks from ordering service. It is recommended to
        # use leader election for large networks of peers.
        useLeaderElection: true
        # Statically defines peer to be an organization "leader",
        # where this means that current peer will maintain connection
        # with ordering service and disseminate block across peers in
        # its own organization
        orgLeader: false

        # Interval for membershipTracker polling
        membershipTrackerInterval: 5s

        # Overrides the endpoint that the peer publishes to peers
        # in its organization. For peers in foreign organizations
        # see 'externalEndpoint'
        endpoint:
        # Maximum count of blocks stored in memory
        maxBlockCountToStore: 100
        # Max time between consecutive message pushes(unit: millisecond)
        maxPropagationBurstLatency: 10ms
        # Max number of messages stored until a push is triggered to remote peers
        maxPropagationBurstSize: 10
        # Number of times a message is pushed to remote peers
        propagateIterations: 1
        # Number of peers selected to push messages to
        propagatePeerNum: 3
        # Determines frequency of pull phases(unit: second)
        # Must be greater than digestWaitTime + responseWaitTime
        pullInterval: 4s
        # Number of peers to pull from
        pullPeerNum: 3
        # Determines frequency of pulling state info messages from peers(unit: second)
        requestStateInfoInterval: 4s
        # Determines frequency of pushing state info messages to peers(unit: second)
        publishStateInfoInterval: 4s
        # Maximum time a stateInfo message is kept until expired
        stateInfoRetentionInterval:
        # Time from startup certificates are included in Alive messages(unit: second)
        publishCertPeriod: 10s
        # Should we skip verifying block messages or not (currently not in use)
        skipBlockVerification: false
        # Dial timeout(unit: second)
        dialTimeout: 3s
        # Connection timeout(unit: second)
        connTimeout: 2s
        # Buffer size of received messages
        recvBuffSize: 20
        # Buffer size of sending messages
        sendBuffSize: 200
        # Time to wait before pull engine processes incoming digests (unit: second)
        # Should be slightly smaller than requestWaitTime
        digestWaitTime: 1s
        # Time to wait before pull engine removes incoming nonce (unit: milliseconds)
        # Should be slightly bigger than digestWaitTime
        requestWaitTime: 1500ms
        # Time to wait before pull engine ends pull (unit: second)
        responseWaitTime: 2s
        # Alive check interval(unit: second)
        aliveTimeInterval: 5s
        # Alive expiration timeout(unit: second)
        aliveExpirationTimeout: 25s
        # Reconnect interval(unit: second)
        reconnectInterval: 25s
        # This is an endpoint that is published to peers outside of the organization.
        # If this isn't set, the peer will not be known to other organizations.
        externalEndpoint: peer0.org3.dy:7063
        # Leader election service configuration
        election:
            # Longest time peer waits for stable membership during leader election startup (unit: second)
            startupGracePeriod: 15s
            # Interval gossip membership samples to check its stability (unit: second)
            membershipSampleInterval: 1s
            # Time passes since last declaration message before peer decides to perform leader election (unit: second)
            leaderAliveThreshold: 10s
            # Time between peer sends propose message and declares itself as a leader (sends declaration message) (unit: second)
            leaderElectionDuration: 5s

        pvtData:
            # pullRetryThreshold determines the maximum duration of time private data corresponding for a given block
            # would be attempted to be pulled from peers until the block would be committed without the private data
            pullRetryThreshold: 60s
            # As private data enters the transient store, it is associated with the peer's ledger's height at that time.
            # transientstoreMaxBlockRetention defines the maximum difference between the current ledger's height upon commit,
            # and the private data residing inside the transient store that is guaranteed not to be purged.
            # Private data is purged from the transient store when blocks with sequences that are multiples
            # of transientstoreMaxBlockRetention are committed.
            transientstoreMaxBlockRetention: 1000
            # pushAckTimeout is the maximum time to wait for an acknowledgement from each peer
            # at private data push at endorsement time.
            pushAckTimeout: 3s
            # Block to live pulling margin, used as a buffer
            # to prevent peer from trying to pull private data
            # from peers that is soon to be purged in next N blocks.
            # This helps a newly joined peer catch up to current
            # blockchain height quicker.
            btlPullMargin: 10
            # the process of reconciliation is done in an endless loop, while in each iteration reconciler tries to
            # pull from the other peers the most recent missing blocks with a maximum batch size limitation.
            # reconcileBatchSize determines the maximum batch size of missing private data that will be reconciled in a
            # single iteration.
            reconcileBatchSize: 10
            # reconcileSleepInterval determines the time reconciler sleeps from end of an iteration until the beginning
            # of the next reconciliation iteration.
            reconcileSleepInterval: 1m
            # reconciliationEnabled is a flag that indicates whether private data reconciliation is enable or not.
            reconciliationEnabled: true

        # Gossip state transfer related configuration
        state:
            # indicates whenever state transfer is enabled or not
            # default value is true, i.e. state transfer is active
            # and takes care to sync up missing blocks allowing
            # lagging peer to catch up to speed with rest network
            enabled: true
            # checkInterval interval to check whether peer is lagging behind enough to
            # request blocks via state transfer from another peer.
            checkInterval: 10s
            # responseTimeout amount of time to wait for state transfer response from
            # other peers
            responseTimeout: 3s
            # batchSize the number of blocks to request via state transfer from another peer
            batchSize: 10
            # blockBufferSize reflect the maximum distance between lowest and
            # highest block sequence number state buffer to avoid holes.
            # In order to ensure absence of the holes actual buffer size
            # is twice of this distance
            blockBufferSize: 100
            # maxRetries maximum number of re-tries to ask
            # for single state transfer request
            maxRetries: 3

    # TLS Settings
    # Note that peer-chaincode connections through chaincodeListenAddress is
    # not mutual TLS auth. See comments on chaincodeListenAddress for more info
    tls:
        # Require server-side TLS
        enabled:  false
        # Require client certificates / mutual TLS.
        # Note that clients that are not configured to use a certificate will
        # fail to connect to the peer.
        clientAuthRequired: false
        # X.509 certificate used for TLS server
        cert:
            file: crypto-config/peerOrganizations/org3.dy/peers/peer0.org3.dy/tls/server.crt
        # Private key used for TLS server (and client if clientAuthEnabled
        # is set to true
        key:
            file: crypto-config/peerOrganizations/org3.dy/peers/peer0.org3.dy/tls/server.key
        # Trusted root certificate chain for tls.cert
        rootcert:
            file: crypto-config/peerOrganizations/org3.dy/peers/peer0.org3.dy/tls/ca.crt
        # Set of root certificate authorities used to verify client certificates
        clientRootCAs:
            files:
              - tls/ca.crt
        # Private key used for TLS when making client connections.  If
        # not set, peer.tls.key.file will be used instead
        clientKey:
            file:
        # X.509 certificate used for TLS when making client connections.
        # If not set, peer.tls.cert.file will be used instead
        clientCert:
            file:

    # Authentication contains configuration parameters related to authenticating
    # client messages
    authentication:
        # the acceptable difference between the current server time and the
        # client's time as specified in a client request message
        timewindow: 15m

    # Path on the file system where peer will store data (eg ledger). This
    # location must be access control protected to prevent unintended
    # modification that might corrupt the peer operations.
    fileSystemPath: ../peer3/production

    # BCCSP (Blockchain crypto provider): Select which crypto implementation or
    # library to use
    BCCSP:
        Default: SW
        # Settings for the SW crypto provider (i.e. when DEFAULT: SW)
        SW:
            # TODO: The default Hash and Security level needs refactoring to be
            # fully configurable. Changing these defaults requires coordination
            # SHA2 is hardcoded in several places, not only BCCSP
            Hash: SHA2
            Security: 256
            # Location of Key Store
            FileKeyStore:
                # If "", defaults to 'mspConfigPath'/keystore
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

    # Path on the file system where peer will find MSP local configurations
    mspConfigPath: crypto-config/peerOrganizations/org3.dy/peers/peer0.org3.dy/msp

    # Identifier of the local MSP
    # ----!!!!IMPORTANT!!!-!!!IMPORTANT!!!-!!!IMPORTANT!!!!----
    # Deployers need to change the value of the localMspId string.
    # In particular, the name of the local MSP ID of a peer needs
    # to match the name of one of the MSPs in each of the channel
    # that this peer is a member of. Otherwise this peer's messages
    # will not be identified as valid by other nodes.
    localMspId: Org3MSP

    # CLI common client config options
    client:
        # connection timeout
        connTimeout: 3s

    # Delivery service related config
    deliveryclient:
        # It sets the total time the delivery service may spend in reconnection
        # attempts until its retry logic gives up and returns an error
        reconnectTotalTimeThreshold: 3600s

        # It sets the delivery service <-> ordering service node connection timeout
        connTimeout: 3s

        # It sets the delivery service maximal delay between consecutive retries
        reConnectBackoffThreshold: 3600s

    # Type for the local MSP - by default it's of type bccsp
    localMspType: bccsp

    # Used with Go profiling tools only in none production environment. In
    # production, it should be disabled (eg enabled: false)
    profile:
        enabled:     false
        listenAddress: 0.0.0.0:6060

    # The admin service is used for administrative operations such as
    # control over logger levels, etc.
    # Only peer administrators can use the service.
    adminService:
        # The interface and port on which the admin server will listen on.
        # If this is commented out, or the port number is equal to the port
        # of the peer listen address - the admin service is attached to the
        # peer's service (defaults to 7051).
        #listenAddress: 0.0.0.0:7055

    # Handlers defines custom handlers that can filter and mutate
    # objects passing within the peer, such as:
    #   Auth filter - reject or forward proposals from clients
    #   Decorators  - append or mutate the chaincode input passed to the chaincode
    #   Endorsers   - Custom signing over proposal response payload and its mutation
    # Valid handler definition contains:
    #   - A name which is a factory method name defined in
    #     core/handlers/library/library.go for statically compiled handlers
    #   - library path to shared object binary for pluggable filters
    # Auth filters and decorators are chained and executed in the order that
    # they are defined. For example:
    # authFilters:
    #   -
    #     name: FilterOne
    #     library: /opt/lib/filter.so
    #   -
    #     name: FilterTwo
    # decorators:
    #   -
    #     name: DecoratorOne
    #   -
    #     name: DecoratorTwo
    #     library: /opt/lib/decorator.so
    # Endorsers are configured as a map that its keys are the endorsement system chaincodes that are being overridden.
    # Below is an example that overrides the default ESCC and uses an endorsement plugin that has the same functionality
    # as the default ESCC.
    # If the 'library' property is missing, the name is used as the constructor method in the builtin library similar
    # to auth filters and decorators.
    # endorsers:
    #   escc:
    #     name: DefaultESCC
    #     library: /etc/hyperledger/fabric/plugin/escc.so
    handlers:
        authFilters:
          -
            name: DefaultAuth
          -
            name: ExpirationCheck    # This filter checks identity x509 certificate expiration
        decorators:
          -
            name: DefaultDecorator
        endorsers:
          escc:
            name: DefaultEndorsement
            library:
        validators:
          vscc:
            name: DefaultValidation
            library:

    #    library: /etc/hyperledger/fabric/plugin/escc.so
    # Number of goroutines that will execute transaction validation in parallel.
    # By default, the peer chooses the number of CPUs on the machine. Set this
    # variable to override that choice.
    # NOTE: overriding this value might negatively influence the performance of
    # the peer so please change this value only if you know what you're doing
    validatorPoolSize:

    # The discovery service is used by clients to query information about peers,
    # such as - which peers have joined a certain channel, what is the latest
    # channel config, and most importantly - given a chaincode and a channel,
    # what possible sets of peers satisfy the endorsement policy.
    discovery:
        enabled: true
        # Whether the authentication cache is enabled or not.
        authCacheEnabled: true
        # The maximum size of the cache, after which a purge takes place
        authCacheMaxSize: 1000
        # The proportion (0 to 1) of entries that remain in the cache after the cache is purged due to overpopulation
        authCachePurgeRetentionRatio: 0.75
        # Whether to allow non-admins to perform non channel scoped queries.
        # When this is false, it means that only peer admins can perform non channel scoped queries.
        orgMembersAllowedAccess: false
###############################################################################
#
#    VM section
#
###############################################################################
vm:

    # Endpoint of the vm management system.  For docker can be one of the following in general
    # unix:///var/run/docker.sock
    # http://localhost:2375
    # https://localhost:2376
    endpoint: unix:///var/run/docker.sock

    # settings for docker vms
    docker:
        tls:
            enabled: false
            ca:
                file: docker/ca.crt
            cert:
                file: docker/tls.crt
            key:
                file: docker/tls.key

        # Enables/disables the standard out/err from chaincode containers for
        # debugging purposes
        attachStdout: true

        # Parameters on creating docker container.
        # Container may be efficiently created using ipam & dns-server for cluster
        # NetworkMode - sets the networking mode for the container. Supported
        # standard values are: `host`(default),`bridge`,`ipvlan`,`none`.
        # Dns - a list of DNS servers for the container to use.
        # Note:  `Privileged` `Binds` `Links` and `PortBindings` properties of
        # Docker Host Config are not supported and will not be used if set.
        # LogConfig - sets the logging driver (Type) and related options
        # (Config) for Docker. For more info,
        # https://docs.docker.com/engine/admin/logging/overview/
        # Note: Set LogConfig using Environment Variables is not supported.
        hostConfig:
            NetworkMode: mynet
            Dns:
               # - 192.168.0.1
            LogConfig:
                Type: json-file
                Config:
                    max-size: "50m"
                    max-file: "5"
            Memory: 2147483648

###############################################################################
#
#    Chaincode section
#
###############################################################################
chaincode:

    # The id is used by the Chaincode stub to register the executing Chaincode
    # ID with the Peer and is generally supplied through ENV variables
    # the `path` form of ID is provided when installing the chaincode.
    # The `name` is used for all other requests and can be any string.
    id:
        path:
        name:

    # Generic builder environment, suitable for most chaincode types
    builder: $(DOCKER_NS)/fabric-ccenv:latest

    # Enables/disables force pulling of the base docker images (listed below)
    # during user chaincode instantiation.
    # Useful when using moving image tags (such as :latest)
    pull: false

    golang:
        # golang will never need more than baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseos:$(ARCH)-$(BASE_VERSION)

        # whether or not golang chaincode should be linked dynamically
        dynamicLink: false

    car:
        # car may need more facilities (JVM, etc) in the future as the catalog
        # of platforms are expanded.  For now, we can just use baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseos:$(ARCH)-$(BASE_VERSION)

    java:
        # This is an image based on java:openjdk-8 with addition compiler
        # tools added for java shim layer packaging.
        # This image is packed with shim layer libraries that are necessary
        # for Java chaincode runtime.
        runtime: $(DOCKER_NS)/fabric-javaenv:$(ARCH)-$(PROJECT_VERSION)

    node:
        # need node.js engine at runtime, currently available in baseimage
        # but not in baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseimage:$(ARCH)-$(BASE_VERSION)

    # Timeout duration for starting up a container and waiting for Register
    # to come through. 1sec should be plenty for chaincode unit tests
    startuptimeout: 300s

    # Timeout duration for Invoke and Init calls to prevent runaway.
    # This timeout is used by all chaincodes in all the channels, including
    # system chaincodes.
    # Note that during Invoke, if the image is not available (e.g. being
    # cleaned up when in development environment), the peer will automatically
    # build the image, which might take more time. In production environment,
    # the chaincode image is unlikely to be deleted, so the timeout could be
    # reduced accordingly.
    executetimeout: 30s

    # There are 2 modes: "dev" and "net".
    # In dev mode, user runs the chaincode after starting peer from
    # command line on local machine.
    # In net mode, peer will run chaincode in a docker container.
    mode: net

    # keepalive in seconds. In situations where the communiction goes through a
    # proxy that does not support keep-alive, this parameter will maintain connection
    # between peer and chaincode.
    # A value <= 0 turns keepalive off
    keepalive: 0

    # system chaincodes whitelist. To add system chaincode "myscc" to the
    # whitelist, add "myscc: enable" to the list below, and register in
    # chaincode/importsysccs.go
    system:
        cscc: enable
        lscc: enable
        escc: enable
        vscc: enable
        qscc: enable

    # System chaincode plugins:
    # System chaincodes can be loaded as shared objects compiled as Go plugins.
    # See examples/plugins/scc for an example.
    # Plugins must be white listed in the chaincode.system section above.
    systemPlugins:
      # example configuration:
      # - enabled: true
      #   name: myscc
      #   path: /opt/lib/myscc.so
      #   invokableExternal: true
      #   invokableCC2CC: true

    # Logging section for the chaincode container
    logging:
      # Default level for all loggers within the chaincode container
      level:  info
      # Override default level for the 'shim' logger
      shim:   warning
      # Format for the chaincode container logs
      format: '%{color}%{time:2006-01-02 15:04:05.000 MST} [%{module}] %{shortfunc} -> %{level:.4s} %{id:03x}%{color:reset} %{message}'

###############################################################################
#
#    Ledger section - ledger configuration encompases both the blockchain
#    and the state
#
###############################################################################
ledger:

  blockchain:

  state:
    # stateDatabase - options are "goleveldb", "CouchDB"
    # goleveldb - default state database stored in goleveldb.
    # CouchDB - store state database in CouchDB
    stateDatabase: goleveldb
    # Limit on the number of records to return per query
    totalQueryLimit: 100000
    couchDBConfig:
       # It is recommended to run CouchDB on the same server as the peer, and
       # not map the CouchDB container port to a server port in docker-compose.
       # Otherwise proper security must be provided on the connection between
       # CouchDB client (on the peer) and server.
       couchDBAddress: 127.0.0.1:5984
       # This username must have read and write authority on CouchDB
       username:
       # The password is recommended to pass as an environment variable
       # during start up (eg CORE_LEDGER_STATE_COUCHDBCONFIG_PASSWORD).
       # If it is stored here, the file must be access control protected
       # to prevent unintended users from discovering the password.
       password:
       # Number of retries for CouchDB errors
       maxRetries: 3
       # Number of retries for CouchDB errors during peer startup
       maxRetriesOnStartup: 12
       # CouchDB request timeout (unit: duration, e.g. 20s)
       requestTimeout: 35s
       # Limit on the number of records per each CouchDB query
       # Note that chaincode queries are only bound by totalQueryLimit.
       # Internally the chaincode may execute multiple CouchDB queries,
       # each of size internalQueryLimit.
       internalQueryLimit: 1000
       # Limit on the number of records per CouchDB bulk update batch
       maxBatchUpdateSize: 1000
       # Warm indexes after every N blocks.
       # This option warms any indexes that have been
       # deployed to CouchDB after every N blocks.
       # A value of 1 will warm indexes after every block commit,
       # to ensure fast selector queries.
       # Increasing the value may improve write efficiency of peer and CouchDB,
       # but may degrade query response time.
       warmIndexesAfterNBlocks: 1
       # Create the _global_changes system database
       # This is optional.  Creating the global changes database will require
       # additional system resources to track changes and maintain the database
       createGlobalChangesDB: false

  history:
    # enableHistoryDatabase - options are true or false
    # Indicates if the history of key updates should be stored.
    # All history 'index' will be stored in goleveldb, regardless if using
    # CouchDB or alternate database for the state.
    enableHistoryDatabase: true

###############################################################################
#
#    Operations section
#
###############################################################################
operations:
    # host and port for the operations server
    listenAddress: 0.0.0.0:9445

    # TLS configuration for the operations endpoint
    tls:
        # TLS enabled
        enabled: false

        # path to PEM encoded server certificate for the operations server
        cert:
            file:

        # path to PEM encoded server key for the operations server
        key:
            file:

        # most operations service endpoints require client authentication when TLS
        # is enabled. clientAuthRequired requires client certificate authentication
        # at the TLS layer to access all resources.
        clientAuthRequired: false

        # paths to PEM encoded ca certificates to trust for client authentication
        clientRootCAs:
            files: []

###############################################################################
#
#    Metrics section
#
###############################################################################
metrics:
    # metrics provider is one of statsd, prometheus, or disabled
    provider: disabled

    # statsd configuration
    statsd:
        # network type: tcp or udp
        network: udp

        # statsd server address
        address: 127.0.0.1:8125

        # the interval at which locally cached counters and gauges are pushed
        # to statsd; timings are pushed immediately
        writeInterval: 10s

        # prefix is prepended to all emitted statsd metrics
        prefix:
```



## core4.yaml

```YAML
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

###############################################################################
#
#    Peer section
#
###############################################################################
peer:

    # The Peer id is used for identifying this Peer instance.
    id: peer0.org4.dy

    # The networkId allows for logical seperation of networks
    networkId: dev

    # The Address at local network interface this Peer will listen on.
    # By default, it will listen on all network interfaces
    listenAddress: 0.0.0.0:7064

    # The endpoint this peer uses to listen for inbound chaincode connections.
    # If this is commented-out, the listen address is selected to be
    # the peer's address (see below) with port 7052

    # I change it to 7041 for local test.
    chaincodeListenAddress: 0.0.0.0:7044

    # The endpoint the chaincode for this peer uses to connect to the peer.
    # If this is not specified, the chaincodeListenAddress address is selected.
    # And if chaincodeListenAddress is not specified, address is selected from
    # peer listenAddress.
    # chaincodeAddress: 0.0.0.0:7041

    # When used as peer config, this represents the endpoint to other peers
    # in the same organization. For peers in other organization, see
    # gossip.externalEndpoint for more info.
    # When used as CLI config, this means the peer's endpoint to interact with
    address: peer0.org4.dy:7064

    # Whether the Peer should programmatically determine its address
    # This case is useful for docker containers.
    addressAutoDetect: false

    # Setting for runtime.GOMAXPROCS(n). If n < 1, it does not change the
    # current setting
    gomaxprocs: -1

    # Keepalive settings for peer server and clients
    keepalive:
        # MinInterval is the minimum permitted time between client pings.
        # If clients send pings more frequently, the peer server will
        # disconnect them
        minInterval: 60s
        # Client keepalive settings for communicating with other peer nodes
        client:
            # Interval is the time between pings to peer nodes.  This must
            # greater than or equal to the minInterval specified by peer
            # nodes
            interval: 60s
            # Timeout is the duration the client waits for a response from
            # peer nodes before closing the connection
            timeout: 20s
        # DeliveryClient keepalive settings for communication with ordering
        # nodes.
        deliveryClient:
            # Interval is the time between pings to ordering nodes.  This must
            # greater than or equal to the minInterval specified by ordering
            # nodes.
            interval: 60s
            # Timeout is the duration the client waits for a response from
            # ordering nodes before closing the connection
            timeout: 20s


    # Gossip related configuration
    gossip:
        # Bootstrap set to initialize gossip with.
        # This is a list of other peers that this peer reaches out to at startup.
        # Important: The endpoints here have to be endpoints of peers in the same
        # organization, because the peer would refuse connecting to these endpoints
        # unless they are in the same organization as the peer.
        bootstrap: peer0.org4.dy:7064

        # NOTE: orgLeader and useLeaderElection parameters are mutual exclusive.
        # Setting both to true would result in the termination of the peer
        # since this is undefined state. If the peers are configured with
        # useLeaderElection=false, make sure there is at least 1 peer in the
        # organization that its orgLeader is set to true.

        # Defines whenever peer will initialize dynamic algorithm for
        # "leader" selection, where leader is the peer to establish
        # connection with ordering service and use delivery protocol
        # to pull ledger blocks from ordering service. It is recommended to
        # use leader election for large networks of peers.
        useLeaderElection: true
        # Statically defines peer to be an organization "leader",
        # where this means that current peer will maintain connection
        # with ordering service and disseminate block across peers in
        # its own organization
        orgLeader: false

        # Interval for membershipTracker polling
        membershipTrackerInterval: 5s

        # Overrides the endpoint that the peer publishes to peers
        # in its organization. For peers in foreign organizations
        # see 'externalEndpoint'
        endpoint:
        # Maximum count of blocks stored in memory
        maxBlockCountToStore: 100
        # Max time between consecutive message pushes(unit: millisecond)
        maxPropagationBurstLatency: 10ms
        # Max number of messages stored until a push is triggered to remote peers
        maxPropagationBurstSize: 10
        # Number of times a message is pushed to remote peers
        propagateIterations: 1
        # Number of peers selected to push messages to
        propagatePeerNum: 3
        # Determines frequency of pull phases(unit: second)
        # Must be greater than digestWaitTime + responseWaitTime
        pullInterval: 4s
        # Number of peers to pull from
        pullPeerNum: 3
        # Determines frequency of pulling state info messages from peers(unit: second)
        requestStateInfoInterval: 4s
        # Determines frequency of pushing state info messages to peers(unit: second)
        publishStateInfoInterval: 4s
        # Maximum time a stateInfo message is kept until expired
        stateInfoRetentionInterval:
        # Time from startup certificates are included in Alive messages(unit: second)
        publishCertPeriod: 10s
        # Should we skip verifying block messages or not (currently not in use)
        skipBlockVerification: false
        # Dial timeout(unit: second)
        dialTimeout: 3s
        # Connection timeout(unit: second)
        connTimeout: 2s
        # Buffer size of received messages
        recvBuffSize: 20
        # Buffer size of sending messages
        sendBuffSize: 200
        # Time to wait before pull engine processes incoming digests (unit: second)
        # Should be slightly smaller than requestWaitTime
        digestWaitTime: 1s
        # Time to wait before pull engine removes incoming nonce (unit: milliseconds)
        # Should be slightly bigger than digestWaitTime
        requestWaitTime: 1500ms
        # Time to wait before pull engine ends pull (unit: second)
        responseWaitTime: 2s
        # Alive check interval(unit: second)
        aliveTimeInterval: 5s
        # Alive expiration timeout(unit: second)
        aliveExpirationTimeout: 25s
        # Reconnect interval(unit: second)
        reconnectInterval: 25s
        # This is an endpoint that is published to peers outside of the organization.
        # If this isn't set, the peer will not be known to other organizations.
        externalEndpoint: peer0.org4.dy:7064
        # Leader election service configuration
        election:
            # Longest time peer waits for stable membership during leader election startup (unit: second)
            startupGracePeriod: 15s
            # Interval gossip membership samples to check its stability (unit: second)
            membershipSampleInterval: 1s
            # Time passes since last declaration message before peer decides to perform leader election (unit: second)
            leaderAliveThreshold: 10s
            # Time between peer sends propose message and declares itself as a leader (sends declaration message) (unit: second)
            leaderElectionDuration: 5s

        pvtData:
            # pullRetryThreshold determines the maximum duration of time private data corresponding for a given block
            # would be attempted to be pulled from peers until the block would be committed without the private data
            pullRetryThreshold: 60s
            # As private data enters the transient store, it is associated with the peer's ledger's height at that time.
            # transientstoreMaxBlockRetention defines the maximum difference between the current ledger's height upon commit,
            # and the private data residing inside the transient store that is guaranteed not to be purged.
            # Private data is purged from the transient store when blocks with sequences that are multiples
            # of transientstoreMaxBlockRetention are committed.
            transientstoreMaxBlockRetention: 1000
            # pushAckTimeout is the maximum time to wait for an acknowledgement from each peer
            # at private data push at endorsement time.
            pushAckTimeout: 3s
            # Block to live pulling margin, used as a buffer
            # to prevent peer from trying to pull private data
            # from peers that is soon to be purged in next N blocks.
            # This helps a newly joined peer catch up to current
            # blockchain height quicker.
            btlPullMargin: 10
            # the process of reconciliation is done in an endless loop, while in each iteration reconciler tries to
            # pull from the other peers the most recent missing blocks with a maximum batch size limitation.
            # reconcileBatchSize determines the maximum batch size of missing private data that will be reconciled in a
            # single iteration.
            reconcileBatchSize: 10
            # reconcileSleepInterval determines the time reconciler sleeps from end of an iteration until the beginning
            # of the next reconciliation iteration.
            reconcileSleepInterval: 1m
            # reconciliationEnabled is a flag that indicates whether private data reconciliation is enable or not.
            reconciliationEnabled: true

        # Gossip state transfer related configuration
        state:
            # indicates whenever state transfer is enabled or not
            # default value is true, i.e. state transfer is active
            # and takes care to sync up missing blocks allowing
            # lagging peer to catch up to speed with rest network
            enabled: true
            # checkInterval interval to check whether peer is lagging behind enough to
            # request blocks via state transfer from another peer.
            checkInterval: 10s
            # responseTimeout amount of time to wait for state transfer response from
            # other peers
            responseTimeout: 3s
            # batchSize the number of blocks to request via state transfer from another peer
            batchSize: 10
            # blockBufferSize reflect the maximum distance between lowest and
            # highest block sequence number state buffer to avoid holes.
            # In order to ensure absence of the holes actual buffer size
            # is twice of this distance
            blockBufferSize: 100
            # maxRetries maximum number of re-tries to ask
            # for single state transfer request
            maxRetries: 3

    # TLS Settings
    # Note that peer-chaincode connections through chaincodeListenAddress is
    # not mutual TLS auth. See comments on chaincodeListenAddress for more info
    tls:
        # Require server-side TLS
        enabled:  false
        # Require client certificates / mutual TLS.
        # Note that clients that are not configured to use a certificate will
        # fail to connect to the peer.
        clientAuthRequired: false
        # X.509 certificate used for TLS server
        cert:
            file: crypto-config/peerOrganizations/org4.dy/peers/peer0.org4.dy/tls/server.crt
        # Private key used for TLS server (and client if clientAuthEnabled
        # is set to true
        key:
            file: crypto-config/peerOrganizations/org4.dy/peers/peer0.org4.dy/tls/server.key
        # Trusted root certificate chain for tls.cert
        rootcert:
            file: crypto-config/peerOrganizations/org4.dy/peers/peer0.org4.dy/tls/ca.crt
        # Set of root certificate authorities used to verify client certificates
        clientRootCAs:
            files:
              - tls/ca.crt
        # Private key used for TLS when making client connections.  If
        # not set, peer.tls.key.file will be used instead
        clientKey:
            file:
        # X.509 certificate used for TLS when making client connections.
        # If not set, peer.tls.cert.file will be used instead
        clientCert:
            file:

    # Authentication contains configuration parameters related to authenticating
    # client messages
    authentication:
        # the acceptable difference between the current server time and the
        # client's time as specified in a client request message
        timewindow: 15m

    # Path on the file system where peer will store data (eg ledger). This
    # location must be access control protected to prevent unintended
    # modification that might corrupt the peer operations.
    fileSystemPath: ../peer4/production

    # BCCSP (Blockchain crypto provider): Select which crypto implementation or
    # library to use
    BCCSP:
        Default: SW
        # Settings for the SW crypto provider (i.e. when DEFAULT: SW)
        SW:
            # TODO: The default Hash and Security level needs refactoring to be
            # fully configurable. Changing these defaults requires coordination
            # SHA2 is hardcoded in several places, not only BCCSP
            Hash: SHA2
            Security: 256
            # Location of Key Store
            FileKeyStore:
                # If "", defaults to 'mspConfigPath'/keystore
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

    # Path on the file system where peer will find MSP local configurations
    mspConfigPath: crypto-config/peerOrganizations/org4.dy/peers/peer0.org4.dy/msp

    # Identifier of the local MSP
    # ----!!!!IMPORTANT!!!-!!!IMPORTANT!!!-!!!IMPORTANT!!!!----
    # Deployers need to change the value of the localMspId string.
    # In particular, the name of the local MSP ID of a peer needs
    # to match the name of one of the MSPs in each of the channel
    # that this peer is a member of. Otherwise this peer's messages
    # will not be identified as valid by other nodes.
    localMspId: Org4MSP

    # CLI common client config options
    client:
        # connection timeout
        connTimeout: 3s

    # Delivery service related config
    deliveryclient:
        # It sets the total time the delivery service may spend in reconnection
        # attempts until its retry logic gives up and returns an error
        reconnectTotalTimeThreshold: 3600s

        # It sets the delivery service <-> ordering service node connection timeout
        connTimeout: 3s

        # It sets the delivery service maximal delay between consecutive retries
        reConnectBackoffThreshold: 3600s

    # Type for the local MSP - by default it's of type bccsp
    localMspType: bccsp

    # Used with Go profiling tools only in none production environment. In
    # production, it should be disabled (eg enabled: false)
    profile:
        enabled:     false
        listenAddress: 0.0.0.0:6060

    # The admin service is used for administrative operations such as
    # control over logger levels, etc.
    # Only peer administrators can use the service.
    adminService:
        # The interface and port on which the admin server will listen on.
        # If this is commented out, or the port number is equal to the port
        # of the peer listen address - the admin service is attached to the
        # peer's service (defaults to 7051).
        #listenAddress: 0.0.0.0:7055

    # Handlers defines custom handlers that can filter and mutate
    # objects passing within the peer, such as:
    #   Auth filter - reject or forward proposals from clients
    #   Decorators  - append or mutate the chaincode input passed to the chaincode
    #   Endorsers   - Custom signing over proposal response payload and its mutation
    # Valid handler definition contains:
    #   - A name which is a factory method name defined in
    #     core/handlers/library/library.go for statically compiled handlers
    #   - library path to shared object binary for pluggable filters
    # Auth filters and decorators are chained and executed in the order that
    # they are defined. For example:
    # authFilters:
    #   -
    #     name: FilterOne
    #     library: /opt/lib/filter.so
    #   -
    #     name: FilterTwo
    # decorators:
    #   -
    #     name: DecoratorOne
    #   -
    #     name: DecoratorTwo
    #     library: /opt/lib/decorator.so
    # Endorsers are configured as a map that its keys are the endorsement system chaincodes that are being overridden.
    # Below is an example that overrides the default ESCC and uses an endorsement plugin that has the same functionality
    # as the default ESCC.
    # If the 'library' property is missing, the name is used as the constructor method in the builtin library similar
    # to auth filters and decorators.
    # endorsers:
    #   escc:
    #     name: DefaultESCC
    #     library: /etc/hyperledger/fabric/plugin/escc.so
    handlers:
        authFilters:
          -
            name: DefaultAuth
          -
            name: ExpirationCheck    # This filter checks identity x509 certificate expiration
        decorators:
          -
            name: DefaultDecorator
        endorsers:
          escc:
            name: DefaultEndorsement
            library:
        validators:
          vscc:
            name: DefaultValidation
            library:

    #    library: /etc/hyperledger/fabric/plugin/escc.so
    # Number of goroutines that will execute transaction validation in parallel.
    # By default, the peer chooses the number of CPUs on the machine. Set this
    # variable to override that choice.
    # NOTE: overriding this value might negatively influence the performance of
    # the peer so please change this value only if you know what you're doing
    validatorPoolSize:

    # The discovery service is used by clients to query information about peers,
    # such as - which peers have joined a certain channel, what is the latest
    # channel config, and most importantly - given a chaincode and a channel,
    # what possible sets of peers satisfy the endorsement policy.
    discovery:
        enabled: true
        # Whether the authentication cache is enabled or not.
        authCacheEnabled: true
        # The maximum size of the cache, after which a purge takes place
        authCacheMaxSize: 1000
        # The proportion (0 to 1) of entries that remain in the cache after the cache is purged due to overpopulation
        authCachePurgeRetentionRatio: 0.75
        # Whether to allow non-admins to perform non channel scoped queries.
        # When this is false, it means that only peer admins can perform non channel scoped queries.
        orgMembersAllowedAccess: false
###############################################################################
#
#    VM section
#
###############################################################################
vm:

    # Endpoint of the vm management system.  For docker can be one of the following in general
    # unix:///var/run/docker.sock
    # http://localhost:2375
    # https://localhost:2376
    endpoint: unix:///var/run/docker.sock

    # settings for docker vms
    docker:
        tls:
            enabled: false
            ca:
                file: docker/ca.crt
            cert:
                file: docker/tls.crt
            key:
                file: docker/tls.key

        # Enables/disables the standard out/err from chaincode containers for
        # debugging purposes
        attachStdout: true

        # Parameters on creating docker container.
        # Container may be efficiently created using ipam & dns-server for cluster
        # NetworkMode - sets the networking mode for the container. Supported
        # standard values are: `host`(default),`bridge`,`ipvlan`,`none`.
        # Dns - a list of DNS servers for the container to use.
        # Note:  `Privileged` `Binds` `Links` and `PortBindings` properties of
        # Docker Host Config are not supported and will not be used if set.
        # LogConfig - sets the logging driver (Type) and related options
        # (Config) for Docker. For more info,
        # https://docs.docker.com/engine/admin/logging/overview/
        # Note: Set LogConfig using Environment Variables is not supported.
        hostConfig:
            NetworkMode: mynet
            Dns:
               # - 192.168.0.1
            LogConfig:
                Type: json-file
                Config:
                    max-size: "50m"
                    max-file: "5"
            Memory: 2147483648

###############################################################################
#
#    Chaincode section
#
###############################################################################
chaincode:

    # The id is used by the Chaincode stub to register the executing Chaincode
    # ID with the Peer and is generally supplied through ENV variables
    # the `path` form of ID is provided when installing the chaincode.
    # The `name` is used for all other requests and can be any string.
    id:
        path:
        name:

    # Generic builder environment, suitable for most chaincode types
    builder: $(DOCKER_NS)/fabric-ccenv:latest

    # Enables/disables force pulling of the base docker images (listed below)
    # during user chaincode instantiation.
    # Useful when using moving image tags (such as :latest)
    pull: false

    golang:
        # golang will never need more than baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseos:$(ARCH)-$(BASE_VERSION)

        # whether or not golang chaincode should be linked dynamically
        dynamicLink: false

    car:
        # car may need more facilities (JVM, etc) in the future as the catalog
        # of platforms are expanded.  For now, we can just use baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseos:$(ARCH)-$(BASE_VERSION)

    java:
        # This is an image based on java:openjdk-8 with addition compiler
        # tools added for java shim layer packaging.
        # This image is packed with shim layer libraries that are necessary
        # for Java chaincode runtime.
        runtime: $(DOCKER_NS)/fabric-javaenv:$(ARCH)-$(PROJECT_VERSION)

    node:
        # need node.js engine at runtime, currently available in baseimage
        # but not in baseos
        runtime: $(BASE_DOCKER_NS)/fabric-baseimage:$(ARCH)-$(BASE_VERSION)

    # Timeout duration for starting up a container and waiting for Register
    # to come through. 1sec should be plenty for chaincode unit tests
    startuptimeout: 300s

    # Timeout duration for Invoke and Init calls to prevent runaway.
    # This timeout is used by all chaincodes in all the channels, including
    # system chaincodes.
    # Note that during Invoke, if the image is not available (e.g. being
    # cleaned up when in development environment), the peer will automatically
    # build the image, which might take more time. In production environment,
    # the chaincode image is unlikely to be deleted, so the timeout could be
    # reduced accordingly.
    executetimeout: 30s

    # There are 2 modes: "dev" and "net".
    # In dev mode, user runs the chaincode after starting peer from
    # command line on local machine.
    # In net mode, peer will run chaincode in a docker container.
    mode: net

    # keepalive in seconds. In situations where the communiction goes through a
    # proxy that does not support keep-alive, this parameter will maintain connection
    # between peer and chaincode.
    # A value <= 0 turns keepalive off
    keepalive: 0

    # system chaincodes whitelist. To add system chaincode "myscc" to the
    # whitelist, add "myscc: enable" to the list below, and register in
    # chaincode/importsysccs.go
    system:
        cscc: enable
        lscc: enable
        escc: enable
        vscc: enable
        qscc: enable

    # System chaincode plugins:
    # System chaincodes can be loaded as shared objects compiled as Go plugins.
    # See examples/plugins/scc for an example.
    # Plugins must be white listed in the chaincode.system section above.
    systemPlugins:
      # example configuration:
      # - enabled: true
      #   name: myscc
      #   path: /opt/lib/myscc.so
      #   invokableExternal: true
      #   invokableCC2CC: true

    # Logging section for the chaincode container
    logging:
      # Default level for all loggers within the chaincode container
      level:  info
      # Override default level for the 'shim' logger
      shim:   warning
      # Format for the chaincode container logs
      format: '%{color}%{time:2006-01-02 15:04:05.000 MST} [%{module}] %{shortfunc} -> %{level:.4s} %{id:03x}%{color:reset} %{message}'

###############################################################################
#
#    Ledger section - ledger configuration encompases both the blockchain
#    and the state
#
###############################################################################
ledger:

  blockchain:

  state:
    # stateDatabase - options are "goleveldb", "CouchDB"
    # goleveldb - default state database stored in goleveldb.
    # CouchDB - store state database in CouchDB
    stateDatabase: goleveldb
    # Limit on the number of records to return per query
    totalQueryLimit: 100000
    couchDBConfig:
       # It is recommended to run CouchDB on the same server as the peer, and
       # not map the CouchDB container port to a server port in docker-compose.
       # Otherwise proper security must be provided on the connection between
       # CouchDB client (on the peer) and server.
       couchDBAddress: 127.0.0.1:5984
       # This username must have read and write authority on CouchDB
       username:
       # The password is recommended to pass as an environment variable
       # during start up (eg CORE_LEDGER_STATE_COUCHDBCONFIG_PASSWORD).
       # If it is stored here, the file must be access control protected
       # to prevent unintended users from discovering the password.
       password:
       # Number of retries for CouchDB errors
       maxRetries: 3
       # Number of retries for CouchDB errors during peer startup
       maxRetriesOnStartup: 12
       # CouchDB request timeout (unit: duration, e.g. 20s)
       requestTimeout: 35s
       # Limit on the number of records per each CouchDB query
       # Note that chaincode queries are only bound by totalQueryLimit.
       # Internally the chaincode may execute multiple CouchDB queries,
       # each of size internalQueryLimit.
       internalQueryLimit: 1000
       # Limit on the number of records per CouchDB bulk update batch
       maxBatchUpdateSize: 1000
       # Warm indexes after every N blocks.
       # This option warms any indexes that have been
       # deployed to CouchDB after every N blocks.
       # A value of 1 will warm indexes after every block commit,
       # to ensure fast selector queries.
       # Increasing the value may improve write efficiency of peer and CouchDB,
       # but may degrade query response time.
       warmIndexesAfterNBlocks: 1
       # Create the _global_changes system database
       # This is optional.  Creating the global changes database will require
       # additional system resources to track changes and maintain the database
       createGlobalChangesDB: false

  history:
    # enableHistoryDatabase - options are true or false
    # Indicates if the history of key updates should be stored.
    # All history 'index' will be stored in goleveldb, regardless if using
    # CouchDB or alternate database for the state.
    enableHistoryDatabase: true

###############################################################################
#
#    Operations section
#
###############################################################################
operations:
    # host and port for the operations server
    listenAddress: 0.0.0.0:9446

    # TLS configuration for the operations endpoint
    tls:
        # TLS enabled
        enabled: false

        # path to PEM encoded server certificate for the operations server
        cert:
            file:

        # path to PEM encoded server key for the operations server
        key:
            file:

        # most operations service endpoints require client authentication when TLS
        # is enabled. clientAuthRequired requires client certificate authentication
        # at the TLS layer to access all resources.
        clientAuthRequired: false

        # paths to PEM encoded ca certificates to trust for client authentication
        clientRootCAs:
            files: []

###############################################################################
#
#    Metrics section
#
###############################################################################
metrics:
    # metrics provider is one of statsd, prometheus, or disabled
    provider: disabled

    # statsd configuration
    statsd:
        # network type: tcp or udp
        network: udp

        # statsd server address
        address: 127.0.0.1:8125

        # the interval at which locally cached counters and gauges are pushed
        # to statsd; timings are pushed immediately
        writeInterval: 10s

        # prefix is prepended to all emitted statsd metrics
        prefix:

```

