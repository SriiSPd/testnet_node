# PointNetwork

[Join our telegram ![](https://user-images.githubusercontent.com/50621007/183283867-56b4d69f-bc6e-4939-b00a-72aa019d1aea.png)](https://t.me/BeritaCryptoo) [Join our twitter ![](https://user-images.githubusercontent.com/108946833/184274157-08210464-fa03-493d-b01c-2420c67a524f.jpg)](https://twitter.com/BeritaCryptoo)

![](https://user-images.githubusercontent.com/108946833/185566136-53e35398-2c9c-4eb3-99af-b93d150ab885.jpg)

## Join Point-XNet-Triton as a Validator

### Perangkat Keras

| Komponen    | Persyaratan Minimum                |
| ----------- | ---------------------------------- |
| CPU         | 4 or more physical CPU cores       |
| RAM         | At least 32GB of memory (RAM)      |
| Penyimpanan | At least 500GB of SSD disk storage |
| koneksi     | At least 100mbps network bandwidth |

### Perangkat Lunak

| Komponen | Persyaratan Minimum            |
| -------- | ------------------------------ |
| OS       | Ubuntu 18.04 atau lebih tinggi |

Sebelum Memulai jalanin NODE, kalian harus mempunyai dulu Faucet, dan isi form untuk melanjutkan ketahap berikutnya, dan add RPC XPOINT Ke metamask

* https://pointnetwork.io/testnet-form

```
Network Title: Point XNet Triton
RPC URL: https://xnet-triton-1.point.space/
Chain ID: 10721
SYMBOL: XPOINT
```

### Install Point Validator

Gunakan skrip di bawah ini untuk instalasi cepat

```
wget -O validator.sh https://raw.githubusercontent.com/xsons/TestnetNode/main/PointNetwork/validator.sh && chmod +x validator.sh && ./validator.sh
```

Anda dapat mengikuti [panduan manual](https://github.com/xsons/TestnetNode/blob/main/PointNetwork/Validator.md) jika Anda lebih suka mengatur node secara manual

### Setelah Beres Instalasi

```
source $HOME/.bash_profile
```

### Periksa info Sinkronisasi

```
evmosd status 2>&1 | jq .SyncInfo
```

### Membuat Wallet

```
evmosd keys add validatorkey --keyring-backend file
```

* Jika address faucet kalian ingin di pakai silahkan gunakan cara ini:

```
evmosd keys add validatorkey --recover
```

Untuk mendapatkan kunci pribadi dompet validator

```
evmosd keys unsafe-export-eth-key validatorkey --keyring-backend file
```

Rubah `validatorkey` dengan nama `validator` kalian

Untuk mengetahui dompet saat ini

```
evmosd keys list
```

### Simpan Wallet info

```
EVMOS_WALLET_ADDRESS=$(evmosd keys show $WALLET -a)
```

Masukan Pharse/password

```
EVMOS_VALOPER_ADDRESS=$(evmosd keys show $WALLET --bech val -a)
```

Masukan Pharse/password

```
echo 'export EVMOSD_WALLET_ADDRESS='${EVMOSD_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export EVMOSD_VALOPER_ADDRESS='${EVMOSD_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Check Saldo

```
evmosd query bank balances evmos1
```

Ganti `evmos1` dengan address kalian

### Membuat Validator

```
evmosd tx staking create-validator \
--amount=1000000000000000000000apoint \
--pubkey=$(evmosd tendermint show-validator) \
--moniker="<myvalidator>" \
--chain-id=point_10721-1 \
--commission-rate="0.10" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.01" \
--min-self-delegation="1000000000000000000000" \
--gas="400000" \
--gas-prices="0.025apoint" \
--from=validatorkey \
--keyring-backend file
```

Rubah `<myvalidator>` dengan nama validator kalian, `from=validatorkey` dengan address evmos

Untuk memeriksa txhash bisa menggunakan cara berikut:

```
evmosd query tx <txhash>
```

`txhash` ganti dengan output yang di hasilkan

Untuk Sekarang belum bisa cek di website apakah validator kalian aktif atau tidak, yang bisa di lihat `voting power` jika lebih besar dari 0 maka validator kalian sudah terdaftar, untuk melihat nya:

```
evmosd status
```

![Screenshot\_65](https://user-images.githubusercontent.com/108946833/185779191-4e3c516d-4f41-4433-bfba-8c20e804596c.png)

### Claim reward hasil validator

```
evmosd tx distribution withdraw-rewards VALOPER_ADDRESS-KALIAN --from=$WALLET --commission --chain-id=$EVMOS_CHAIN_ID
```

```
evmosd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$EVMOS_CHAIN_ID --gas=auto
```

Untuk mengetahui address VALOPER

```
evmosd debug addr <address evmos>
```

### Perintah yang Berguna

Delegation

```
evmosd tx staking delegate $(evmosd tendermint show-address) <ammount>apoint --chain-id=point_10721-1 --from=<evmosvaloper> --gas=400000 --gas-prices=0.025apoint 
```

Periksa Log

```
journalctl -fu evmosd -o cat
```

Memulai layanan

```
sudo systemctl start evmosd
```

Hentikan Layanan

```
sudo systemctl stop evmosd
```

Mulai Ulang Layanan

```
sudo systemctl restart evmosd
```

### Node Info

Info Validator

```
evmosd status 2>&1 | jq .ValidatorInfo
```

Info simpul

```
evmosd status 2>&1 | jq .NodeInfo
```

### Explorer

https://explorer-xnet-triton.point.space/

### Menghapus Node

```
sudo systemctl stop evmosd
sudo systemctl disable evmosd
sudo rm /etc/systemd/system/evmos* -rf
sudo rm $(which evmosd) -rf
sudo rm $HOME/.evmosd -rf
sudo rm $HOME/point-chain -rf
```
