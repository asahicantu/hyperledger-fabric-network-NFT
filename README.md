# 1. hyperledger-fabric-network-NFT

This is the first implementation of my personal project to create a hyperledger framework with IPFS permissioned network as a datalake managed by nft among organizations.
Consensus and participation workflow is provided here.

## 1.1. Generalities

* Hyperledger version used: v2.4.3 [Use this link to download the latest version](https://github.com/hyperledger/fabric/releases/tag/v2.4.3)

## 1.2. Steps

1. Extract the downloaded hyperledger file and add the source to your environment variables

```bash
mkdir fabric
cd fabric
tar -xzf hyperledger-fabric-darwin-amd64-2.4.3.tar.gz
export PATH=$PATH:$(pwd)/linux/bin
```

2. Generate Crypto-config profiles. Creates organizations certificates

```bash
cryptogen generate --config=./crypto-config.yaml
```

3. Generate a genesis block. This command uses [configtx.yaml](/configtx.yaml) file with profile 'NetGenesis'

```bash
mkdir channel-artifacts
configtxgen -profile NetGenesis -outputBlock ./channel-artifacts/genesis.block -channelID system-channel
```

4. Create a Channel

```bash
configtxgen -profile FourOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel1.tx -channelID channel1

peer channel create -o localhost:7050  --ordererTLSHostnameOverride orderer.example.com -c channel1 -f ./channel-artifacts/channel1.tx --outputBlock ./channel-artifacts/channel1.block --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
```

osnadmin channel join --channelID $CHANNEL_NAME --config-block ./channel-artifacts/${CHANNEL_NAME}.block -o localhost:7053 --ca-file "$ORDERER_CA" --client-cert "$ORDERER_ADMIN_TLS_SIGN_CERT" --client-key "$ORDERER_ADMIN_TLS_PRIVATE_KEY" >&log.txt