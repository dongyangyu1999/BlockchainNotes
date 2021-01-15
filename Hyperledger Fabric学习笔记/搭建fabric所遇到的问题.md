> 此测试所拉取版本为 v1.1

# 使用`./network_setup.sh` 脚本来搭建一个集群

疑问：在脚本运行过程中遇到了如下错误

```bash
Error: Error endorsing chaincode: rpc error: code = Unknown desc = Error starting container: API error (404): {"message":"network e2ecli_default not found"}
```

报错信息是404 也就是找不到网络具体原因可以查看脚本做了什么

解决：

编辑文件`go/src/github.com/hyperledger/fabric/examples/e2e_cli/base/peer-base.yaml` 文件

```
将其中的

- CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=e2ecli_default

修改为

- CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=e2e_cli_default

```

并且使用./network_setup.sh down 将之前启动的关闭掉

再次执行 ./network_setup.sh up 即可

![image-20201019135504485](%E6%90%AD%E5%BB%BAfabric%E6%89%80%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98.assets/image-20201019135504485.png)



### 手动测试Fabric网络

这里有官方提供的小例子，在官方例子中，channel名字是mychannel，链码的名字是mycc。
首先进入CLI，然后**重新打开**一个命令行窗口，输入：
`docker exec -it cli bash`
这时用户为root@02638306b93a，在/opt/gopath/src/github.com/hyperledger/fabric/peer目录下，运行以下命令可以查询a账户的余额：
`peer chaincode query -C mychannel -n mycc -c '{"Args":["query","a"]}'`

![image-20201019135740342](%E6%90%AD%E5%BB%BAfabric%E6%89%80%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98.assets/image-20201019135740342.png)

可见方框中余额为90。

下面我们可以进行转账操作，操作为invoke ，由a转b 100：
`peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n mycc -c '{"Args":["invoke","a","b","100"]}'`

现在转账完毕， 我们试一试再查询一下a账户的余额，重复之前的查询指令，结果为：

![image-20201019140254919](%E6%90%AD%E5%BB%BAfabric%E6%89%80%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98.assets/image-20201019140254919.png)

结果正确，a的余额只有10了。
最后，我们需要关闭Fabric，这里先使用exit命令退出cli容器。
`exit`
然后类似于启动指令：
`cd ~/go/src/github.com/hyperledger/fabric/examples/e2e_cli`
`./network_setup.sh down`

![image-20201019140419786](%E6%90%AD%E5%BB%BAfabric%E6%89%80%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98.assets/image-20201019140419786.png)

到这，整个Fabric的环境测试完毕。