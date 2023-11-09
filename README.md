<p style="font-size:14px" align="right">
<a href="https://t.me/bangpateng_airdrop" target="_blank">Join our telegram <img src="https://user-images.githubusercontent.com/50621007/183283867-56b4d69f-bc6e-4939-b00a-72aa019d1aea.png" width="30"/></a>

<p align="center">
  <img height="auto" height="auto" src="https://github.com/bangpateng/gitopia/assets/38981255/cd1cad4a-4cec-4bd5-9377-8f063331d1bc">
</p>

#  MANTRA Chain Incentivized Testnet (Phase 1)

##### Dokumentasi
> - [Dokumentasi](https://docs.mantrachain.io/operate-a-node/initial-setup)
> - [Explorer](https://testnet.itrocket.net/mantra/staking)


###  Persyaratan Perangkat Keras Minimum
- Ubuntu 20.04 LTS
- CPU: 2vCPU (1 core)
- Memory: 4 GB
- Storage: 200 - 500 GB

###  Persyaratan Perangkat Keras yang Direkomendasikan
- CPU: 4vCPU (2 cores)
- Memory: 8 - 16GB
- Storage: 500 - 1000 GB

## 1. Instal Otomatis

```
wget -O mantra.sh https://raw.githubusercontent.com/bangpateng/mantra-testnet-phase-1/main/mantra.sh && chmod +x mantra.sh && ./mantra.sh
```

## 2. Check Log Sinkron

```
journalctl -fu mantrachaind -o cat
```

Biarkan Hingga Selesai Sinkron 15 Menitan Dah

## 3. Check Status Node

```
mantrachaind status 2>&1 | jq .SyncInfo
```

Jika Status Sudah `FALSE` Next Step Gawein Command Selanjutnya


## 4. Load Sistem

```
source $HOME/.bash_profile
```

## 5. Buat Dompet/Wallet

Jangan Lupa Simpan Address & Mnemonic Kalian (Enter Keyring Phase Buat Password bebas)

```
mantrachaind keys add $WALLET
```

(OPTIONAL/BISA SKIP) Untuk memulihkan dompet Anda menggunakan frase seed

```
mantrachaind keys add $WALLET --recover
```

## 6. Simpan Info Wallet

```
MANTRA_WALLET_ADDRESS=$(mantrachaind keys show $WALLET -a)
MANTRA_VALOPER_ADDRESS=$(mantrachaind keys show $WALLET --bech val -a)
echo 'export MANTRA_WALLET_ADDRESS='${MANTRA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export MANTRA_VALOPER_ADDRESS='${MANTRA_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

Enter Keyring Phase Buat Password = Masukan Pass Yang Sebelumnya Udah di Buat

## 7. Claim Faucet 

- Open Link : https://faucet.testnet.mantrachain.io/
- Paste Address Kalian
- Done

## 8. Check Balance 

```
mantrachaind q bank balances $MANTRA_WALLET_ADDRESS
```

Kalau Balance Sudah Masuk, Baru Buat Validator

## 9. Mambuat Validator

```
mantrachaind tx staking create-validator \
  --amount 1000000uaum \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.10" \
  --min-self-delegation "1" \
  --pubkey  $(mantrachaind tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $MANTRA_CHAIN_ID \
  --gas auto \
  --gas-adjustment 1.5 \
  --fees 50uaum \
  -y
```

## 10. Update Foto Profil, Deskripsi Validator

```
mantrachaind tx staking edit-validator \
--new-moniker=$NODENAME \
--identity=<your_keybase_id> \
--website="<your_website>" \
--details="<your_validator_description>" \
--chain-id=$MANTRA_CHAIN_ID \
--from=$WALLET
```

- <your_keybase_id> Ganti Dengan ID Keybase Kalian
- <your_website> Ganti Dengan Link Twitter atau Website Kalian
- <your_validator_description> Ganti Dengan Deskripsi Node Kalian

## 11. Hapus Node

```
sudo systemctl stop mantrachaind
sudo systemctl disable mantrachaind
sudo rm /etc/systemd/system/mantrachaind.service
sudo systemctl daemon-reload
rm -f $(which mantrachaind)
rm -rf .mantrachain
rm -rf mantrachaind
```

## Command Berguna Lainnya

#### Delegate

```
mantrachaind tx staking delegate $VALOPER_ADDRESS 10000000uaum --from $WALLET --chain-id $MANTRA_CHAIN_ID --fees 5000uaum
```

#### Redelegate

```
mantrachaind tx staking redelegate $VALOPER_ADDRESS <dst-validator-operator-addr> 100000000uaum --from=$WALLET --chain-id=$MANTRA_CHAIN_ID
```

#### Withdraw Reward

```
mantrachaind tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$MANTRA_CHAIN_ID
```

#### Unjail

```
mantrachaind tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$MANTRA_CHAIN_ID \
  --gas=auto \
  --gas-adjustment=1.4
```



