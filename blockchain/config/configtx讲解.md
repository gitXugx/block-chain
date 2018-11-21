### 1.configtx讲解

#### 1.1 configtx.yaml

``````shell

Profiles:
    #创世块的生成配置，创世块里面包含order信息和org信息,来做org的接入验证
    TwoOrgsOrdererGenesis:
        Orderer:
            <<: *OrdererDefaults
            Organizations:
                - *OrdererOrg
        Consortiums:
            SampleConsortium:
                Organizations:
                    - *Org1
                    - *Org2
    #配置初始的交易信息
    TwoOrgsChannel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *Org1
                - *Org2

Organizations:

    - &OrdererOrg
     development environment
        Name: OrdererOrg
        #order的mspId
        ID: OrdererMSP
        #之前生成的msp
        MSPDir: crypto-config/ordererOrganizations/example.com/msp

    - &Org1
        Name: Org1MSP
        #org1的msp
        ID: Org1MSP

        MSPDir: crypto-config/peerOrganizations/org1.example.com/msp
        #锚节点的配置,用来给其他组织通信的
        AnchorPeers:
            - Host: peer0.org1.example.com
              Port: 7051

    - &Org2
        Name: Org2MSP
        ID: Org2MSP

        MSPDir: crypto-config/peerOrganizations/org2.example.com/msp

        AnchorPeers:
            - Host: peer0.org2.example.com
              Port: 7051

Orderer: &OrdererDefaults

    #两种模式一种是solo主要用于开发环境,另一种是kafka(共识类型)
    OrdererType: solo

    Addresses:
        - orderer.example.com:7050
    #Orderer节点的出块时间
    BatchTimeout: 2s

    BatchSize:
        #区块大小包含最大交易笔数
        MaxMessageCount: 10
        #最大区块大小
        AbsoluteMaxBytes: 98 MB
        #建议区块大小
        PreferredMaxBytes: 512 KB

    Kafka:
        #kafka配置 如果order type是solo则该配置不生效
        Brokers:
            - 127.0.0.1:9092

    Organizations:

Application: &ApplicationDefaults

    Organizations:

``````
