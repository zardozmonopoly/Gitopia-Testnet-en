<h1 align="center">Gitopia Janus Testnet </h1>

![Gitopia](https://user-images.githubusercontent.com/100621008/201538939-9acf59e9-7395-4ea2-ad82-b2e707a13a33.jpg)

*Gitopia is an open source Web3 application that offers decentralized code collaboration*

*System Requirements*
|  CPU  |    RAM     |     SSD    |  
|-------|------------|------------|
|    4  |   8-16     |    400+    |

*System Update*
```
sudo apt update && sudo apt upgrade -y
```
*Required library installation*
```
sudo apt install curl build-essential git wget jq make gcc tmux chrony -y
```
*Set your validator name*
```
moniker="YourValidatorName"
GITOPIA_CHAIN_ID="gitopia-janus-testnet-2"
```
*Go installation*
```
cd $HOME
wget -O go1.18.4.linux-amd64.tar.gz https://golang.org/dl/go1.18.4.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.18.4.linux-amd64.tar.gz && rm go1.18.4.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
go version
```` 
*Cloning Gitopia and binary file configuration*
```
cd $HOME 
rm -rf gitopia
curl https://get.gitopia.com | bash
git clone -b v1.2.0 gitopia://gitopia/gitopia
cd gitopia 
make install
```
*Version check*

**version: 1.2.0**
```
gitopiad version 
```
*Start*
```
gitopiad init --chain-id "$Gitopia_Chain_ID" "$moniker"
```
*Genesis and addrbook installation*
```
wget -O $HOME/.gitopia/config/addrbook.json "http://65.108.6.45:8000/gitopia/addrbook.json"
wget https://server.gitopia.com/raw/gitopia/testnets/master/gitopia-janus-testnet-2/genesis.json.gz
gunzip genesis.json.gz
mv genesis.json $HOME/.gitopia/config/genesis.json
```
*Seed and Peers arrange*
```
SEEDS="399d4e19186577b04c23296c4f7ecc53e61080cb@seed.gitopia.com:26656"
PEERS=""
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.gitopia/config/config.toml
```
*Open Pruning*
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.gitopia/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.gitopia/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.gitopia/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.gitopia/config/app.toml
```
*Disable Indexer*
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.gitopia/config/config.toml
```
*Arrange gas price*
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.01utlore\"/" $HOME/.gitopia/config/app.toml
```
*Create service file*
```
sudo tee /etc/systemd/system/gitopiad.service > /dev/null <<EOF
[Unit]
Description=gitopia
After=network-online.target
[Service]
User=root
ExecStart=$(which gitopiad) start --home $HOME/.gitopia
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
*Node Initialization*
```
sudo systemctl daemon-reload
sudo systemctl enable gitopiad
sudo systemctl restart gitopiad 
sudo journalctl -u gitopiad -f -o cat
```
*Create wallet*

**Enter your wallet name**

**Don't forget save your mnemonic**
```
gitopiad keys add <yourvalidatorname>
```

![Screenshot_2](https://user-images.githubusercontent.com/100621008/201543404-2d5bf989-3086-430c-82fb-57870b39fe53.jpg)

* Go to [Gitopia](https://gitopia.com/home) 
**and connect with your keplr wallet**

**Import your 24 words mnemonics to your keplr wallet and get lore**

![gitopia](https://user-images.githubusercontent.com/100621008/201544432-2e214c23-f62b-42e1-85e1-f9441889347c.png)

**Make sync info and if you get false then create validator**
```
gitopiad status 2>&1 | jq .SyncInfo
```

*Create validator*

**Customize it yourself**
```
gitopiad tx staking create-validator \
  --amount 1000000utlore \
  --from yournodename \
  --identity=keybase ID \
  --website="yourwebsite" \
  --details="aboutyourself" \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(gitopiad tendermint show-validator) \
  --moniker yournodename \
  --chain-id gitopia-janus-testnet-2
  ```
  ![Screenshot_3](https://user-images.githubusercontent.com/100621008/201545197-f533ce48-d548-43ac-abe5-af77ea43ab10.jpg)
  
  *Useful commands*
  
  **Check logs**
  ```
  journalctl -fu gitopiad -o cat
  ```
  **Start service**
  ```
  sudo systemctl start gitopiad
  ```
  **Stop service**
  ```
  sudo systemctl stop gitopiad
  ```
  **Restart service**
  ```
  sudo systemctl restart gitopiad
  ```
  
  **Synchronization info**
  ```
  gitopiad status 2>&1 | jq .SyncInfo
  ```
  **Validator info**
  ```
  gitopiad status 2>&1 | jq .ValidatorInfo
  ```
  **Node info**
  ```
  gitopiad status 2>&1 | jq .NodeInfo
  ```
  **Node id**
  ```
  gitopiad tendermint show-node-id
  ```
  **List of wallets**
  ```
  gitopiad keys list
  ```
  **Recover wallet**
  ```
  gitopiad keys add walletaddress --recover
  ```
  **Delete wallet**
  ```
  gitopiad keys delete walletaddress
  ```
  **Get wallet balance**
  ```
  gitopiad query bank balances walletaddress
  ```
  **Transfer funds**
  ```
  gitopiad tx bank send yourwalletaddress receiverwalletaddress 10000000utlore
  ```
  **Exit from jail**
  ```
  gitopiad tx slashing unjail \
  --broadcast-mode=block \
  --from=yournodename \
  --chain-id=gitopia-janus-testnet-2 \
  --gas=auto
  ```
  **Delete node**
  ```
sudo systemctl stop gitopiad
sudo systemctl disable gitopiad
sudo rm /etc/systemd/system/gitopia* -rf
sudo rm $(which gitopiad) -rf
sudo rm $HOME/.gitopia* -rf
sudo rm $HOME/gitopia -rf
sed -i '/GITOPIA_/d' ~/.bash_profile
```

* [Gitopia web](https://gitopia.com/)
* [Gitopia explorer](https://explorer.gitopia.com/)
* [Discord](https://discord.gg/Dku9mezt)<br>















  




  

  





  
  
















  









