### 1. docker-compose-base讲解

####1.1 docker-compose-base.yaml

``````shell
version: '2'

services:

  orderer.example.com:
    container_name: orderer.example.com
    image: hyperledger/fabric-orderer
    environment:
      - GODEBUG=netdns=go
      - ORDERER_GENERAL_LOGLEVEL=debug
      - ORDERER_GENERAL_LISTENADDRESS=0.0.0.0
      #区块类型为文件类型
      - ORDERER_GENERAL_GENESISMETHOD=file
      #设置order启动是的创世块文件
      - ORDERER_GENERAL_GENESISFILE=/var/hyperledger/orderer/orderer.genesis.block
      - ORDERER_GENERAL_LOCALMSPID=OrdererMSP
      - ORDERER_GENERAL_LOCALMSPDIR=/var/hyperledger/orderer/msp
      # enabled TLS 这里不适用TLS所以下面TLS的配置都删除了
      - ORDERER_GENERAL_TLS_ENABLED=false
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric
    command: orderer
    #映射了区块文件和msp
    volumes:
    - ../channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
    - ../crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/msp:/var/hyperledger/order
er/msp    - ../crypto-config/ordererOrganizations/example.com/orderers/orderer.example.com/tls/:/var/hyperledger/orde
rer/tls    ports:
      - 7050:7050

  peer0.org1.example.com:
    container_name: peer0.org1.example.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer0.org1.example.com
      - CORE_PEER_ADDRESS=peer0.org1.example.com:7051
      - CORE_PEER_CHAINCODELISTENADDRESS=peer0.org1.example.com:7052
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.org1.example.com:7051
      - CORE_PEER_LOCALMSPID=Org1MSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp:/etc/hyperledger
/fabric/msp        - ../crypto-config/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls:/etc/hyperledger
/fabric/tls    ports:
      - 7051:7051
      - 7052:7052
      - 7053:7053

  peer1.org1.example.com:
    container_name: peer1.org1.example.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer1.org1.example.com
      - CORE_PEER_ADDRESS=peer1.org1.example.com:7051
      - CORE_PEER_CHAINCODELISTENADDRESS=peer1.org1.example.com:7052
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.org1.example.com:7051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org1.example.com:7051
      - CORE_PEER_LOCALMSPID=Org1MSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/msp:/etc/hyperledger
/fabric/msp        - ../crypto-config/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls:/etc/hyperledger
/fabric/tls
    ports:
      - 8051:7051
      - 8052:7052
      - 8053:7053

  peer0.org2.example.com:
    container_name: peer0.org2.example.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer0.org2.example.com
      - CORE_PEER_ADDRESS=peer0.org2.example.com:7051
      - CORE_PEER_CHAINCODELISTENADDRESS=peer0.org2.example.com:7052
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.org2.example.com:7051
      - CORE_PEER_LOCALMSPID=Org2MSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/msp:/etc/hyperledger
/fabric/msp        - ../crypto-config/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls:/etc/hyperledger
/fabric/tls    ports:
      - 9051:7051
      - 9052:7052
      - 9053:7053

  peer1.org2.example.com:
    container_name: peer1.org2.example.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer1.org2.example.com
      - CORE_PEER_ADDRESS=peer1.org2.example.com:7051
      - CORE_PEER_CHAINCODELISTENADDRESS=peer1.org2.example.com:7052
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.org2.example.com:7051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org2.example.com:7051
      - CORE_PEER_LOCALMSPID=Org2MSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/msp:/etc/hyperledger
/fabric/msp        - ../crypto-config/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls:/etc/hyperledger
/fabric/tls    ports:
      - 10051:7051
      - 10052:7052
      - 10053:7053

``````

