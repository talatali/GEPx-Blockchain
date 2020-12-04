# GEPx-Blockchain
Welcome to the Blockchain hands-on demo. This guide walks through the steps needed to complete the prerequisite installation as well as the main demo activities.

How to read this guide:
The green code snippets are to be copy-pasted into the command line interface (note - '+' sign indiactes command to be executed please copy commands without '+' sign)


## Use Case - GE Power Exchange 

- This is simple power exchange model which demonstrates volume based power exchange settlement.

## Steps to run application

### Installing Pre-requisites 
(note - '+' sign indiactes command to be executed please copy commands without '+' sign)

#1 SSH into your VM

- Open command promt from the folder where your .pem file is stored

- ssh into your VM
```diff
+ ssh -i <pemfile>.pem ec2-user@<public-ip>
```

#1 Create a file install.sh and copy paste following contents to it

Create and open file
```diff
+ vi install.sh
```

Copy this and paste it to the file by right clicking on command promt
```
sudo yum install docker
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -a -G docker ec2-user
sudo chmod 666 /var/run/docker.sock
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo yum install git
curl https://dl.google.com/go/go1.15.2.linux-amd64.tar.gz --output go1.15.2.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.15.2.linux-amd64.tar.gz
sudo yum install java-1.8.0-openjdk
sudo yum install -y gcc-c++ make
curl -sL https://rpm.nodesource.com/setup_12.x | sudo -E bash -
sudo yum install -y nodejs
```

Make sure you don't miss any letter from above contents! 
If any letter is missed you can input it
```
Press I key on keyboard
Using arrow keys go to place where you want to input the missing letter
Type your missing letter
```

Save and close file
```
Press Esc key
type :wq!
```

#2 Give execute permissions to the file

```diff
+ chmod +x install.sh
```

#3 Execute install.sh

```diff
+ ./insatll.sh
```
While installing promt will ask for inputs. Give 'Y' to all.

#4 Give permissions

```diff
+ sudo chmod 777 /usr/local/bin/docker-compose
+ sudo chmod 777 /usr/local/go/pkg
+ sudo chmod 777 /usr/local/go/src
```

#5 Set Environment Variables
- Open .bash_profile file
```diff
+ vi .bash_profile
```
- Copy paste following to the file
```
export PATH=$PATH:/usr/local/go/bin
export GOPATH=/usr/local/go
```
- Save the file
```
press Esc key
:wq!
```
- Exit from VM
```
exit
```
- Restart the VM
```diff
+ ssh -i <pemfile>.pem ec2-user@<public-ip>
```

Now we are done with prerequisite installation!

### Installing Hyperledger Fabric

#1 Goto go/src directory
```diff
+ cd $GOPATH/src
```

