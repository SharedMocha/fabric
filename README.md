How does fabric network wakes up and works

1.)Create 2 files crypro-config.yaml and configtx.yaml files
crypto-config.yaml ->Has info on key orgs and noof peers to start-tHOS IS USED TO TO CREATE CRYPTO MATERIAL aka MSP.
configtx.yam -> Has details on channels and consortium and consensu,timeut,block sizes details

2.)Run the files through below tools
crypto-config.yam -> CRYPTOGEN -> Generates folder structure which has ROOT,ADMIN,CA,SIGNING Certs and Private keys
config.tx yaml file -> CONFIGTXGEN -> Generates 3 things
a.) genesis block for entire network with details to msp of participants
b.)channel.tx file for each channel to be created
c.)anchor peer transactions

3.) Have a cli to connect to each of these and execute commans

Now -> We have the stuff ready -->Lets start it

1.)Everyone in the org generates material using CRYPTOGEN

*/2.)Bring up orderer using CLI by passing geneis block created in 2.a(above)

3.)now connect to any peer get into its bash and execute command to create a chnnale usin channel.tx file ->This give back geneis.block file which has configarations-that will be stored on file system of peers
peer channel create -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/channel.tx --tls $CORE_PEER_TLS_ENABLED --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

4.)Now using the obtained genesis.block file ask other peers to join the channel
 peer channel join -b <channel-ID.block>


5.)Install chaincode on peers
peer chaincode install -n mycc -v 1.0 -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02


6>Send proposal to install chaincode on channel.Here we have 2 steps propose and transact-Send proposal and then start transacting.Here we set endorsemtn policy

/peer chaincode instantiate -o orderer.example.com:7050 --tls $CORE_PEER_TLS_ENABLED --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_NAME -n mycc -v 1.0 -c '{"Args":["init","a", "100", "b","200"]}' -P "OR ('Org1MSP.member','Org2MSP.member')"


7.)Remember chaincode is not reayd until we make init call or query it
8.)Now execute functions in chaincode and its all working

/peer chaincode query -C $CHANNEL_NAME -n mycc -c '{"Args":["query","a"]}'

/peer chaincode invoke -o orderer.example.com:7050  --tls $CORE_PEER_TLS_ENABLED --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem  -C $CHANNEL_NAME -n mycc -c '{"Args":["invoke","a","b","10"]}'


**If anyone wants to use couch db they can**
Its simple as passing couchdb yaml file during network setup to orderer --step */2.) as above

