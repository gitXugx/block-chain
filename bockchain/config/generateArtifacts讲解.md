### 1. generateArtifacts讲解


#### 1.1 generateArtifacts.sh 

``````shell
#!/bin/bash +x
#
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#


#set -e

CHANNEL_NAME=$1
: ${CHANNEL_NAME:="mychannel"}
echo $CHANNEL_NAME

export FABRIC_ROOT=$PWD/../..
export FABRIC_CFG_PATH=$PWD
echo

OS_ARCH=$(echo "$(uname -s|tr '[:upper:]' '[:lower:]'|sed 's/mingw64_nt.*/windows/')-$(uname -m | sed 's/x86_64
/amd64/g')" | awk '{print tolower($0)}')
## Using docker-compose template replace private key file names with constants
function replacePrivateKey () {
	ARCH=`uname -s | grep Darwin`
	if [ "$ARCH" == "Darwin" ]; then
		OPTS="-it"
	else
		OPTS="-i"
	fi

	cp docker-compose-e2e-template.yaml docker-compose-e2e.yaml

        CURRENT_DIR=$PWD
        cd crypto-config/peerOrganizations/org1.example.com/ca/
        PRIV_KEY=$(ls *_sk)
        cd $CURRENT_DIR
        sed $OPTS "s/CA1_PRIVATE_KEY/${PRIV_KEY}/g" docker-compose-e2e.yaml
        cd crypto-config/peerOrganizations/org2.example.com/ca/
        PRIV_KEY=$(ls *_sk)
        cd $CURRENT_DIR
        sed $OPTS "s/CA2_PRIVATE_KEY/${PRIV_KEY}/g" docker-compose-e2e.yaml
}

## 2. 编译源码生成工具cryptogen
function generateCerts (){
	CRYPTOGEN=$FABRIC_ROOT/release/$OS_ARCH/bin/cryptogen

	if [ -f "$CRYPTOGEN" ]; then
            echo "Using cryptogen -> $CRYPTOGEN"
	else
	    echo "Building cryptogen"
	    make -C $FABRIC_ROOT release
	fi

    # 3.执行 cryptogen  generate命令根据crypto-config.yaml生成对应的证书msp
	$CRYPTOGEN generate --config=./crypto-config.yaml
	echo
}

## Generate orderer genesis block , channel configuration transaction and anchor peer update transactions
function generateChannelArtifacts() {

	CONFIGTXGEN=$FABRIC_ROOT/release/$OS_ARCH/bin/configtxgen
	if [ -f "$CONFIGTXGEN" ]; then
            echo "Using configtxgen -> $CONFIGTXGEN"
	else
	    echo "Building configtxgen"
	    make -C $FABRIC_ROOT release
	fi

    #生成创世块
	$CONFIGTXGEN -profile TwoOrgsOrdererGenesis -outputBlock ./channel-artifacts/genesis.block

    #生成channl 配置区块
	$CONFIGTXGEN -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID $
CHANNEL_NAME

    #生成 组织的锚节点配置
	$CONFIGTXGEN -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx -ch
annelID $CHANNEL_NAME -asOrg Org1MSP
	$CONFIGTXGEN -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors.tx -ch
annelID $CHANNEL_NAME -asOrg Org2MSP	echo
}

#1. 生成证书
generateCerts
#替换密钥
replacePrivateKey
#生成
generateChannelArtifacts

``````


