# Newrl

[Join Our Telegram](https://t.me/BeritaCryptoo) <img src=".gitbook/assets/Tele.png" alt="" data-size="line"> [Follow Our Twitter](https://twitter.com/BeritaCryptoo) <img src=".gitbook/assets/512x512-logo-27152.png" alt="" data-size="line">

<figure><img src=".gitbook/assets/NEWWRL.png" alt=""><figcaption></figcaption></figure>

## Newrl Testnet Guide

****[**Official Guide**](https://docs.newrl.net/Validating/running-validator-node/)****

#### Mininum Requirements

|  Storage | RAM | CPU |
| :------: | :-: | :-: |
| 50GB SSD | 2GB |  2  |

### Setup

**Open Port**

```
sudo ufw allow ssh && sudo ufw allow 8421 && sudo ufw enable -y
```

**Update Packages**

```
sudo apt update && sudo apt upgrade -y
```

**Install Hak & Kewajiban**

```
sudo apt install -y build-essential libssl-dev libffi-dev git curl screen
```

**Install Python**

```
sudo apt install python3.9
```

**Install Pip & Python3 venv**

```
curl -sSL https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3 get-pip.py
sudo apt install python3.9-venv
```

**Aktifasi**

```
source newrl-venv/bin/activate
```

**Download dan Mulai Script**

```
git clone https://github.com/newrlfoundation/newrl.git
cd newrl
scripts/install.sh testnet
```

Buat Wallet Baru

```
python3 scripts/show_wallet.py
```
