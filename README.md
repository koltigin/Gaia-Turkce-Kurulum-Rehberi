# Cosmos HUB Gaia Türkçe Kurulum Rehberi 
<div align="center">
  <img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F643ab9b2-4062-4367-99f6-59c0f7507e22%2FGroup_1041.png?table=block&id=23fcc36a-07b6-46a5-84d6-2f4a5569cd6e&spaceId=2fa227ac-dee8-4655-ad64-fe3c359def5a&width=250&userId=b57d8bef-0356-4537-8ae8-7251a098e5b3&cache=v2" width="100" height="100" />
  
  <hr height="0.5px" />
  
  <br/>
  
  <h1> Cosmos Hub (Gaia) </h1>
  <p> Cosmos Hub, Cosmos Ağı'nı oluşturan, sürekli artan sayıda birbirine bağlı blok zincirlerinin ilkidir. </p>
  
  <a href="https://twitter.com/cosmoshub"><img src="https://img.shields.io/badge/Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white" alt="Tweet" height="20"/></a>
  <br/>
</div>

<hr/>

<div align="center">
  <img src="https://miro.medium.com/max/2000/1*DHtmSfS_Efvuq8n2LAnhkA.png" />
</div>

<br/>


### 🤔 — Cosmos Hub'la Neden İlgilenmelisiniz?
___

