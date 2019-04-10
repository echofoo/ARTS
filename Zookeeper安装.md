# Zookeeper部署
## 1 安装
### 1） 下载并解压
### 2) 创建状态数据存储文件夹
mkdir -p /data/soft/zkdata
### 3) 配置zoo.cfg文件
dataDir=/data/soft/zkdata

clientPort=2181

server.1=dn1:2888:3888
server.2=dn1:2888:3888
server.3=dn1:2888:3888

### 4) 配置注意事项
在配置的dataDir目录下（本例中即/data/soft/zkdata目录下）创建一个myid文件，文件里面的数字在各个节点唯一，范围在0~255之间，且应该与server.X=dn1:2888:3888中的X代表的数字一致。

### 5) 云服务器的两个额外配置
由于云服务器无法真正关闭真实主机的防火墙，也无法访问真实主机的ip（给的所谓弹性公网ip并不是真实主机ip），所以还应添加如下两个操作：
* 将云上开放2181，2888，3888端口，将这三个端口加入安全组(实例->更多->安全组)。具体操作参照https://help.aliyun.com/document_detail/25443.html?spm=a2c4g.11186623.2.27.c7af71afHAp93s 或 https://jingyan.baidu.com/album/03b2f78c31bdea5ea237ae88.html?picindex=1
* 在zoo.cfg添加一行语句：`quorumListenOnAllIPs=true`,具体原因参照：https://blog.csdn.net/u014284000/article/details/74508963

## 2 .配置并生效全局变量
* 在/etc/profile文件中添加如下两条语句：
export ZK_HOME=/data/soft/zookeeper
export PATH=$PATH:$ZK_HOME/bin
* 生效环境变量
source /etc/profile


## 3 .启动并验证
```bash
zkServer.sh start
zkServer.sh status
```

以上操作均在3个datanode节点上执行。可以使用scp命令将下载安装包或刚刚设置的配置文件分发到其他节点。
