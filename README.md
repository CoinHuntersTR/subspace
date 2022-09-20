# <h1 align="center">Subspace Network Node Kurulumu

  ## Bu rehberde Subspace için Gemini II ödüllü testnetine katılıyoruz. Sorularınız için: [Pusula Finans](https://t.me/pusulafinans)
  
 ![image](https://user-images.githubusercontent.com/107190154/191179355-ac1b6ff1-095b-4937-8f2c-8578c0774345.gif)

### Sistem Gereksinimleri 

|CPU | RAM  | Disk  | 
|----|------|----------|
|   4| 4GB(8)  | 30GB     |
  
  
 
  # Daha önce Node kurulumu yapmadıysanız buradan sırasıyla adımları takip ederek tüm süreci öğrenebilirsiniz.
  ## Yeni Başladım Rehberi; [Pusula Finans Labs.](https://www.labs.pusulafinans.com/category/rehber/)
  ### 1. [Testnet ve Node test kurulum rehberi Bölüm-1](https://www.labs.pusulafinans.com/2022/08/23/testnet-ve-node-kurulum-rehberi/)
  ### 2. [Yeni Chrome Tarayıcı nasıl açarım?](https://www.labs.pusulafinans.com/2022/08/23/yeni-chrome-tarayici-nasil-acarim/)
  ### 3. [Ücretsiz Sunucu Kiralama](https://www.labs.pusulafinans.com/2022/08/23/nasil-ucretsiz-sunucu-kiralarim/)
  ### 4. [Digital Ocean Nasıl Kayıt olurum?](https://www.labs.pusulafinans.com/2022/08/23/digital-oceana-nasil-kayit-olabilirim/)
  ### 5. [MobaXTerm Terminal Kurulumu](https://www.labs.pusulafinans.com/2022/08/23/mobaxterm-terminal-kurulumu/)
  
  # NODE KURULUMU
  
  ## Sistem Güncelleme
  
  ### Root yetkisi alalım.
  ```
  sudo su
  ```
  
   ### Sistem güncellemesi yapıyoruz
  ```
  sudo apt update && sudo apt upgrade -y
  ```
  
  ## Kütüphanelerin Kurulumu
   ```
  sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony unzip liblz4-tool -y
  ```
  ## Subspace Node ve Farmer Dosyalarını indiriyoruz.
  ```
  cd $HOME
  ```
  ```
  wget -qO subspace-node-ubuntu-x86_64-gemini-2a-2022-sep-10 https://github.com/subspace/subspace/releases/download/gemini-2a-2022-sep-10/subspace-node-ubuntu-x86_64-gemini-2a-2022-sep-10
  ```
  ```
  wget -qO subspace-farmer-ubuntu-x86_64-gemini-2a-2022-sep-10 https://github.com/subspace/subspace/releases/download/gemini-2a-2022-sep-10/subspace-farmer-ubuntu-x86_64-gemini-2a-2022-sep-10
  ```
   ## Binary dosyalarına yetki veriyoruz
  ```
  sudo chmod +x subspace-node-ubuntu-x86_64-gemini-2a-2022-sep-10
  sudo chmod +x subspace-farmer-ubuntu-x86_64-gemini-2a-2022-sep-10
  ```
  
  ## Binary dosyalarımızı /usr/local/bin dizinine taşıyoruz
  ```
sudo mv subspace-node-ubuntu-x86_64-gemini-2a-2022-sep-10 /usr/local/bin/subspaceNode
sudo mv subspace-farmer-ubuntu-x86_64-gemini-2a-2022-sep-10 /usr/local/bin/subspaceFarmer
  ```  
##  Node için subspaced isimli bir servis dosyası oluşturalım; (NODEISMINIYAZ kısmına istediğiniz node isminizi yazmayı unutmayın!)
```
sudo tee <<EOF >/dev/null /etc/systemd/system/subspaced.service
[Unit]
Description=Supsapce Node
After=network.target

[Service]
User=$USER
ExecStart=$(which subspaceNode) --chain gemini-2a --execution wasm --state-pruning archive --validator --name NODEISMINIYAZ
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
  ``` 
  
 ## Node servislerini başlatalım
  ```
  sudo systemctl daemon-reload
  ```
  ```
  sudo systemctl enable subspaced
  ```
  ```
  sudo systemctl restart subspaced
  ```
# [BURADAN](https://chrome.google.com/webstore/detail/polkadot%7Bjs%7D-extension/mopnmbcafieddcagagdcbnhejhlodfdd) Polkadotjs cüzdan indiriyoruz.

[Buradan](https://www.pusulafinans.com/polkadot-cuzdan-nasil-olusturulur/) Detaylı Kurulum Rehberine ulaşabilirsiniz.

### Testnete katılmak için gerekli cüzdan adresini; cüzdanımıza bu ayarları yaptıktan sonra, gelen adresi kopyalıyoruz.

![subspace](https://user-images.githubusercontent.com/111747226/191375431-d800f3ac-a361-4ac3-8416-518cf43cc652.png)

  ## Farmer için farmerd isimli bir servis oluşturalım;
  
  CUZDANADRESI kısmına ödül almak istediğiniz cüzdan adresini giriyoruz.

  ```
  sudo tee <<EOF >/dev/null /etc/systemd/system/farmerd.service
[Unit]
Description=Supsapce Node
After=network.target

[Service]
User=$USER
ExecStart=$(which subspaceFarmer) farm --reward-address CUZDANADRESI --plot-size 100GB
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
  ```
 ## Farmer servislerini başlatalım
  ```
  sudo systemctl daemon-reload
  ```
  ```
  sudo systemctl enable farmerd
  ```
  ```
  sudo systemctl restart farmerd
  ```  
## Node loglarımıza bakıyoruz.(Aşağıdaki görsel gibi logların akması gerekiyor.)
  ```
 journalctl -u subspaced -f -o cat
  ```  

![supspace2](https://user-images.githubusercontent.com/111747226/191376523-09d78401-83d8-46d1-9344-924344208f73.png)
  
## Sync kontrol etmek için 
  ### isSynincg:false çıktısı almanız lazım
  ```
 curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "system_health", "params":[]}' http://localhost:9933
  ```   
![subspace 3](https://user-images.githubusercontent.com/111747226/191377150-74d43064-261d-4bf4-bb70-8bd8b6cf298b.png)
  
 Subspace Network Linkler;
  
### [Discord](https://discord.gg/hxYrA3VQ)  [Twitter](https://twitter.com/NetworkSubspace)  [Web](https://subspace.network/)
 
 
