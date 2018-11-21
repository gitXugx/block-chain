### 1. peer-base讲解

####1.1 peer-base.yaml

``````shell

version: '2'
services:
  peer-base:
    #启动容器的名字
    image: hyperledger/fabric-peer
    #环境变量的设置,可以进如容器中执行 env 查看环境变量
    environment:
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=e2ecli_default
      - GODEBUG=netdns=go
      - CORE_LOGGING_LEVEL=DEBUG
      - CORE_PEER_TLS_ENABLED=false
      - CORE_PEER_GOSSIP_USELEADERELECTION=true
      - CORE_PEER_GOSSIP_ORGLEADER=false
      - CORE_PEER_PROFILE_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
    #容器的工作目录,当容器启动成功的时候会进入这个目录,执行面的command命令
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: peer node start
    
``````

