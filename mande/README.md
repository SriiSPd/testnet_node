# mande

[Join Our Telegram <img src="https://user-images.githubusercontent.com/50621007/183283867-56b4d69f-bc6e-4939-b00a-72aa019d1aea.png" alt="" data-size="line">](https://t.me/BeritaCryptoo) [Follow Our Twitter <img src="https://user-images.githubusercontent.com/108946833/184274157-08210464-fa03-493d-b01c-2420c67a524f.jpg" alt="" data-size="line">](https://twitter.com/BeritaCryptoo)

![](https://user-images.githubusercontent.com/44331529/195984832-4b59ffcb-4253-40ee-9168-edc7bfa7425f.png)

## Mande node setup for testnet

Official documentation:

> * [Validator setup instructions](https://github.com/mande-labs)

Explorer:

> * https://explorer.stavr.tech/mande-chain

### Perangkat Keras

| Komponen    | Persyaratan Minimum |
| ----------- | ------------------- |
| CPU         | 2                   |
| RAM         | 4GB                 |
| Penyimpanan | 100GB               |

### Perangkat Lunak

| Komponen | Persyaratan Minimum |
| -------- | ------------------- |
| OS       | Ubuntu 20.04        |

### Siapkan fullnode Mande Anda

```
wget -O mnd.sh https://raw.githubusercontent.com/xsons/testnet_node/main/mande/mnd.sh && chmod +x mnd.sh && ./mnd.sh
```

### Pasca instalasi

Ketika instalasi selesai, silakan muat variabel ke dalam sistem

```
source $HOME/.bash_profile
```

Selanjutnya Anda harus memastikan validator Anda menyinkronkan blok. Anda dapat menggunakan perintah di bawah ini untuk memeriksa status sinkronisasi

```
mande-chaind status 2>&1 | jq .SyncInfo
```

#### (OPSIONAL) Sinkronisasi Status

```
SNAP_RPC=http://209.182.239.169:28657
peers="bd9929b9a2e8b5ad1581e4b01f85457e0d01cba3@209.182.239.169:28656"
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.mande-chain/config/config.toml
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 500)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.mande-chain/config/config.toml

mande-chaind tendermint unsafe-reset-all --home /root/.mande-chain --keep-addr-book
systemctl restart mande-chaind && journalctl -u mande-chaind -f -o cat
```

#### Buat dompet

Untuk membuat dompet baru Anda dapat menggunakan perintah di bawah ini. Jangan lupa simpan mnemonicnya

```
mande-chaind keys add $WALLET
```

(OPSIONAL) Untuk memulihkan dompet Anda menggunakan frase seed

```
mande-chaind keys add $WALLET --recover
```

Untuk mendapatkan daftar dompet saat ini

```
mande-chaind keys list
```

#### Danai dompet Anda

Untuk membuat validator terlebih dahulu, Anda perlu mendanai dompet Anda dengan token testnet. Anda dapat meminta token testnet dari faucet testnet TIA di [MANDE DISCORD](\[https:/discord.gg/TDBGZT29]\(https:/discord.gg/ZBgHwtWQ\)/)

* \#faucet untuk meminta token uji

Untuk meminta faucet:

```
$request <YOUR_WALLET_ADDRESS> theta
```

Untuk memeriksa saldo dompet:

```
$balance <YOUR_WALLET_ADDRESS>
```

#### Buat validator

Sebelum membuat validator, pastikan Anda memiliki setidaknya 1 Cred (1 Cred sama dengan 1000000 Cred) dan node Anda tersinkronisasi

Untuk memeriksa saldo dompet Anda:

```
mande-chaind query bank balances $MANDE_WALLET_ADDRESS
```

> Jika dompet Anda tidak menunjukkan saldo apa pun, kemungkinan simpul Anda masih disinkronkan. Silahkan tunggu sampai selesai untuk sinkronisasi lalu lanjutkan

Untuk membuat perintah jalankan validator Anda di bawah ini

```
mande-chaind tx staking create-validator \
--chain-id mande-testnet-1 \
--amount 0cred \
--pubkey "$(mande-chaind tendermint show-validator)" \
--from <wallet> \
--moniker="YOUR_MONIKER" \
--fees 1000mand
```

### Keamanan

Untuk melindungi kunci Anda, pastikan Anda mengikuti aturan keamanan dasar

#### Siapkan kunci ssh untuk otentikasi

Tutorial yang bagus tentang cara mengatur kunci ssh untuk otentikasi ke server Anda dapat ditemukan di sini

#### Keamanan Firewall Dasar

Mulailah dengan memeriksa status ufw.

```
sudo ufw status
```

Setel default untuk mengizinkan koneksi keluar, tolak semua yang masuk kecuali ssh dan 26656. Batasi upaya login SSH

```
sudo ufw default allow outgoing
sudo ufw default deny incoming
sudo ufw allow ssh/tcp
sudo ufw limit ssh/tcp
sudo ufw enable
```

### Perintah yang berguna

#### Manajemen Pelayanan

Periksa log

```
journalctl -fu mande-chaind -o cat
```

Memulai layanan

```
sudo systemctl start mande-chaind
```

Hentikan layanan

```
sudo systemctl stop mande-chaind
```

Mulai ulang layanan

```
sudo systemctl restart mande-chaind
```

#### Node info

Informasi sinkronisasi

```
mande-chaind status 2>&1 | jq .SyncInfo
```

Info validator

```
mande-chaind status 2>&1 | jq .ValidatorInfo
```

Node info

```
mande-chaind status 2>&1 | jq .NodeInfo
```

Show node id

```
mande-chaind tendermint show-node-id
```

#### Operasi dompet

Daftar dompet

```
mande-chaind keys list
```

Pulihkan dompet

```
mande-chaind keys add $WALLET --recover
```

Hapus dompet

```
mande-chaind keys delete $WALLET
```

Dapatkan saldo dompet

```
mande-chaind query bank balances $MANDE_WALLET_ADDRESS
```

Transfer dana

```
mande-chaind tx bank send $MANDE_WALLET_ADDRESS <TO_MANDE_WALLET_ADDRESS> 10000000cred
```

#### Pemungutan suara

```
mande-chaind tx gov vote 1 yes --from $WALLET --chain-id=$MANDE_CHAIN_ID
```

#### Staking, Delegasi, dan Hadiah

Delegasikan saham

```
mande-chaind tx staking delegate $MANDE_VALOPER_ADDRESS 10000000cred --from=$WALLET --chain-id=$MANDE_CHAIN_ID --gas=auto
```

Delegasikan ulang stake dari validator ke validator lain

```
mande-chaind tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000cred --from=$WALLET --chain-id=$MANDE_CHAIN_ID --gas=auto
```

Tarik semua hadiah

```
mande-chaind tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$MANDE_CHAIN_ID --gas=auto
```

Tarik hadiah dengan komisi

```
mande-chaind tx distribution withdraw-rewards $MANDE_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$MANDE_CHAIN_ID
```

#### Manajemen validator

Edit validator

```
mande-chaind tx staking edit-validator \
    --identity="keybase identity"
    --security-contact="XXXXXXXX" \
    --website="XXXXXXXX"
```

Unjail validator

```
mande-chaind tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$MANDE_CHAIN_ID \
  --gas=auto
```

#### Delete node

Perintah ini akan sepenuhnya menghapus node dari server. Gunakan dengan risiko Anda sendiri!

```
sudo systemctl stop mande-chaind && \
sudo systemctl disable mande-chaind && \
rm /etc/systemd/system/mande-chaind.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .mande-chain && \
rm -rf $(which mande-chaind)
```