Cosmos Hub, [Cosmos SDK](https://github.com/cosmos/cosmos-sdk) kullanılarak oluşturulur ve `gaiad` (Gaia Daemon) adlı bir ikili dosyada derlenir. Cosmos Hub ve diğer tamamen bağımsız Cosmos SDK blok zincirleri, Blok Zincirler Arası İletişimi sağlayan [IBC](https://github.com/cosmos/ibc) adlı bir protokol kullanarak birbirleriyle etkileşime girer. Cosmos Hub'ın ne olduğunu anlamak için bu [giriş açıklamasını](https://hub.cosmos.network/main/hub-overview/overview.html) okuyabilirsiniz.


|  |  |  |  |  |
| --- | --- | --- | --- | --- |
| [![codecov](https://codecov.io/gh/cosmos/gaia/branch/master/graph/badge.svg)](https://codecov.io/gh/cosmos/gaia) | [![Go Report Card](https://goreportcard.com/badge/github.com/cosmos/gaia)](https://goreportcard.com/report/github.com/cosmos/gaia) | [![license](https://img.shields.io/github/license/cosmos/gaia.svg)](https://github.com/cosmos/gaia/blob/main/LICENSE) | [![LoC](https://tokei.rs/b1/github/cosmos/gaia)](https://github.com/cosmos/gaia) | [![GolangCI](https://golangci.com/badges/github.com/cosmos/gaia.svg)](https://golangci.com/r/github.com/cosmos/gaia) |


<br/>
<br/>

# Gaia Türkçe Node Kurulum Rehberi

## NOT
Eğer Stride kurulu bir sunucuya kurulum yapacaksanız `Sistemi Güncelleme`, `Gerekli Kütüphanelerin Kurulması` ve `Go Kurulumu` adımlarını atlayabilirsiniz.

## Root Yetkisi Alma ve Root Dizinine Geçme
```shell
sudo su
cd $HOME
```

## Sistemi Güncelleme
```shell
apt update && sudo apt upgrade -y
```

## Gerekli Kütüphanelerin Kurulması
```shell
apt install curl make build-essential gcc tmux jq chrony htop -y < "/dev/null"
```

## Go Kurulumu
```shell
ver="1.19"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
rm -rf /usr/local/go
tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm -rf "go$ver.linux-amd64.tar.gz"
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```

## Değişkenleri Yükleme
aşağıda değiştirmeniz gereken yerleri yazıyorum.
* `$GAIA_NODENAME` validator adınız
* `$GAIA_WALLET` cüzdan adınız
```shell
echo "export GAIA_NODENAME=$GAIA_NODENAME"  >> $HOME/.bash_profile
echo "export GAIA_WALLET=$GAIA_WALLET" >> $HOME/.bash_profile
echo "export GAIA_PORT=23" >> $HOME/.bash_profile
echo "export GAIA_CHAIN_ID=GAIA" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
Yukarıdaki kodlarda değişiklik yaptığınızda aşağıdaki gibi görünmeli.
```shell
echo "export GAIA_NODENAME=Bilge"  >> $HOME/.bash_profile
echo "export GAIA_WALLET=Bilge" >> $HOME/.bash_profile
echo "export GAIA_PORT=23" >> $HOME/.bash_profile
echo "export GAIA_CHAIN_ID=GAIA" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Gaia Kurulumu
```shell
cd $HOME
git clone https://github.com/Stride-Labs/gaia.git
cd gaia
git checkout 5b47714dd5607993a1a91f2b06a6d92cbb504721
make build
cp $HOME/gaia/build/gaiad /usr/local/bin
```

## Uygulamayı Yapılandırma
```shell
gaiad config chain-id $GAIA_CHAIN_ID
gaiad config keyring-backend test
gaiad config node tcp://localhost:${GAIA_PORT}657
```

## Uygulamayı Başlatma
```shell
gaiad init $GAIA_NODENAME --chain-id $GAIA_CHAIN_ID
```

## Genesis ve Addrbook Dosyalarının İndirilmesi
```shell
curl https://raw.githubusercontent.com/mmc6185/node-testnets/main/stride/GAIA/genesis.jso > ~/.gaia/config/genesis.json
curl https://raw.githubusercontent.com/mmc6185/node-testnets/main/stride/GAIA/addrbook.json > ~/.gaia/config/addrbook.json
```

## Minimum GAS Ücretinin Ayarlanması
```shell
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uatom\"/" $HOME/.gaia/config/app.toml
```

## SEED ve PEERS Ayarlanması
```shell
SEEDS=""
PEERS="ec829f12f6d513384e6c5ae79220455e0d80163c@104.208.67.175:23656,88f9b658a77a1ed7376adbc6d0584da8c1a35f6f@176.124.213.56:23656,b8948a13a8953f864ff43fa31ede14a21e44efdc@88.208.57.200:26656,b3dee7da18fc03c8f9481bad25a06138c7badd8c@86.48.2.74:23656,b7716bc446bd0c636ccb343c408065af71fbb576@159.65.20.94:23656,4f0e774fdf629771045fc95e74145d04e899af92@134.122.96.36:23656,a3720d1999a88056ef74fdb923e27dfd9c24c01d@40.114.118.113:23656,8f7058c8d3ba5b889c9895ed4525cb89e64f0a8b@75.119.133.19:23656,b767515dca0be232fc287e0d274831a8c80fcac7@5.9.147.22:26256,c3c32094135bc9d9148dbcbac52fdace8d01d62c@51.77.108.119:23656,a3b3668f967de210ae31ce779deed03f91074038@185.249.225.35:23656,d241b443f87c613d8e7039acd64ff7c296166b99@38.242.134.205:23656,8e2cf0c23b69924a8442b8102951778bd5254773@38.242.233.25:23656,4e6ba3223ba24e27eccffede205e4cffcbae903a@38.242.135.66:23656,9c86d46e33566001c89d274e2559932a4e98e406@20.90.88.145:23656,ccadfbc7c6204887edc9a6eba5f9beed78ffe9de@149.102.137.76:23656,c24fecd05c85385aaa84e587557285e7dfe38d54@217.160.207.56:23656,c6dcce40e8b8a00f353a642ef0ee3623a333c067@20.230.133.117:23656,964f3d7398196238acd9e26cc96ad7787c7513f6@45.130.104.89:23656,ff3a2a2022b2d53541efc0403af302eae2775da5@51.159.182.149:23656,6567e116f975eb36be8e15598f10917dab831c35@31.207.44.66:23656,853174f1ca8b78fbbfdefd32af7cc1f3fc424ce3@185.182.187.33:23656,6fd97df135f806249b55789d314b1482df38d366@20.213.53.251:23656,2101d45204248d9a8b825a23950370029d5e136e@195.88.87.43:23656,75c0154117e46f29b1eee482d740f0cc73ef76ed@164.92.80.118:23656,f6149bcd125f8972b0dc333c84cfef6fc3b9b54a@20.193.154.140:23656,6b85c6a0b2cdcf05d0ce5b2f6a78728b510fcb01@131.255.179.4:23656,87c1cebe140dbde04644e62a31af7863fa1b4fc5@157.245.0.168:23656,a64faaf6fe45425352524341d2f390ce6c603c09@139.162.2.113:23656,712f37d4e5f080452759bb6f4c7ed1716270584f@20.25.144.37:23656,23e60781c1e71968d7412cb6f45aa7d5648f2517@52.234.146.133:23656,aa3aa0e1244c0503b6d94d7a2ab4554ba0e3fd79@173.212.233.187:23656,7f248115c0636860cfcdfaec5a20f42bc6d622a2@38.242.222.136:23656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.gaia/config/config.toml
```

## Prometheus'u Aktif Etme
```shell
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.gaia/config/config.toml
```

## Pruning'i Ayarlama
```shell
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.gaia/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.gaia/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.gaia/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.gaia/config/app.toml
```

## Port Ayarlarını Yapılandırma
```shell
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${GAIA_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${GAIA_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${GAIA_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${GAIA_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${GAIA_PORT}660\"%" $HOME/.gaia/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${GAIA_PORT}317\"%; s%^address = \":8080\"%address = \":${GAIA_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${GAIA_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${GAIA_PORT}091\"%" $HOME/.gaia/config/app.toml
```

## Zincir Verilerini Sıfırlama
```shell
gaiad tendermint unsafe-reset-all --home $HOME/.gaia
```

## Servis Dosyası Oluşturma
```shell
tee /etc/systemd/system/gaiad.service > /dev/null <<EOF
[Unit]
Description=gaia
After=network-online.target
[Service]
User=$USER
ExecStart=$(which gaiad) start --home $HOME/.gaia
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

## Servisi Başlatma
```shell
systemctl daemon-reload
systemctl enable gaiad
systemctl restart gaiad
```

## Logları Kontrol Etme
```shell
journalctl -u gaiad -f -o cat
```

## State Sync Kullanma
Bu kod blokları hızlı yakalamamıza yarar. Bağlanması uzun sürebilir. 
```shell
SNAP_RPC1="https://gaia.poolparty.stridenet.co:445" \
&& SNAP_RPC2="https://gaia.poolparty.stridenet.co:445"
LATEST_HEIGHT=$(curl -s $SNAP_RPC2/block | jq -r .result.block.header.height) \
&& BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)) \
&& TRUST_HASH=$(curl -s "$SNAP_RPC2/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC1,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.gaia/config/config.toml
gaiad tendermint unsafe-reset-all --home $HOME/.gaia
systemctl restart gaiad && journalctl -fu gaiad -o cat
```

## Cüzdan Oluşturma

### Yeni Cüzdan Oluşturma
Eğer Stride testnetine katılıyorsanız yeni cüzdan oluşturmanıza gerek yok.
* `$GAIA_WALLET` bölümünü değiştirmiyoruz kurulumun başında cüzdanımıza isim belirledik.
```shell 
gaiad keys add $GAIA_WALLET
```  

### Var Olan Cüzdanı İçeri Aktarma
* `$GAIA_WALLET` bölümünü değiştirmiyoruz kurulumun başında cüzdanımıza isim belirledik.
```shell
gaiad keys add $GAIA_WALLET --recover
```


* BU AŞAMADAN SONRA NODE'UMUZUN EŞLEŞMESİNİ BEKLİYORUZ.

## Faucet  Musluk
Test token almak için Discord'da [#token-faucet](https://discord.gg/HXgZSzstTV) kanalından şu şekilde `$faucet-atom:CUZDAN_ADRESINIZ` mesaj atıyoruz.

## Cüzdan Bakiyesini Kontrol Etme
```shell
gaiad query bank balances CUZDAN_ADRESINIZ --chain-id $GAIA_CHAIN_ID
```  

## Senkronizasyonu Kontrol Etme
`false` çıktısı almaldıkça bir sonraki yani validator oluşturma adımına geçmiyoruz.
```shell
gaiad status 2>&1 | jq .SyncInfo
```

## Validator Oluşturma
 Aşağıdaki komutta aşağıda berlittiğim yerler dışında bir değişikli yapmanız gerekmez;
   'identity'  buraya `httpskeybase.io` sitesine üye olarak size verilen kimlik numaranızı yazıyorsunuz.
   'details'  kendiniz hakkında bilgiler verebilir ya da `Rues Community Supporter` yazabilirsiniz.
   'website'  Varsa bir siteniz yazabilirsiniz ya da `httpsforum.rues.info` olarak bırakabilirsiniz.
   'security-contact'  E-posta adresiniz.
```shell 
gaiad tx staking create-validator \
 --commission-max-change-rate=0.01 \
 --commission-max-rate=0.2 \
 --commission-rate=0.05 \
 --amount 1000000uatom \
 --pubkey=$(gaiad  tendermint show-validator) \
 --moniker=$GAIA_NODENAME \
 --chain-id=$GAIA_CHAIN_ID \
 --details=Rues Community Supporter \
 --security-contact=E-POSTANIZ \
 --website=httpsforum.rues.info \
 --identity=XXXX1111XXXX1111 \
 --min-self-delegation=1000000 \ 
 --from=$GAIA_WALLET
 ```  


## Validator Linkinizi Paylaşma
Sei Discord [#role-request](https://discord.gg/HXgZSzstTV) kanalından validatorumuze ait [explorer](https://stride.explorers.guru) linkini gönderiyoruz.

## DAHA FAZLA SORUNUZ VARSA STRIDE TÜRKİYE TELEGRAM GRUBU

[Stride Türkiye Telegram Sayfası](httpst.meTeritoriTurkish)

## FAYDALI KOMUTLAR

### Logları Kontrol Etme 
```shell
journalctl -fu gaiad -o cat
```

### Sistemi Başlatma
```shell
systemctl start gaiad
```

### Sistemi Durdurma
```shell
systemctl stop gaiad
```

### Sistemi Yeniden Başlatma
```shell
systemctl restart gaiad
```

### Node Senkronizasyon Durumu
```shell
gaiad status 2>&1 | jq .SyncInfo
```

### Validator Bilgileri
```shell
gaiad status 2>&1 | jq .ValidatorInfo
```

### Node Bilgileri
```shell
gaiad status 2>&1 | jq .NodeInfo
```

### Node ID Öğrenme
```shell
gaiad tendermint show-node-id
```

### Node IP Adresini Öğrenme
```shell
curl icanhazip.com
```

### Peer Adresinizi Öğrenme
```shell
echo $(gaiad tendermint show-node-id)@$(curl ifconfig.me):${GAIA_PORT}656
```

### Cüzdanların Listesine Bakma
```shell
gaiad keys list
```

### Cüzdanı İçeri Aktarma
```shell
gaiad keys add $GAIA_WALLET --recover
```

### Cüzdanı Silme
```shell
gaiad keys delete CUZDAN_ADI
```

### Cüzdan Bakiyesine Bakma
```shell
gaiad query bank balances CUZDAN_ADRESI
```

### Bir Cüzdandan Diğer Bir Cüzdana Transfer Yapma
```shell
gaiad tx bank send CUZDAN_ADRESI GONDERILECEK_CUZDAN_ADRESI 100000000uatom
```

### Proposal Oylamasına Katılma
```shell
gaiad tx gov vote 1 yes --from $GAIA_WALLET --chain-id=$GAIA_CHAIN_ID
```

### Validatore Stake Etme  Delegate Etme
```shell
gaiad tx staking delegate $VALOPER_ADDRESS 100000000utoi --from=$GAIA_WALLET --chain-id=$GAIA_CHAIN_ID  --gas=auto
```

### Mevcut Validatorden Diğer Validatore Stake Etme  Redelegate Etme
```shell
gaiad tx staking redelegate MevcutValidatorAdresi StakeEdilecekYeniValidatorAdresi 100000000uatom --from=$GAIA_WALLET --chain-id=$GAIA_CHAIN_ID  --gas=auto
```

### Ödülleri Çekme
```shell
gaiad tx distribution withdraw-all-rewards --from=$GAIA_WALLET --chain-id=$GAIA_CHAIN_ID  --gas=auto
```

### Komisyon Ödüllerini Çekme
```shell
gaiad tx distribution withdraw-rewards VALIDATOR_ADRESI --from=$GAIA_WALLET --commission --chain-id=$GAIA_CHAIN_ID
```

### Validator İsmini Değiştirme
```shell
gaiad tx staking edit-validator 
--moniker=YENI_NODE_ADI 
--chain-id=$GAIA_CHAIN_ID 
--from=$GAIA_WALLET
```

### Validatoru Jail Durumundan Kurtarma 
```shell
gaiad tx slashing unjail 
  --broadcast-mode=block 
  --from=$GAIA_WALLET 
  --chain-id=$GAIA_CHAIN_ID
  --gas=auto
```

### Node'u Tamamen Silme 
```shell
systemctl stop gaiad && 
systemctl disable gaiad && 
rm etc/systemd/system/gaiad.service && 
systemctl daemon-reload && 
cd $HOME && 
rm -rf .gaia gaia && 
rm -rf $(which gaiad)
```


### Hesaplar

[Linktree](https://linktr.ee.mehmetkoltigin)

[Twitter](https://twitter.com.mehmetkoltigin)

### Komunite 
[Forum Rues](https://forum.rues.infoindex.php)

[Telegram Rues Announcement](https://t.meRuesAnnouncement)

[Telegram Rues Chat](https://t.meRuesChat)

[Telegram Rues Node](https://t.meRuesNode)

[Telegram Rues Node Chat](https://t.meRuesNodeChat)


### ⚡ — Documentation & Introduction
___

Cosmos Hub is a blockchain network that operates on Proof-of-Stake consensus. You can find an introduction to the Cosmos Hub and how to use the `gaiad` binary as a delegator, validator or node operator as well as how governance on the Cosmos Hub works in the [documentation](https://hub.cosmos.network/main/hub-overview/overview.html). 

Alternatively, whether you're new to blockchain technology or interested in getting involed, the Cosmos Network [Course](https://tutorials.cosmos.network/academy/0-welcome/) will guide you through everything. The course walks you through the basics of blockchain technology, to staking, setting up your own node, and beyond.




<br/>
<br/>


### 👤 — Node Operators
___
If you're interested in running a node on the current Cosmos Hub, check out the docs to [Join the Cosmos Hub Mainnet](https://github.com/cosmos/gaia/blob/main/docs/hub-tutorials/join-mainnet.md).


<br/>
<br/>


### 🗣️ — Validators
___

If you want to participate and help secure Cosmos Hub, check out becoming a validator. Information on what a validator is and how to participate as one can be found at the [Validator FAQ](https://hub.cosmos.network/main/validators/validator-faq.html#). If you're running a validator node on the Cosmos Hub, reach out to a Janitor on the [Cosmos Developers Discord](https://discord.gg/cosmosnetwork) to join the `#validators-verified` channel.


<br/>
<br/>


### 👥 — Delegators
___

If you still want to participate on the Cosmos Hub, check out becoming a delegator. Information what a delegator is and how to participate as one can be found at the [Delegator FAQ](https://hub.cosmos.network/main/delegators/delegator-faq.html).


<br/>
<br/>

### 👥 — Testnet
___

To participate in or utilize the current Cosmos Hub testnet, take a look at the [cosmos/testnets](https://github.com/cosmos/testnets) repository. This testnet is for the Theta Upgrade expected in Q1 2022. For future upgrades of the Cosmos Hub take a look at the [roadmap](https://github.com/cosmos/gaia/blob/main/docs/roadmap/cosmos-hub-roadmap-2.0.md).


<br/>
<br/>


### 🌐 — Roadmap
___

For an overview of upcoming changes to the Cosmos Hub take a look at the [Roadmap](https://github.com/cosmos/gaia/blob/main/docs/roadmap/cosmos-hub-roadmap-2.0.md).


<br/>
<br/>


### 🗄️ — Archives & Genesis
___

With each version of the Cosmos Hub, the chain is restarted from a new Genesis state. 
Mainnet is currently running as `cosmoshub-4`. Archives of the state of `cosmoshub-1`, `cosmoshub-2`, and `cosmoshub-3` are available [here](./docs/resources/archives.md).

If you are looking for historical genesis files and other data [`cosmos/mainnet`](http://github.com/cosmos/mainnet) is an excellent resource. Snapshots are also available at [cosmos.quicksync.io](https://cosmos.quicksync.io).


<br/>
<br/>


### 🤝 — How to contribute 
___

Check out [contributing.md](CONTRIBUTING.md) for our guidelines & policies for how we develop the Cosmos Hub. Thank you to all those who have contributed!


<br/>
<br/>


### 💬 — Talk to us
___

We have active, helpful communities on Twitter, Discord, and Telegram.

|    |    |
| -- | -- |
| Cosmos Developers Discord | <a href="https://discord.gg/cosmosnetwork"><img src="https://img.shields.io/badge/Discord-7289DA?style=for-the-badge&logo=discord&logoColor=white" alt="Discord" height="30"/></a> |
| Cosmos Twitter | <a href="https://twitter.com/cosmos"><img src="https://img.shields.io/badge/Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white" alt="Tweet" height="30"/></a> |
| Cosmos Telegram | <a href="https://t.me/cosmosproject"><img src="https://img.shields.io/badge/Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white" alt="Telegram" height="30"/></a> |


For updates on the Cosmos Hub team's activities follow us on the [Cosmos Hub Twitter](https://twitter.com/cosmoshub) account.


<br/>
<br/>

### 👏 — Supporters
___

[![Stargazers repo roster for @cosmos/gaia](https://reporoster.com/stars/cosmos/gaia)](https://github.com/cosmos/gaia/stargazers)
[![Forkers repo roster for @cosmos/gaia](https://reporoster.com/forks/cosmos/gaia)](https://github.com/cosmos/gaia/network/members)

<br/>

<p align="center"><a href="https://github.com/nastyox/Rando.js#nastyox"><img src="http://randojs.com/images/barsSmallTransparentBackground.gif" alt="Animated footer bars" width="100%"/></a></p>

<br/>

<p align="center"><a href="https://github.com/cosmos/gaia#"><img src="http://randojs.com/images/backToTopButtonTransparentBackground.png" alt="Back to top" height="29"/></a></p>