#2 Run following command
```diff
+ sudo curl -sSL https://bit.ly/2ysbOFE | bash -s
```
This will install hyperledger fabric including [fabric-samples](https://github.com/hyperledger/fabric-samples) folder which we will use for creating test-network with CA.

### Cloning this repository

#1 cd into fabric-sample directory
```diff
+ cd $GOPATH/src/fabric-samples
```

#2 Clone this repo using following command
```diff
+ git clone https://github.com/GE-Blockchain-Demo/GEPx-Blockchain.git
```
(Make sure you clone this repository inside fabric-samples!)

### Create channel with CA

#1 Goto test-network directory
```diff
+ cd $GOPATH/src/fabric-samples/test-network
```
If you are already in fabric-samples use
```
cd test-network
```

#2 Create Channel with CA
```diff
+ ./network.sh up createChannel -ca
```
This will create a channel to which we will add two nodes(org1 and org2) to the network for POC

### Deploy Smart-Contract on channel

Make sure you are in test-network directory and run following command
```diff
+ ./network.sh deployCC -ccn gepx -ccp ../GEPx-Blockchain/chaincode-go/ -ccep "OR('Org1MSP.peer','Org2MSP.peer')"
```
This will add org1 and org2 to the channel and deploy chaincode named gepx on them. By default it will chreate one peer per org.

Output -
```
2020-12-02 05:09:23.306 UTC [chaincodeCmd] ClientWait -> INFO 001 txid [8e1e1414b2f0e2d2891ea5f8258b9d814a7ae414437f666632fa1f299a505f39] committed with status (VALID) at localhost:7051
2020-12-02 05:09:23.314 UTC [chaincodeCmd] ClientWait -> INFO 002 txid [8e1e1414b2f0e2d2891ea5f8258b9d814a7ae414437f666632fa1f299a505f39] committed with status (VALID) at localhost:9051
Chaincode definition committed on channel 'mychannel'
Using organization 1
Querying chaincode definition on peer0.org1 on channel 'mychannel'...
Attempting to Query committed status on peer0.org1, Retry after 3 seconds.
+ peer lifecycle chaincode querycommitted --channelID mychannel --name gepx
+ res=0
Committed chaincode definition for chaincode 'gepx' on channel 'mychannel':
Version: 1.0, Sequence: 1, Endorsement Plugin: escc, Validation Plugin: vscc, Approvals: [Org1MSP: true, Org2MSP: true]
Query chaincode definition successful on peer0.org1 on channel 'mychannel'
Using organization 2
Querying chaincode definition on peer0.org2 on channel 'mychannel'...
Attempting to Query committed status on peer0.org2, Retry after 3 seconds.
+ peer lifecycle chaincode querycommitted --channelID mychannel --name gepx
+ res=0
Committed chaincode definition for chaincode 'gepx' on channel 'mychannel':
Version: 1.0, Sequence: 1, Endorsement Plugin: escc, Validation Plugin: vscc, Approvals: [Org1MSP: true, Org2MSP: true]
Query chaincode definition successful on peer0.org2 on channel 'mychannel'
Chaincode initialization is not required
```

### Running Application

To run the POC application goto GEPx-Blockchain/application-javascript
```diff
+ cd $GOPATH/src/fabric-samples/GEPx-Blockchain/application-javascript
```

To make sure all dependecies are installed run
```diff
+ npm install
```

This should give output as
```
found 0 vulnerabilities
```

Now we are ready to run! 
Follow the steps given below

#1 Enroll network nodes

Command to run-
```diff
+ node enrollNode.js <node>
```

Output - Enroll node1
```diff
+ $ node enrollNode.js org1

--> Enrolling the Org1 CA admin
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a CA Client named ca-org1
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1
Successfully enrolled admin user and imported it into the wallet
```

Output - Enroll node2
```diff
+ $ node enrollNode.js org2

--> Enrolling the Org2 CA admin
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a CA Client named ca-org2
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2
Successfully enrolled admin user and imported it into the wallet
```

#2 Register Admin

command to run -
```diff
+ node registerEnrollUser.js <node> <Admin>
```

Output - 
```diff
+ $ node registerEnrollUser.js org1 adminuser

--> Register and enrolling new user
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a CA Client named ca-org1
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1
Successfully registered and enrolled user adminuser and imported it into the wallet
```

#3 Create Session - This will initialize the session for bidding

Command to run -
```diff
+ node createSession.js <node> <Admin> <sessionID>
```

Output - 
```diff
+ $ node createSession.js org1 adminuser session1
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Submit Session: Propose a new session
*** Result: committed

--> Evaluate Session: query the session that was just created
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP"
  ],
  "privateBids": {},
  "finalizedBids": {},
  "status": "Open"
}
```

#4 Register companies for bidding

Command to run -
```diff
+ node registerEnrollUser.js <node> <companyID>
```

Output - Register company1 to node1
```diff
+ $ node registerEnrollUser.js org1 company1

--> Register and enrolling new user
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a CA Client named ca-org1
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1
Successfully registered and enrolled user company1 and imported it into the wallet
```

Output - Register company2 to node1
```diff
+ $ node registerEnrollUser.js org1 company2

--> Register and enrolling new user
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a CA Client named ca-org1
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1
Successfully registered and enrolled user company2 and imported it into the wallet
```

Output - Register company3 to node2
```diff
+ $ node registerEnrollUser.js org2 company3

--> Register and enrolling new user
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a CA Client named ca-org2
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2
Successfully registered and enrolled user company3 and imported it into the wallet
```

Output - Register company4 to node2
```diff
+ $ node registerEnrollUser.js org2 company4

--> Register and enrolling new user
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a CA Client named ca-org2
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2
Successfully registered and enrolled user company4 and imported it into the wallet
```

#5 Create and submit bid - This is used to place bid in particular session

Command to run -
1. Create bid
```diff
+ node bid.js <node> <companyID> <sessionID> <Volume> <bidType(sell/buy)>
```

2. Submit bid
```diff
+ node submitBid.js <node> <companyID> <sessionID> <BidID(generated by above command)>
```

Output - Create bid for company1
```diff
+ $ node bid.js org1 company1 session1 1000 sell
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Evaluate Transaction: get your client ID
*** Result:  Bidder ID is eDUwOTo6Q049Y29tcGFueTEsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=

--> Submit Session: Create the bid that is stored in your organization's private data collection
*** Result: committed
+ *** Result ***SAVE THIS VALUE*** BidID: d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a

--> Evaluate Session: read the bid that was just created
*** Result:  Bid: {
  "bidType": "sell",
  "volume": 1000,
  "org": "Org1MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTEsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
  "status": "Placed"
}
```

Output - Submit bid for company1
```diff
+ $ node submitBid.js org1 company1 session1 d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Evaluate Session: query the session you want to join

--> Submit Session: add bid to the session

--> Evaluate Session: query the session to see that our bid was added
*** Result: session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {},
  "status": "Open"
}
```

Output - Create bid for company2
```diff
+ $ node bid.js org1 company2 session1 700 sell
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Evaluate Transaction: get your client ID
*** Result:  Bidder ID is eDUwOTo6Q049Y29tcGFueTIsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=

--> Submit Session: Create the bid that is stored in your organization's private data collection
*** Result: committed
+ *** Result ***SAVE THIS VALUE*** BidID: 40cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09

--> Evaluate Session: read the bid that was just created
*** Result:  Bid: {
  "bidType": "sell",
  "volume": 700,
  "org": "Org1MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTIsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
  "status": "Placed"
}
```

Output - Submit bid for company2
```diff
+ $ node submitBid.js org1 company2 session1 40cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Evaluate Session: query the session you want to join

--> Submit Session: add bid to the session

--> Evaluate Session: query the session to see that our bid was added
*** Result: session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {},
  "status": "Open"
}
```

Output - create bid for company3
```diff
+ $ node bid.js org2 company3 session1 600 buy
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2

--> Evaluate Transaction: get your client ID
*** Result:  Bidder ID is eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==

--> Submit Session: Create the bid that is stored in your organization's private data collection
*** Result: committed
+ *** Result ***SAVE THIS VALUE*** BidID: aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250

--> Evaluate Session: read the bid that was just created
*** Result:  Bid: {
  "bidType": "buy",
  "volume": 600,
  "org": "Org2MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
  "status": "Placed"
}
```

Output - Submit bid for company3
```diff
+ $ node submitBid.js org2 company3 session1 aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2

--> Evaluate Session: query the session you want to join

--> Submit Session: add bid to the session

--> Evaluate Session: query the session to see that our bid was added
*** Result: session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {},
  "status": "Open"
}
```

Output - create bid for company4
```diff
+ $ node bid.js org2 company4 session1 700 buy
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2

--> Evaluate Transaction: get your client ID
*** Result:  Bidder ID is eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==

--> Submit Session: Create the bid that is stored in your organization's private data collection
*** Result: committed
+ *** Result ***SAVE THIS VALUE*** BidID: cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419

--> Evaluate Session: read the bid that was just created
*** Result:  Bid: {
  "bidType": "buy",
  "volume": 700,
  "org": "Org2MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
  "status": "Placed"
}
```

Output - Submit bid for company4
```diff
+ $ node submitBid.js org2 company4 session1 cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2

--> Evaluate Session: query the session you want to join

--> Submit Session: add bid to the session

--> Evaluate Session: query the session to see that our bid was added
*** Result: session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {},
  "status": "Open"
}
```

#6 Close Session - This should be used at end of time slot of session. Once the session is closed no more bidding can be done

Command to run -

```diff
+ node closeSession.js <node> <adminID> <sessionID>
```

Output -
```diff
+ $ node closeSession.js org1 adminuser session1
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Submit Session: close session
*** Result: committed

--> Evaluate Session: query the updated session
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {},
  "status": "Close"
}
```

#7 Finalize Bid - This will finalize the bid placed by companies. Only finalized bids will be considered for approval

Command to run -
```diff
+ node finalizeBid.js <node> <comapnyID> <sessionID> <bidID>
```

Output - Finalized bid for company4
```diff
+ $ node finalizeBid.js org2 company4 session1 cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2

--> Evaluate Session: read your bid
*** Result:  Bid: {
  "bidType": "buy",
  "volume": 700,
  "org": "Org2MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
  "status": "Placed"
}

--> Evaluate Session: query the transaction to see that our bid was added
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "bidType": "buy",
      "volume": 700,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    }
  },
  "status": "Close"
}
```

Output - Finalized bid for company3
```diff
+ $ node finalizeBid.js org2 company3 session1 aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2

--> Evaluate Session: read your bid
*** Result:  Bid: {
  "bidType": "buy",
  "volume": 600,
  "org": "Org2MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
  "status": "Placed"
}

--> Evaluate Session: query the transaction to see that our bid was added
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "bidType": "buy",
      "volume": 600,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "bidType": "buy",
      "volume": 700,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    }
  },
  "status": "Close"
}
```

Output - Finalized bid for company2
```diff
+ $ node finalizeBid.js org1 company2 session1 40cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Evaluate Session: read your bid
*** Result:  Bid: {
  "bidType": "sell",
  "volume": 700,
  "org": "Org1MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTIsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
  "status": "Placed"
}

--> Evaluate Session: query the transaction to see that our bid was added
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "bidType": "sell",
      "volume": 700,
      "org": "Org1MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTIsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
      "status": "Finalized"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "bidType": "buy",
      "volume": 600,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "bidType": "buy",
      "volume": 700,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    }
  },
  "status": "Close"
}
```

Output - Finalized bid for company1
```diff
+ $ node finalizeBid.js org1 company1 session1 d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Evaluate Session: read your bid
*** Result:  Bid: {
  "bidType": "sell",
  "volume": 1000,
  "org": "Org1MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTEsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
  "status": "Placed"
}

--> Evaluate Session: query the transaction to see that our bid was added
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "bidType": "sell",
      "volume": 700,
      "org": "Org1MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTIsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
      "status": "Finalized"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "bidType": "buy",
      "volume": 600,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "bidType": "buy",
      "volume": 700,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "bidType": "sell",
      "volume": 1000,
      "org": "Org1MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTEsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
      "status": "Finalized"
    }
  },
  "status": "Close"
}
```

#8 End Session - This will evaluate the bids and provide approval

Command to run -
```diff
+ node endSession.js <node> <adminID> <sessionID>
```

Output -
```diff
+ $ node endSession.js org1 adminuser session1
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Submit the session to end the session
*** Result: committed

--> Evaluate Session: query the updated session
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "bidType": "sell",
      "volume": 700,
      "org": "Org1MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTIsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
      "status": "Approved"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "bidType": "buy",
      "volume": 600,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Approved"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "bidType": "buy",
      "volume": 700,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Approved"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "bidType": "sell",
      "volume": 1000,
      "org": "Org1MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTEsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
      "status": "Partially Approved"
    }
  },
  "status": "ended"
} 

```
### Deleting Database

Once we are done with all execution we can clean up our database using following command

```diff
+ rm -rf wallet
```

### Shut down network

- Go to test-network directory
```diff
+ cd $GOPATH/src/fabric-samples/test-network
```
- Shut down the network
```diff
+ ./network.sh down
```
