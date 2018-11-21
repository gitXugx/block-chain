### 1.crypto-config注解

#### 1.1crypto-config.yaml

``````shell
#order的msp生成配置
OrdererOrgs:
  - Name: Orderer 
    Domain: example.com
    Specs:
      - Hostname: orderer
#Org的msp生成配置
PeerOrgs:
  - Name: Org1
    Domain: org1.example.com
    #节点个数从1开始
    Template:
      Count: 2
    #org的用户从0开始
    Users:
      Count: 1

  - Name: Org2
    Domain: org2.example.com
    Template:
      Count: 2
    Users:
      Count: 1

``````

#### 1.2 org生成的证书

``````shell

peerOrganizations/
├── org1.example.com
│   ├── ca
│   │   ├── 9c32da5aa2423df14d3438de971782e3e5264965c68e964024974718c4409bfb_sk
│   │   └── ca.org1.example.com-cert.pem
│   ├── msp
│   │   ├── admincerts
│   │   ├── cacerts
│   │   └── tlscacerts
│   ├── peers
│   │   ├── peer0.org1.example.com
│   │   └── peer1.org1.example.com
│   ├── tlsca
│   │   ├── 9f2972dbd31824c84b2766a798ef811c3dfe7c22e2fc63ef3f7e90a74ab26038_sk
│   │   └── tlsca.org1.example.com-cert.pem
│   └── users
│       ├── Admin@org1.example.com
│       └── User1@org1.example.com
└── org2.example.com
    ├── ca
    │   ├── 9bed173a2ca0c4215df94a5c5a11ed5efb4e3e0eedaf99647f08350a8809ba05_sk
    │   └── ca.org2.example.com-cert.pem
    ├── msp
    │   ├── admincerts
    │   ├── cacerts
    │   └── tlscacerts
    ├── peers
    │   ├── peer0.org2.example.com
    │   └── peer1.org2.example.com
    ├── tlsca
    │   ├── d67e998bef0205a5fcda1496f02daca367f830d6ec16030e677ce24a0fd428fc_sk
    │   └── tlsca.org2.example.com-cert.pem
    └── users
        ├── Admin@org2.example.com
        └── User1@org2.example.com

``````

#####1.2.1 多个msp的不同作用

`````shell
   peerOrganizations/org1.example.com/msp
`````
该目录的msp属于组织,每个组织的msp会存放在创世块中,以作为某个组织接入进来联盟链的权限

#####1.2.2 users的作用
`````shell
   peerOrganizations/org1.example.com/users
`````
当在peer下面做的所有操作都会有对应的users来标识(例如:创建channel ,install chaincode , invoke chaincode)

#### 1.3 order生成的证书

``````shell

ordererOrganizations/
└── example.com
    ├── ca
    │   ├── a93654a055b06b107db45241062659cea907891752cbd92cab2d3231d6f9e0cb_sk
    │   └── ca.example.com-cert.pem
    ├── msp
    │   ├── admincerts
    │   ├── cacerts
    │   └── tlscacerts
    ├── orderers
    │   └── orderer.example.com
    ├── tlsca
    │   ├── 8821070034e84a3fae0b40d30299670d9bc1474a8c431714b62953f4288f680d_sk
    │   └── tlsca.example.com-cert.pem
    └── users
        └── Admin@example.com
``````

#### 1.4 证书的作用

1. 接入联盟链的权限验证
2. 进行节点间通信的TLS
3. 对链的操作用户记录
