# Source

[Join our telegram <img src="https://user-images.githubusercontent.com/50621007/183283867-56b4d69f-bc6e-4939-b00a-72aa019d1aea.png" alt="" data-size="line">](https://t.me/BeritaCryptoo) [Follow our twitter <img src="https://user-images.githubusercontent.com/108946833/184274157-08210464-fa03-493d-b01c-2420c67a524f.jpg" alt="" data-size="line">](https://twitter.com/BeritaCryptoo)

![](https://user-images.githubusercontent.com/108946833/188787002-658bf009-a004-447d-979d-cf57e07b1ba1.jpg)

## Source Testnet

| Komponen    | Persyaratan Minimum                |
| ----------- | ---------------------------------- |
| CPU         | 4 or more physical CPU cores       |
| RAM         | At least 8GB of memory (RAM)       |
| Penyimpanan | At least 160GB of SSD disk storage |
| koneksi     | At least 100mbps network bandwidth |

### Perangkat Lunak

| Komponen | Persyaratan Minimum |
| -------- | ------------------- |
| OS       | Ubuntu 20.04        |

### Install Otomatis

```
wget -O source.sh https://raw.githubusercontent.com/xsons/TestnetNode/main/Source/source.sh && chmod +x source.sh && ./source.sh
```

Opsi 2 (manual)

Anda dapat mengikuti [panduan manual](https://github.com/xsons/TestnetNode/blob/main/Source/Manual.md) jika Anda lebih suka mengatur node secara manual

### Pasca Instalasi

Selanjutnya Anda harus memastikan validator Anda menyinkronkan blok. Anda dapat menggunakan perintah di bawah ini untuk memeriksa status sinkronisasi

```
sourced status 2>&1 | jq .SyncInfo
```

## [Install Snapshoot](https://github.com/xsons/testnet\_node/edit/main/Source/stateSync-snapshot.md)

### Membuat Wallet

Untuk membuat dompet baru Anda dapat menggunakan perintah di bawah ini. Jangan lupa simpan mnemonicnya

```
sourced keys add wallet
```

(OPSIONAL) Untuk memulihkan dompet Anda menggunakan frase seed

```
sourced keys add wallet --recover
```

Untuk mendapatkan daftar dompet saat ini

```
sourced keys list
```

### Simpan info dompet

Tambahkan alamat dompet dan valoper dan muat variabel ke dalam sistem

```
SRC_WALLET_ADDRESS=$(sourced keys show wallet -a)
SRC_VALOPER_ADDRESS=$(sourced keys show wallet --bech val -a)
echo 'export SRC_WALLET_ADDRESS='${SRC_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export SRC_VALOPER_ADDRESS='${SRC_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

Dapatkan Token Faucet Di Discord

> * [Discord Official Source](https://discord.gg/MgcfAgrD)

Untuk memeriksa saldo dompet Anda:

```
sourced query bank balances $SRC_WALLET_ADDRESS
```

#### Buat Validator

```
sourced tx staking create-validator \
--amount=1000000usource \
--pubkey=$(sourced tendermint show-validator) \
--moniker=<moniker> \
--chain-id=sourcechain-testnet \
--commission-rate="0.10" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.1" \
--min-self-delegation="1" \
--fees=100usource \
--from=<walletName> \
--identity="" \
--website="" \
--details="" \
-y
```

### Perintah yang berguna

#### Manajemen Pelayanan

Periksa log

```
journalctl -fu sourced -o cat
```

Memulai layanan

```
sudo systemctl start sourced
```

Hentikan layanan

```
sudo systemctl stop sourced
```

Mulai ulang layanan

```
sudo systemctl restart sourced
```

### Informasi simpul

Informasi sinkronisasi

```
sourced status 2>&1 | jq .SyncInfo
```

Info validator

```
seid status 2>&1 | jq .ValidatorInfo
```

Informasi simpul

```
sourced status 2>&1 | jq .NodeInfo
```

Tampilkan id simpul

```
sourced tendermint show-node-id
```

#### Operasi dompet

Daftar dompet

```
sourced keys list
```

Pulihkan dompet

```
sourced keys add $SRC_WALLET --recover
```

Hapus dompet

```
sourced keys delete $SRC_WALLET
```

Cadangan Kunci Pribadi

```
sourced keys export $SRC_WALLET --unarmored-hex --unsafe
```

Dapatkan saldo dompet

```
sourced query bank balances $SRC_WALLET_ADDRESS
```

Transfer dana

```
sourced tx bank send $SRC_WALLET_ADDRESS <TO_WALLET_ADDRESS> 10000000usource
```

Pemungutan suara

```
sourced tx gov vote 1 yes --from $SRC_WALLET --chain-id=$SRC_ID
```

#### Staking, Delegasi, dan Hadiah

Delegasikan saham

```
sourced tx staking delegate $SRC_VALOPER_ADDRESS 10000000usource --from=$SRC_WALLET --chain-id=$SRC_ID --gas=auto --fees 250usource
```

Delegasikan ulang stake dari validator ke validator lain

```
sourced tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000usource --from=$SRC_WALLET --chain-id=$SRC_ID --gas=auto --fees 250usource
```

Tarik semua hadiah

```
sourced tx distribution withdraw-all-rewards --from=$SRC_WALLET --chain-id=$SRC_ID --gas=auto --fees 250usource
```

Tarik hadiah dengan komisi

```
sourced tx distribution withdraw-rewards $SRC_VALOPER_ADDRESS --from=$SRC_WALLET --commission --chain-id=$SRC_ID
```

#### Manajemen Validator

Edit validator

```
sourced tx staking edit-validator \
--moniker=NEWNODENAME \
--chain-id=$SRC_ID \
--from=$SRC_WALLET
```

Unjail validator

```
sourced tx slashing unjail \
  --broadcast-mode=block \
  --from=$SRC_WALLET \
  --chain-id=$SRC_ID \
  --gas=auto --fees 250usource
```

### Hapus node

Hapus node Perintah ini akan menghapus node sepenuhnya dari server. Gunakan dengan risiko Anda sendiri!

```
sudo systemctl stop sourced
sudo systemctl disable sourced
sudo rm /etc/systemd/system/sourced* -rf
sudo rm $(which sourced) -rf
sudo rm $HOME/.source* -rf
sudo rm $HOME/source -rf
sed -i '/SRC_/d' ~/.bash_profile
```
